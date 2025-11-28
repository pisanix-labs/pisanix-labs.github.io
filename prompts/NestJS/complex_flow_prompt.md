# Prompt para Implementação de Fluxos Complexos (Multi-Módulos)

Você é um Arquiteto de Software Especialista em Sistemas Distribuídos e NestJS. Sua tarefa é desenhar e implementar um fluxo de negócio complexo que orquestra operações entre múltiplos módulos independentes.

## Cenário de Exemplo: Faturamento de Pedido
O fluxo deve processar o faturamento de um pedido, o que implica:
1.  **Módulo Pedidos**: Validar o pedido e atualizar status.
2.  **Módulo Estoque**: Baixar a quantidade dos itens.
3.  **Módulo Financeiro**: Gerar o contas a receber.

## Diretrizes de Arquitetura

### 1. Padrão de Orquestração (Facade Service)
Não acople os módulos circularmente. Crie um **Service Orquestrador** (ex: `OrderBillingService`) que reside no módulo onde o fluxo começa (ex: `Orders`) ou num módulo dedicado a processos de negócio (ex: `SalesProcess`).

Este serviço deve:
-   Receber a requisição.
-   Chamar os métodos expostos pelos outros módulos.
-   Gerenciar a transação atômica.

### 2. Comunicação entre Módulos
-   **Síncrona (Recomendado para este caso)**: Injete os Services exportados (`InventoryService`, `FinanceService`) no `OrderBillingService`.
    -   *Regra*: Os módulos `Inventory` e `Finance` devem exportar apenas o necessário.
-   **Assíncrona (Eventos)**: Use apenas para efeitos colaterais que **não** impedem o sucesso do fluxo principal (ex: enviar email, notificar analytics). Se a baixa de estoque falhar, o faturamento NÃO pode ocorrer, logo, deve ser síncrono/transacional.

### 3. Transacionalidade (Atomicidade)
O fluxo deve ser "Tudo ou Nada". Se falhar ao gerar o contas a receber, o estoque deve ser estornado e o pedido não pode ser finalizado.
-   Utilize o gerenciador de transações do seu ORM (ex: `QueryRunner` no TypeORM ou `Prisma.$transaction`).
-   Garanta que todas as operações de escrita recebam o contexto da transação.

## Passo a Passo para Implementação

### Passo 1: Preparar os Módulos Satélites
Garanta que os módulos dependentes exportem os métodos necessários.
-   **InventoryModule**: Deve exportar um `InventoryService` com método `decreaseStock(productId, quantity, transactionContext)`.
-   **FinanceModule**: Deve exportar um `FinanceService` com método `createReceivable(orderData, transactionContext)`.

### Passo 2: Criar o DTO de Entrada
Defina o que é necessário para disparar o fluxo.
```typescript
export class BillOrderDto {
  @IsUUID()
  orderId: string;
  // Outros dados como método de pagamento, se necessário
}
```

### Passo 3: Implementar o Orquestrador (`OrderBillingService`)
```typescript
import { EventEmitter2 } from '@nestjs/event-emitter';

@Injectable()
export class OrderBillingService {
  constructor(
    private ordersRepo: OrdersRepository,
    private inventoryService: InventoryService, // Importado de InventoryModule
    private financeService: FinanceService,     // Importado de FinanceModule
    private dataSource: DataSource,             // Para gerenciar transação
    private eventEmitter: EventEmitter2,        // Para eventos assíncronos
  ) {}

  async execute(dto: BillOrderDto) {
    const queryRunner = this.dataSource.createQueryRunner();
    await queryRunner.connect();
    await queryRunner.startTransaction();

    try {
      // 1. Buscar e Validar Pedido
      const order = await this.ordersRepo.findById(dto.orderId);
      if (order.status !== 'PENDING') throw new BadRequestException('Pedido já processado');

      // 2. Baixar Estoque (Iterar itens)
      for (const item of order.items) {
        await this.inventoryService.decreaseStock(item.productId, item.quantity, queryRunner.manager);
      }

      // 3. Gerar Contas a Receber
      await this.financeService.createReceivable({
        amount: order.total,
        customerId: order.customerId
      }, queryRunner.manager);

      // 4. Atualizar Pedido
      order.status = 'BILLED';
      await this.ordersRepo.save(order, queryRunner.manager);

      // Commit da Transação
      await queryRunner.commitTransaction();

      // 5. Disparar Eventos Assíncronos (Side Effects)
      // Só disparamos se a transação foi comitada com sucesso.
      this.eventEmitter.emit('order.billed', new OrderBilledEvent(order));

      return order;

    } catch (err) {
      // Rollback em caso de qualquer erro
      await queryRunner.rollbackTransaction();
      throw err;
    } finally {
      await queryRunner.release();
    }
  }
}
```

### Passo 4: Expor no Controller
Crie um endpoint específico para essa ação, ex: `POST /orders/:id/bill`. Evite colocar lógica complexa dentro do Controller.

### Passo 5: Lidar com Eventos Assíncronos (Email)
Crie um Listener em um módulo apropriado (ex: `NotificationsModule`) para reagir ao evento sem bloquear o fluxo principal.

```typescript
@Injectable()
export class OrderNotificationListener {
  @OnEvent('order.billed', { async: true })
  async handleOrderBilledEvent(payload: OrderBilledEvent) {
    // Envia email, notifica Slack, etc.
    // Se falhar aqui, o pedido JÁ ESTÁ faturado (não afeta a consistência do negócio principal).
    await this.emailService.sendBillingConfirmation(payload.order.email);
  }
}
```

---
**Resumo das Regras:**
1.  Use **Transações** para garantir consistência (Tudo ou Nada).
2.  Use **Injeção de Dependência** para comunicação síncrona crítica (Estoque, Financeiro).
3.  Use **Eventos Assíncronos** para side effects não-críticos (Email, Logs).
4.  Dispare eventos apenas **APÓS** o commit da transação.

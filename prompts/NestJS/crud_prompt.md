# Prompt para Geração de CRUD Complexo

Você é um Especialista em NestJS e TypeORM. Sua tarefa é gerar todo o código necessário para um módulo CRUD completo, baseado na definição de entidade fornecida abaixo.

## 1. Definição da Entidade (Input)
O usuário fornecerá a estrutura da entidade em formato TypeScript/JSON.
**Analise cuidadosamente:**
-   **Propriedades Primitivas**: (`string`, `number`, `boolean`, `Date`).
-   **Relacionamentos**: (`OneToMany`, `ManyToOne`, `OneToOne`).
-   **Entidades Filhas**: Itens que devem ser criados/atualizados junto com o pai (Cascade).

## 2. Regras de Geração

### A. Entidades e DTOs
1.  **Entidade (`.entity.ts`)**:
    -   Use decorators do TypeORM (`@Entity`, `@Column`, `@OneToMany`, etc.).
    -   Para relacionamentos, use `cascade: true` se fizer sentido (ex: Itens de um Pedido).
2.  **DTO de Criação (`create-x.dto.ts`)**:
    -   Use `class-validator` para validação rigorosa.
    -   Para entidades filhas, use `@ValidateNested()` e `@Type(() => ChildDto)`.
    -   Documente tudo com `@ApiProperty()`.
3.  **DTO de Atualização (`update-x.dto.ts`)**:
    -   Estenda `PartialType(CreateXDto)`.

### B. Camada de Dados (Repository)
1.  **Interface (`x.repository.ts`)**: Defina os métodos padrão (`create`, `findAll`, `findOne`, `update`, `remove`).
2.  **Implementação**: Crie a classe concreta usando `DataSource` ou `Repository` do TypeORM.

### C. Camada de Serviço (Service)
1.  Implemente a lógica de negócio.
2.  Se houver entidades filhas, garanta que elas sejam salvas corretamente.
3.  Lance `NotFoundException` se o ID não existir na busca/atualização.

### D. Camada de Controle (Controller)
1.  Exponha os endpoints REST padrão:
    -   `POST /`
    -   `GET /` (com paginação opcional)
    -   `GET /:id`
    -   `PATCH /:id`
    -   `DELETE /:id`
2.  Use decorators do Swagger (`@ApiOperation`, `@ApiResponse`) em todos os métodos.

## 3. Testes

Crie testes unitários e de integração (quando houver) para todos os módulos gerados.
Deixe evidente as validações de entrada e saída.
Utilize estratégia de mock para não depender de banco de dados.

## Atualização dos novos módulos no módulo raiz do projeto

Atualize o arquivo `app.module.ts` para incluir os novos módulos.

---

## Modelo de Input (Copie e Preencha)

Segue definição das entidades para geração dos CRUD:

```typescript
Entity Customer {
  name: string;
  email: string;
  phone: string;
  document: string;
  vehicles: Vehicle[];
}

Entity Vehicle {
    factory: string;
    model: string;
    year: number;
    licensePlate: string;
    customer: Customer;
}

Entity Service {
    name: string;
    price: number;
}

Entity Product {
    name: string;
    price: number;
    stock: number;
}

Enum OrderStatus {
    Recebida,
    EmDiagnostico,
    AguardandoAprovacao,
    EmExecucao,
    Finalizada,
    Entregue
}

Entity Order {
  customer: Customer;
  vehicle: Vehicle;
  services: Service[];
  products: Product[];
  totalPrice: number;
  status: OrderStatus;
}

```

---

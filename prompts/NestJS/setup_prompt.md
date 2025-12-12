# Prompt de Setup de Projeto NestJS

Você é um Especialista em DevOps e Arquitetura NestJS. Sua tarefa é inicializar um novo projeto NestJS configurado exatamente como o nosso padrão corporativo.

Siga os passos abaixo sequencialmente para garantir que o ambiente esteja pronto para o desenvolvimento.

## 1. Pré-requisitos
- **Node.js**: Versão 20 ou superior (obrigatório para NestJS v11).

## 2. Inicialização do Projeto
Execute o comando para criar um novo projeto NestJS (substitua `nome-do-projeto` pelo nome desejado):
```bash
npx @nestjs/cli new nome-do-projeto --package-manager yarn
```

## 3. Instalação de Dependências
Instale as bibliotecas essenciais para validação, documentação e utilitários:

```bash
yarn add @nestjs/swagger class-validator class-transformer reflect-metadata rxjs
yarn add -D @types/express
```

*Nota: `@nestjs/core`, `@nestjs/common`, `@nestjs/platform-express` já vêm instalados por padrão.*

## 4. Configuração do `main.ts`
Substitua o conteúdo do `src/main.ts` pelo seguinte código, que habilita:
- **CORS**: Para permitir requisições de frontends.
- **ValidationPipe Global**: Com `whitelist: true` e `transform: true`.
- **Swagger**: Disponível na rota `/swagger`.
- **Filtro Global de Exceções**: Prepara o hook para o filtro que criaremos a seguir.

```typescript
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';
import { ValidationPipe } from '@nestjs/common';
import { DocumentBuilder, SwaggerModule } from '@nestjs/swagger';
// import { AllExceptionsFilter } from './common/filters/all-exceptions.filter'; // Descomente após criar o arquivo

async function bootstrap() {
  const app = await NestFactory.create(AppModule);

  app.enableCors();

  app.useGlobalPipes(
    new ValidationPipe({
      whitelist: true,
      transform: true,
      transformOptions: { enableImplicitConversion: false },
      forbidUnknownValues: false,
    }),
  );

  // app.useGlobalFilters(new AllExceptionsFilter()); // Descomente após criar o arquivo

  const config = new DocumentBuilder()
    .setTitle('API do Projeto')
    .setDescription('Documentação da API')
    .setVersion('1.0.0')
    .build();
  const document = SwaggerModule.createDocument(app, config);
  SwaggerModule.setup('swagger', app, document);

  await app.listen(process.env.PORT ?? 3000);
}
bootstrap();
```

## 5. Estrutura de Pastas Base
Crie a seguinte estrutura de diretórios dentro de `src/` para manter a organização modular:

```bash
mkdir -p src/common/filters
```

## 6. Criação do Filtro Global de Exceções
Crie o arquivo `src/common/filters/all-exceptions.filter.ts` com o seguinte conteúdo padrão (com tipagem estrita para Express):

```typescript
import { ArgumentsHost, Catch, ExceptionFilter, HttpException, HttpStatus, Logger } from '@nestjs/common';
import { Request, Response } from 'express';

@Catch()
export class AllExceptionsFilter implements ExceptionFilter {
  private readonly logger = new Logger(AllExceptionsFilter.name);

  catch(exception: unknown, host: ArgumentsHost) {
    const ctx = host.switchToHttp();
    const response = ctx.getResponse<Response>();
    const request = ctx.getRequest<Request>();

    const isHttp = exception instanceof HttpException;
    const status = isHttp ? exception.getStatus() : HttpStatus.INTERNAL_SERVER_ERROR;

    let message: unknown = 'Internal server error';
    if (exception instanceof HttpException) {
      message = exception.message;
      const response = exception.getResponse() as any;
      if (response && response.message && Array.isArray(response.message) && response.message.length) {
        message += ': ' + response.message.join('; ');
      }
    } else {
      message = (exception as Error).message;
    }
    this.logger.error(`Http Status: ${status} Error Message: ${JSON.stringify(message)}`);
    this.logger.error(`stack: ${JSON.stringify((exception as Error).stack)}`)

    response.status(status).json({
      statusCode: status,
      message,
      path: request.url,
      timestamp: new Date().toISOString(),
    });
  }
}
```

*Após criar este arquivo, lembre-se de descomentar as linhas referentes ao `AllExceptionsFilter` no `main.ts`.*

## 6. Configuração do TypeScript (Opcional mas Recomendado)
Verifique se o `tsconfig.json` está com `strict: true` ou pelo menos `strictNullChecks: true` para garantir maior segurança de tipos.

## 7. Data e hora
No banco de dados iremos persistir em UTC, dessa forma, no módulo raiz inicialize a seguinte variável de ambiente
```
process.env.TZ = 'UTC';
```

---
**Resultado Esperado:**
Ao final destes passos, devo ter uma aplicação rodando na porta 3000, com Swagger acessível em `/swagger`, validação de DTOs ativa e tratamento de erros padronizado.

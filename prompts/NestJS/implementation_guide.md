# Guia de Implantação do Console de Boas Práticas

Para garantir que o assistente (eu ou outro) siga as diretrizes definidas no `best_practices_prompt.md`, você pode implantar esse "console" de algumas formas, dependendo das ferramentas que utiliza.

## 1. Arquivo de Contexto do Projeto (Recomendado)
Crie um arquivo na raiz do seu projeto (como você já tem `prompts.md`) chamado `AI_RULES.md` ou `.cursorrules` (se usar o Cursor).

- **Arquivo**: `.cursorrules` ou `AI_RULES.md`
- **Conteúdo**: Copie o conteúdo de `best_practices_prompt.md` para este arquivo.
- **Uso**: A maioria dos assistentes de código modernos (Cursor, Copilot, etc.) lêem esses arquivos automaticamente para entender o contexto e as regras do projeto.

## 2. Instruções Personalizadas (Custom Instructions)
Se você utiliza o ChatGPT, Claude ou Gemini via interface web ou API:

- Vá nas configurações de "Custom Instructions" ou "System Prompt".
- Cole o texto do `best_practices_prompt.md`.
- Isso garantirá que *todas* as suas interações com aquele assistente sigam essas regras.

## 3. Prompt de Inicialização (Workflow Manual)
Sempre que iniciar uma nova task ou feature com o assistente:

1. Tenha o texto salvo em um local de fácil acesso (ex: Notion, Obsidian, ou um arquivo `docs/standards.md` no repo).
2. Inicie a conversa colando o prompt:
   > "Vou começar um novo módulo de Clientes. Por favor, leia e siga estritamente as diretrizes abaixo: [Cole o Texto Aqui]"

## 4. Validação Automática (CI/CD - Avançado)
Para garantir que as regras estão sendo seguidas mesmo se o humano ou a IA esquecerem:

- **ESLint**: Configure regras rígidas de linting.
- **Testes de Arquitetura**: Ferramentas como `dependency-cruiser` podem validar se módulos não estão importando coisas que não deveriam (ex: Service importando Controller).
- **Script de Verificação**: Um script simples que verifica se existem arquivos `.dto.ts` sem decorators `@ApiProperty` (usando regex simples).

## Resumo da Recomendação
Para o seu caso, dado que você já usa arquivos markdown no projeto (`prompts.md`), recomendo criar um arquivo **`.cursorrules`** (se usar Cursor) ou manter um **`ARCHITECTURE.md`** na raiz.

Sempre que pedir para a IA criar algo, referencie esse arquivo:
> "Crie o módulo de Pedidos seguindo as diretrizes de `ARCHITECTURE.md`."

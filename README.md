# AGI

Agente de IA em linha de comando. A conversa acontece no terminal: você escreve um pedido, o agente responde e, quando precisa, usa ferramentas para ler e alterar arquivos, executar comandos, rodar código ou buscar na web.

A interface é feita com [Ink](https://github.com/vadimdemedes/ink) (React no terminal). O modelo e as ferramentas usam o [AI SDK](https://ai-sdk.dev/) com a API da OpenAI.

## Funcionalidades

- **Chat no terminal.** Digite a mensagem e pressione Enter. O histórico mostra as suas mensagens e as respostas do assistente.
- **Ferramentas de arquivo.** O agente pode listar um diretório (`listFiles`), ler um arquivo (`readFile`), criar ou sobrescrever um arquivo (`writeFile`) e apagar um arquivo (`deleteFile`).
- **Comandos de shell.** `runCommand` executa um comando do sistema e devolve a saída e o código de saída.
- **Execução de código.** `executeCode` grava o código em um arquivo temporário e o executa. Linguagens suportadas: JavaScript (Node.js), Python (`python3`) e TypeScript (`npx tsx`).
- **Busca na web.** `webSearch` usa a busca nativa da OpenAI. A execução acontece no provedor, e o resultado volta na resposta do modelo.
- **Aprovação humana.** Antes de uma ferramenta rodar, o terminal pede confirmação. Use as setas para escolher **Yes** ou **No** e confirme com Enter. Os argumentos da chamada aparecem em uma prévia.
- **Chamadas de ferramenta visíveis.** Cada ferramenta em andamento aparece com um indicador, e o resultado concluído é mostrado em seguida.
- **Contexto da conversa.** A tela exibe o percentual de tokens usados em relação à janela do modelo. Quando o uso passa de 80% da janela, a conversa pode ser compactada em um resumo para a sessão continuar.
- **Avaliações.** Conjuntos de teste medem a escolha de ferramentas de arquivo, de shell e o comportamento em vários turnos. Os resultados são enviados ao [Laminar](https://laminar.sh/).

## Pré-requisitos

- [Node.js](https://nodejs.org/) 20 ou superior
- npm
- Uma chave de API da OpenAI
- Python 3 disponível como `python3`, se quiser que o agente execute código Python

## Instalação

Na pasta `agents-v2`:

```bash
npm install
```

Crie um arquivo `.env` na mesma pasta:

```bash
OPENAI_API_KEY=sua-chave-da-openai
```

A chave é lida automaticamente pelo SDK da OpenAI. Não commite o `.env`.

Para rodar as avaliações, inclua também a chave do projeto Laminar:

```bash
LMNR_API_KEY=sua-chave-do-laminar
```

## Iniciar

Modo normal:

```bash
npm start
```

Modo de desenvolvimento, com reinício ao salvar arquivos:

```bash
npm run dev
```

Para gerar o executável em `dist/` e usar o comando `agi`:

```bash
npm run build
npx agi
```

Encerre a sessão com `Ctrl+C`.

## Como usar

1. Inicie o agente com `npm start`.
2. No prompt `>`, escreva o pedido e pressione Enter.
3. Enquanto o modelo trabalha, o terminal mostra `Thinking...`.
4. Se o agente for usar uma ferramenta, aparece **Tool Approval Required**. Revise o nome e os argumentos, escolha **Yes** para permitir ou **No** para recusar, e pressione Enter.
5. A chamada aparece na conversa, com o resultado resumido quando termina.
6. A resposta do assistente entra no histórico. Você pode continuar a conversa no mesmo processo.
7. O indicador de tokens mostra o quanto da janela de contexto já foi usado.

Exemplos de pedidos:

```text
Liste os arquivos desta pasta e resuma o que o projeto faz.
```

```text
Leia o package.json e diga quais scripts existem.
```

```text
Crie um arquivo notas.txt com três ideias de nomes para o projeto.
```

```text
Calcule a soma dos números de 1 a 100 em JavaScript e mostre o resultado.
```

```text
Busque na web o que é compactação de contexto em agentes de IA e explique em poucas linhas.
```

Ferramentas que alteram o sistema (`writeFile`, `deleteFile` e `runCommand`) só devem ser aprovadas quando o caminho, o conteúdo ou o comando forem os esperados.

## Avaliações

Com `LMNR_API_KEY` definido no `.env`:

```bash
npm run eval:file-tools
npm run eval:shell-tools
npm run eval:agent
```

`npm run eval` executa a suíte configurada no Laminar. As avaliações de um turno conferem se o modelo escolhe a ferramenta certa. A avaliação de vários turnos confere a ordem das chamadas, se ferramentas proibidas foram evitadas e se a resposta final faz sentido para a tarefa.

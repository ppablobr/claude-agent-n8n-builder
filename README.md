# n8n Workflow Builder

Workspace assistido por IA para construir workflows n8n de forma produtiva. Combina o **Claude Code** (CLI) com o **n8n MCP Server** e **7 skills especializadas** para criar, validar e testar workflows diretamente na sua instância n8n — tudo via conversação.

## Pre-requisitos

| Requisito | Versao minima | Para que serve |
|-----------|---------------|----------------|
| **Node.js** | v18+ | Executar o servidor MCP via `npx` |
| **Claude Code** | — | CLI da Anthropic para interagir com o projeto |
| **Instancia n8n** | v1.0+ | Sua instancia n8n com acesso de admin para gerar API key |

### Instalando o Claude Code

Se voce ainda nao tem o Claude Code instalado:

```bash
npm install -g @anthropic-ai/claude-code
```

Apos instalar, autentique-se:

```bash
claude auth
```

## Setup rapido

```bash
# 1. Clone o repositorio
git clone <URL_DO_REPOSITORIO>
cd n8n-builder

# 2. Copie o template de configuracao
cp .mcp.json.example .mcp.json

# 3. Edite o .mcp.json com suas credenciais (veja secao abaixo)

# 4. Abra o projeto com Claude Code
claude
```

Ao abrir, peca ao Claude para verificar a conexao:

> "Faca um health check no meu n8n"

Se a resposta mostrar que a instancia esta conectada, esta tudo pronto.

## Configuracao do .mcp.json

Apos copiar o template, edite o arquivo `.mcp.json` na raiz do projeto:

```json
{
  "mcpServers": {
    "n8n-mcp": {
      "command": "npx",
      "args": ["n8n-mcp"],
      "env": {
        "MCP_MODE": "stdio",
        "LOG_LEVEL": "error",
        "DISABLE_CONSOLE_OUTPUT": "true",
        "N8N_API_URL": "https://SUA-INSTANCIA-N8N.com.br",
        "N8N_API_KEY": "SUA_API_KEY_AQUI",
        "WEBHOOK_SECURITY_MODE": "moderate"
      }
    }
  }
}
```

### Campos que voce precisa alterar

| Campo | O que colocar | Exemplo |
|-------|---------------|---------|
| `N8N_API_URL` | URL completa da sua instancia n8n (sem barra no final) | `https://n8n.minhaempresa.com.br` |
| `N8N_API_KEY` | API key gerada no painel do n8n | `n8n_api_abc123...` |

Os demais campos ja estao configurados e nao precisam ser alterados.

> **Importante:** O arquivo `.mcp.json` ja esta no `.gitignore`. Nunca faca commit deste arquivo — ele contem suas credenciais.

## Como gerar sua API Key no n8n

Para conectar o Claude Code a sua instancia n8n, voce precisa gerar uma API key. Siga os passos:

1. **Acesse sua instancia n8n** no navegador (ex: `https://n8n.minhaempresa.com.br`)

2. **Abra as configuracoes** clicando no icone de engrenagem no menu lateral esquerdo (ou acesse `/settings`)

3. **Navegue ate a secao API** — clique em **"API"** no menu de configuracoes

4. **Clique em "Create an API Key"** (ou "Criar API Key")

5. **Copie a key gerada** — ela sera exibida apenas uma vez. Cole no campo `N8N_API_KEY` do seu `.mcp.json`

### Requisitos de permissao

- Voce precisa ser **Owner** ou **Admin** da instancia para gerar API keys
- A API key herda as permissoes do usuario que a criou
- Se voce nao tem acesso, peca ao administrador da instancia para gerar uma key para voce

### Verificando se funcionou

Apos configurar, abra o Claude Code e peca:

> "Faca um health check no meu n8n"

Voce deve receber uma resposta confirmando a conexao com sucesso.

## Skills incluidas

O projeto inclui 7 skills especializadas que ensinam o Claude a trabalhar com n8n de forma eficiente:

| Skill | O que faz |
|-------|-----------|
| **Expression Syntax** | Escreve expressoes `{{ }}` corretamente, incluindo acesso a dados de webhook via `$json.body` |
| **MCP Tools Expert** | Guia a selecao e uso das ferramentas MCP para interagir com o n8n |
| **Workflow Patterns** | 5 padroes arquiteturais comprovados (webhook, HTTP API, banco de dados, AI agent, tarefas agendadas) |
| **Validation Expert** | Interpreta erros de validacao e identifica falsos positivos |
| **Node Configuration** | Configuracao de nodes com base na operacao selecionada, incluindo conexoes de AI |
| **Code JavaScript** | 10 padroes de producao para Code nodes em JS (formato de retorno: `[{json: {...}}]`) |
| **Code Python** | Limitacoes do Python no n8n (sem bibliotecas externas) e quando usar JS no lugar |

As skills ativam automaticamente conforme o contexto da conversa — voce nao precisa fazer nada.

## Como usar

Apos o setup, basta abrir o Claude Code na pasta do projeto e descrever o que voce precisa. Exemplos:

### Criar workflows
> "Crie um workflow que recebe um webhook com dados de pedido e envia uma notificacao no Slack"

> "Preciso de um workflow que sincroniza contatos do HubSpot com uma planilha do Google Sheets a cada hora"

### Gerenciar workflows existentes
> "Liste meus workflows ativos"

> "Valide o workflow de ID 42"

> "Desative o workflow de sincronizacao"

### Pesquisar e explorar
> "Quais nodes existem para trabalhar com o Notion?"

> "Busque templates de workflows para envio de email"

### Testar
> "Teste o workflow 15 enviando um payload de exemplo"

### Fluxo tipico

```
Voce descreve a necessidade
    → Claude pesquisa nodes e templates
        → Constroi o workflow
            → Valida a configuracao
                → Testa com dados reais
```

## Estrutura do projeto

```
n8n-builder/
├── .mcp.json.example    # Template de configuracao (copie para .mcp.json)
├── .mcp.json            # Sua configuracao local (ignorado pelo git)
├── .gitignore           # Ignora .mcp.json e node_modules
├── CLAUDE.md            # Instrucoes e padroes de qualidade para o Claude
├── README.md            # Este arquivo
├── .claude/             # Configuracoes do Claude Code
│   └── settings.local.json
└── n8n-skills/          # 7 skills especializadas
    ├── skills/          # Definicoes das skills
    ├── evaluations/     # 32 cenarios de teste
    ├── docs/            # Documentacao detalhada
    └── dist/            # Pacotes de distribuicao
```

## Boas praticas

- **Nunca faca commit do `.mcp.json`** — ele ja esta no `.gitignore`, mas fique atento
- **Sempre valide antes de ativar** — peca ao Claude para validar o workflow antes de coloca-lo em producao
- **Use nomes descritivos** — tanto para workflows ("Sync Shopify Orders to Airtable") quanto para nodes ("Filter Active Users")
- **Trate erros** — adicione caminhos de fallback para chamadas HTTP e operacoes que podem falhar
- **Um workflow por preocupacao** — mantenha workflows focados; use sub-workflows para logica complexa

## Solucao de problemas

| Problema | Solucao |
|----------|---------|
| "MCP server not connected" | Verifique se o Node.js v18+ esta instalado (`node -v`) e se o `.mcp.json` existe |
| "Authentication failed" | Confira a URL e API key no `.mcp.json`. Gere uma nova key se necessario |
| "Permission denied" | Sua API key pode nao ter permissoes suficientes. Peca ao admin uma key com acesso completo |
| Health check falha | Verifique se a URL do n8n esta acessivel no navegador e nao tem barra `/` no final |

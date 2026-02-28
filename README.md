<div align="center">

# n8n Workflow Builder

![n8n](https://img.shields.io/badge/n8n-v1.0+-00C853?style=for-the-badge&logo=n8n&logoColor=white)
![Node.js](https://img.shields.io/badge/Node.js-v18+-339933?style=for-the-badge&logo=node.js&logoColor=white)
![Claude Code](https://img.shields.io/badge/Claude_Code-CLI-7C3AED?style=for-the-badge&logo=anthropic&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-2196F3?style=for-the-badge)
![Skills](https://img.shields.io/badge/Skills-7_incluidas-FF9800?style=for-the-badge)

*Workspace assistido por IA para construir workflows n8n de forma produtiva.*
*Combina o **Claude Code** com o **n8n MCP Server** e **7 skills especializadas** para criar, validar e testar workflows diretamente na sua instancia n8n — tudo via conversacao.*

</div>

---

## 📋 Pre-requisitos

| Requisito | Versao minima | Para que serve |
|-----------|:-------------:|----------------|
| **Node.js** | `v18+` | Executar o servidor MCP via `npx` |
| **Claude Code** | — | CLI da Anthropic para interagir com o projeto |
| **Instancia n8n** | `v1.0+` | Sua instancia n8n com acesso de admin para gerar API key |

### Instalando o Claude Code

Se voce ainda nao tem o Claude Code instalado:

```bash
npm install -g @anthropic-ai/claude-code
```

Apos instalar, autentique-se:

```bash
claude auth
```

---

## 🚀 Setup rapido

```bash
# 1. Clone o repositorio
git clone <URL_DO_REPOSITORIO>
cd n8n-builder

# 2. Crie o arquivo .mcp.json com suas credenciais (veja secao abaixo)

# 3. Abra o projeto com Claude Code
claude
```

Ao abrir, peca ao Claude para verificar a conexao:

> "Faca um health check no meu n8n"

> [!TIP]
> Se a resposta mostrar que a instancia esta conectada, esta tudo pronto!

---

## ⚙️ Configuracao do .mcp.json

Crie o arquivo `.mcp.json` na raiz do projeto com o seguinte conteudo:

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

> [!IMPORTANT]
> O arquivo `.mcp.json` ja esta no `.gitignore`. **Nunca faca commit deste arquivo** — ele contem suas credenciais.

---

## 🔑 Como gerar sua API Key no n8n

Para conectar o Claude Code a sua instancia n8n, voce precisa gerar uma API key:

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

> [!TIP]
> Voce deve receber uma resposta confirmando a conexao com sucesso.

---

## 🧩 Skills incluidas

O projeto inclui **7 skills especializadas** que ensinam o Claude a trabalhar com n8n de forma eficiente:

| Skill | O que faz |
|:------|:----------|
| 🔤 **Expression Syntax** | Escreve expressoes `{{ }}` corretamente, incluindo acesso a dados de webhook via `$json.body` |
| 🛠️ **MCP Tools Expert** | Guia a selecao e uso das ferramentas MCP para interagir com o n8n |
| 🔀 **Workflow Patterns** | 5 padroes arquiteturais comprovados (webhook, HTTP API, banco de dados, AI agent, tarefas agendadas) |
| ✅ **Validation Expert** | Interpreta erros de validacao e identifica falsos positivos |
| ⚙️ **Node Configuration** | Configuracao de nodes com base na operacao selecionada, incluindo conexoes de AI |
| 📜 **Code JavaScript** | 10 padroes de producao para Code nodes em JS (formato de retorno: `[{json: {...}}]`) |
| 🐍 **Code Python** | Limitacoes do Python no n8n (sem bibliotecas externas) e quando usar JS no lugar |

> [!NOTE]
> As skills ficam em `.claude/skills/` e ativam automaticamente conforme o contexto da conversa — voce nao precisa fazer nada.

---

## 💡 Como usar

Apos o setup, basta abrir o Claude Code na pasta do projeto e descrever o que voce precisa:

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
📝 Descreva a necessidade
  └─➤ 🔍 Claude pesquisa nodes e templates
        └─➤ 🏗️ Constroi o workflow
              └─➤ ✅ Valida a configuracao
                    └─➤ 🧪 Testa com dados reais
```

---

## 📁 Estrutura do projeto

```
n8n-builder/
├── 🔒 .mcp.json              # Sua configuracao local (ignorado pelo git)
├── 📄 .gitignore              # Ignora .mcp.json e node_modules
├── 📘 CLAUDE.md               # Instrucoes e padroes de qualidade para o Claude
├── 📖 README.md               # Este arquivo
└── 📂 .claude/                # Configuracoes do Claude Code
    ├── settings.local.json    # Permissoes e configuracoes locais
    ├── 📂 skills/             # 7 skills especializadas
    └── 📂 evaluations/        # 32 cenarios de teste
```

### Exemplo de `.claude/settings.local.json`

Este arquivo controla as permissoes automaticas e os servidores MCP habilitados no projeto:

```json
{
  "permissions": {
    "allow": [
      "WebFetch(domain:github.com)",
      "WebFetch(domain:raw.githubusercontent.com)",
      "Bash(gh api:*)",
      "Bash(gh repo view:*)",
      "Bash(npx --version:*)",
      "Bash(npx n8n-mcp:*)",
      "Bash(git clone:*)",
      "Bash(git init:*)",
      "Bash(git remote add:*)",
      "Bash(git fetch:*)",
      "Bash(git commit:*)",
      "mcp__n8n-mcp__get_node",
      "mcp__n8n-mcp__n8n_create_workflow",
      "mcp__n8n-mcp__n8n_validate_workflow",
      "mcp__n8n-mcp__n8n_update_partial_workflow",
      "mcp__n8n-mcp__n8n_get_workflow",
      "mcp__n8n-mcp__n8n_test_workflow",
      "mcp__n8n-mcp__tools_documentation",
      "mcp__github__create_repository"
    ]
  },
  "enableAllProjectMcpServers": true,
  "enabledMcpjsonServers": [
    "n8n-mcp"
  ]
}
```

> [!TIP]
> Novas permissoes serao solicitadas conforme voce usa o Claude Code. Voce pode aprovar uma a uma ou adicionar diretamente neste arquivo.

---

## 🔐 Configuracoes de permissao

O projeto inclui `.vscode/settings.json` que permite ao Claude Code executar ferramentas automaticamente sem solicitar aprovacao:

```json
{
    "claudeCode.allowDangerouslySkipPermissions": true
}
```

Esta configuracao acelera o desenvolvimento ao eliminar prompts de confirmacao, mas remove uma camada de seguranca.

> [!WARNING]
> Esta configuracao desabilita o sistema de aprovacao de permissoes. Use apenas em ambientes de desenvolvimento confiaveis. Se preferir aprovar cada acao manualmente, remova este arquivo ou altere o valor para `false`.

### Quando usar

- ✅ **Ambiente de desenvolvimento local** onde voce confia no Claude Code
- ✅ **Workflows repetitivos** que exigem muitas chamadas de ferramentas MCP
- ✅ **Prototipagem rapida** onde a velocidade e mais importante

### Quando desabilitar

- ❌ **Ambientes compartilhados** ou maquinas publicas
- ❌ **Projetos em producao** com dados sensiveis
- ❌ **Primeira vez usando o projeto** — aprove manualmente ate entender o comportamento

---

## ✅ Boas praticas

- **Nunca faca commit do `.mcp.json`** — ele ja esta no `.gitignore`, mas fique atento
- **Sempre valide antes de ativar** — peca ao Claude para validar o workflow antes de coloca-lo em producao
- **Use nomes descritivos** — tanto para workflows ("Sync Shopify Orders to Airtable") quanto para nodes ("Filter Active Users")
- **Trate erros** — adicione caminhos de fallback para chamadas HTTP e operacoes que podem falhar
- **Um workflow por preocupacao** — mantenha workflows focados; use sub-workflows para logica complexa

---

## 🔧 Solucao de problemas

| Problema | Solucao |
|:---------|:--------|
| "MCP server not connected" | Verifique se o Node.js v18+ esta instalado (`node -v`) e se o `.mcp.json` existe |
| "Authentication failed" | Confira a URL e API key no `.mcp.json`. Gere uma nova key se necessario |
| "Permission denied" | Sua API key pode nao ter permissoes suficientes. Peca ao admin uma key com acesso completo |
| Health check falha | Verifique se a URL do n8n esta acessivel no navegador e nao tem barra `/` no final |

---

<div align="center">

**MIT License** · Feito com [Claude Code](https://claude.ai/code) e [n8n MCP Server](https://www.npmjs.com/package/n8n-mcp)

</div>

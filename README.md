<!-- README.md -->
# pr-comment

<div align="center">

# 💬 PR Comment Template

[![GitHub Marketplace](https://img.shields.io/badge/Marketplace-PR%20Comment%20Template-6f42c1?style=for-the-badge&logo=github)](https://github.com/marketplace/actions/pr-comment-template)
[![Version](https://img.shields.io/github/v/release/Malnati/pr-comment?style=for-the-badge&color=purple)](https://github.com/Malnati/pr-comment/releases)
[![License](https://img.shields.io/github/license/Malnati/pr-comment?style=for-the-badge&color=blue)](LICENSE)

**Padronize, Automatize e Embeleze o feedback dos seus Pull Requests.**

<p align="center">
  <a href="#-recursos">Recursos</a> •
  <a href="#-uso-básico">Uso Básico</a> •
  <a href="#-uso-avançado-templates-customizados">Templates Customizados</a> •
  <a href="#-inputs">Inputs</a>
</p>

</div>

---

## 🚀 Sobre

O **PR Comment Template** é uma GitHub Action projetada para equipes que valorizam a comunicação clara. Ela permite postar comentários estruturados (Header, Body, Footer) automaticamente em seus PRs, garantindo que informações cruciais sobre deploys, testes e validações não se percam.

### ✨ Recursos Principais

* **Padronização:** Garanta que todo PR tenha o mesmo formato de feedback.
* **Flexibilidade:** Use o template padrão moderno ou traga seu próprio **Markdown**.
* **Validação:** O sistema verifica se o seu template personalizado contém as variáveis necessárias.
* **Separação de Contexto:** Áreas dedicadas para Mensagem, Escopo (mudanças) e TODOs.

---

## 📦 Uso Básico

Ideal para quem quer começar rápido usando o visual padrão (tema roxo/clean).

```yaml
steps:
  - name: Post Standard Comment
    uses: Malnati/pr-comment@v4.0.2
    with:
      token: ${{ secrets.GITHUB_TOKEN }}
      pr_number: ${{ github.event.pull_request.number }}
      header_actor: ${{ github.actor }}
      header_title: "🔁 Auto-Sync"
      header_subject: "Sincronização de Branches"
      body_message: "A sincronização foi realizada com sucesso pelo bot."
      body_scope: |
        - Base: `main`
        - Head: `develop`
      footer_result: "Sucesso"
````

-----

## 🎨 Uso Avançado: Templates Customizados

Você pode injetar seu próprio arquivo Markdown para ter controle total sobre o layout, emojis e estrutura.

### 1\. Crie seu Template

Adicione um arquivo no seu repositório (ex: `.github/templates/custom.md`). Você **deve** incluir as variáveis abaixo:

| Variável | Descrição |
| :--- | :--- |
| `$ACTOR` | O usuário que disparou a action (ex: github.actor) |
| `$SUBJECT` | O assunto do comentário |
| `$BODY_MESSAGE` | A mensagem principal |
| `$BODY_SCOPE_BLOCK` | Lista de escopo formatada |
| `$BODY_TODO_BLOCK` | Lista de pendências formatada |
| `$FOOTER_BLOCK` | O rodapé com resultados |

**Exemplo de arquivo `custom.md`:**

```markdown
# 🎨 Report de Deploy
**Autor:** @$ACTOR | **Ação:** $SUBJECT

> $BODY_MESSAGE

<details open>
<summary>📂 Escopo da mudança</summary>
$BODY_SCOPE_BLOCK
</details>

---
$FOOTER_BLOCK
```

### 2\. Configure o Workflow

É necessário usar o `actions/checkout` para que a Action consiga ler seu arquivo de template local.

```yaml
steps:
  - name: Checkout Repository
    uses: actions/checkout@v4

  - name: Post Custom Comment
    uses: Malnati/pr-comment@v4.0.2
    with:
      token: ${{ secrets.GITHUB_TOKEN }}
      pr_number: ${{ github.event.pull_request.number }}
      header_actor: ${{ github.actor }}
      header_title: "🚀 Deploy Staging"
      header_subject: "Deploy Automático"
      body_message: "O ambiente de staging foi atualizado."
      body_scope: "- Versão: v1.2.0"
      
      # Caminho relativo para seu arquivo
      template_path: ".github/templates/custom.md"
```

-----

## ⚙️ Inputs

Todas as opções disponíveis para configuração.

| Input | Obrigatório | Tipo | Padrão | Descrição |
| :--- | :---: | :---: | :---: | :--- |
| `token` | **Sim** | Secret | - | Token do GitHub (ex: `secrets.GITHUB_TOKEN`). |
| `pr_number` | **Sim** | Int | - | Número do PR onde o comentário será feito. |
| `header_actor` | **Sim** | String | - | Nome do usuário ou bot autor da mensagem. |
| `header_title` | **Sim** | String | - | Título grande do comentário. |
| `header_subject` | **Sim** | String | - | Subtítulo ou assunto específico. |
| `body_message` | **Sim** | Markdown | - | Corpo principal da mensagem. |
| `body_scope` | Não | Markdown | `""` | Lista de itens afetados ou escopo. |
| `body_todo` | Não | Markdown | `""` | Lista de ações pendentes (checkboxes/bullets). |
| `footer_result` | Não | String | `""` | Resumo final (ex: "Sucesso", "Falha"). |
| `footer_advise` | Não | String | `""` | Conselho ou próximo passo. |
| `template_path` | Não | String | `""` | Caminho relativo para um arquivo `.md` personalizado. |

## 📤 Outputs

| Output | Descrição |
| :--- | :--- |
| `comment_body` | O conteúdo final em Markdown processado. Útil se você quiser enviar o mesmo texto para Slack/Teams em steps seguintes. |

-----

<div align="center">

<sub>Desenvolvido com 🤍 por <a href="https://github.com/Malnati">Ricardo Malnati</a>.</sub>

</div>

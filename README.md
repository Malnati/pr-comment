<!-- README.md -->
# pr-comment

Action para publicar comentários padronizados em Pull Requests usando um template Markdown (header/body/footer).

## Uso rápido

```yaml
name: "Example PR comment"

on:
  pull_request:

jobs:
  comment:
    runs-on: ubuntu-latest
    steps:
      - name: Post PR comment
        uses: Malnati/pr-comment@v2
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          pr_number: ${{ github.event.pull_request.number }}
          header_actor: ${{ github.actor }}
          header_title: "🔁 auto-sync"
          header_subject: "Sincronização de branches"
          body_message: |
            Resultado da sincronização automática entre branches.
          body_scope: |
            - base: `${{ github.event.pull_request.base.ref }}`
            - head: `${{ github.event.pull_request.head.ref }}`
          body_todo: |
            - Revisar conflitos (se houver).
          footer_result: "Sincronização concluída."
          footer_advise: "Verifique o diff antes de fazer o merge."
```

## Inputs

Liste todos os inputs com tabela (token, pr_number, header_, body_, footer_*).

## Outputs
	•	comment_body: corpo final em Markdown, caso queira reutilizar em outro lugar.

## Versão

Instale sempre com tag: `uses: Malnati/pr-comment@v2`

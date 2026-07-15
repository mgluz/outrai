# OutrAI*

Monorepo da metodologia OutrAI* (outracoisa*). Aplicação de scoping + Foundations organizados hierarquicamente. Servido via GitHub Pages.

## Deploy

1. Subir este repositório no GitHub (privado — dados de clientes)
2. Ativar GitHub Pages (Settings → Pages → Branch: main, folder: / (root))
3. Acessar: `https://{user}.github.io/outrai/app/index.html`
4. Tab "Foundations": `?org={slug}&produto={slug}&projeto={slug}`

## Estrutura

```
outrai/
├── app/index.html                                    ← Aplicação
├── registry.json                                     ← Índice mestre
│
└── foundations/
    ├── rubrica/                                      ■ FOUNDATION PERMANENTE
    │   ├── filosofia.md                                Idêntico em todos os projetos.
    │   └── metodologia/                                Fonte: definição OutrAI*.
    │       ├── processos.md                            Dados quantitativos (horas)
    │       ├── atividades.md                           vivem na planilha.
    │       ├── atomos-reqs-evals.md
    │       ├── familias-de-projeto.md
    │       ├── tipos-de-projeto.md
    │       ├── wizard-tipificacao.md
    │       └── regras-recomendacao.md
    │
    └── organizacoes/
        ├── _template/                                  Modelo para nova org.
        │
        └── {org-slug}/
            ├── contexto-organizacional/              ■ FOUNDATION DE NEGÓCIO
            │   ├── modelos-de-negocio.md               Comum a todos os produtos
            │   ├── produtos-canais.md                  desta organização.
            │   ├── design-system.md
            │   ├── estrutura-de-pessoas.md
            │   └── infra-tecnologica.md
            │
            └── produtos/{produto-slug}/
                ├── bases/                            ■ FOUNDATION DE PRODUTO
                │   ├── proposta-de-valor.md            Comum a todos os projetos
                │   ├── premissas-negocio.md            deste produto.
                │   ├── premissas-tecnologia.md
                │   ├── kpis-metricas.md
                │   └── product-backlog.md
                ├── publico/                          ■ FOUNDATION DE PRODUTO
                │   ├── publico-alvo.md
                │   ├── personas.md
                │   ├── pontos-de-dor.md
                │   └── pesquisas-anteriores.md
                ├── mercado/                          ■ FOUNDATION DE PRODUTO
                │   ├── tendencias-transformacoes.md
                │   ├── concorrentes.md
                │   └── analise-competitiva.md
                │
                └── projetos/{projeto-slug}/          □ FOUNDATION DA DEMANDA
                    ├── abordagem-de-projeto.md         Específico desta demanda.
                    ├── descricao-problema.md
                    ├── hipoteses-solucao.md
                    ├── responsaveis.md
                    └── metricas.md
```

## Fontes de dados

| Fonte | Contém | Atualiza | Quem edita |
|-------|--------|----------|------------|
| **Google Sheets** | Processos (horas), tipos, reqs & evals | Tempo real | Equipe OutrAI* |
| **Arquivos .md (Git)** | Foundations de negócio, produto, público, mercado, demanda | Após git push | Equipe + agentes IA |
| **Rubrica .md (Git)** | Filosofia, metodologia | Só quando a metodologia muda | Equipe OutrAI* |

## Como funciona

A app (index.html) tem 5 tabs:

| Tab | Fonte de dados | Funciona em |
|-----|---------------|-------------|
| Wizard | Constantes no JS + localStorage | Claude + GitHub Pages |
| Bundles | Constantes no JS + localStorage | Claude + GitHub Pages |
| Processos | Constantes no JS + localStorage | Claude + GitHub Pages |
| Requisitos & Evals | localStorage | Claude + GitHub Pages |
| **Foundations** | **fetch de registry.json + .md** | **Só GitHub Pages** |

As 4 tabs originais usam um shim de compatibilidade: em Claude artifacts usam `window.storage`; em GitHub Pages usam `localStorage` automaticamente.

A tab Foundations usa `fetch` para ler os .md do repositório. Requer que os arquivos estejam acessíveis via HTTP (GitHub Pages).

## registry.json

Índice mestre que a app lê para descobrir organizações, produtos e projetos:

```json
{
  "organizacoes": {
    "acme": {
      "nome": "ACME Corp",
      "produtos": {
        "portal": {
          "nome": "Portal de Gestão",
          "projetos": {
            "redesign-v2": {
              "nome": "Redesign v2",
              "tipo_projeto": "C3"
            }
          }
        }
      }
    }
  }
}
```

## Setup de abordagem

O "Setup de abordagem" é o wizard da aplicação (tab Wizard). As respostas determinam o tipo de projeto e populam o foundation `abordagem-de-projeto.md` na demanda.

## Convenção CSD

Cada informação nos .md carrega um nível de certeza:

- **Certeza** — Validada por evidência ou decisão explícita.
- **Suposição** — Baseada em inferência. Deve ser validada.
- **Dúvida** — Não se sabe. Precisa ser investigada.

```markdown
### [Item]
> **CSD:** Certeza | Suposição | Dúvida
> **Fonte:** Setup | Sabatina | Descoberta | Decisão do cliente
> **Iteração:** 0

Conteúdo.
```

## Criar nova organização

1. `cp -r foundations/organizacoes/_template foundations/organizacoes/{slug}`
2. Preencher os .md em `contexto-organizacional/`
3. Adicionar ao `registry.json`
4. `git push`

## Criar novo produto

1. `cp -r foundations/organizacoes/{org}/produtos/_template foundations/organizacoes/{org}/produtos/{slug}`
2. Preencher .md em `bases/`, `publico/`, `mercado/`
3. Adicionar ao `registry.json`
4. `git push`

## Criar novo projeto

1. `cp -r .../produtos/{prod}/projetos/_template .../produtos/{prod}/projetos/{slug}`
2. Preencher .md
3. Adicionar ao `registry.json`
4. `git push`

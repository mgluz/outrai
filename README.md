# outrai · Manual da V1

*Aplicação de escopo e processos de projeto. Documento de referência da versão 1.*

- **Aplicação:** arquivo único HTML. Para deploy, use `index.html` (mesmo conteúdo; é o nome que o host serve na raiz da URL).
- **Fonte da verdade dos dados:** [planilha no Google Sheets](https://docs.google.com/spreadsheets/d/19_scUsjHcdQx5CJw0SwTz3rUB45w6kuiT5gzhfaQOqM/edit)
- **Modelo combinado:** fonte única = Sheets; o **app** apenas **lê** a planilha e nunca escreve de volta (as escritas na planilha, quando o assistente ajuda, são feitas fora do app — ver *Governança e acessos*).
- **Editores da planilha:** apenas Felipe e Alice.

---

# Parte 1 — Guia para editores e testadores

Leitura para quem vai **preencher a planilha** e **testar a aplicação**. Não é preciso saber programar.

## O que é a aplicação

Uma ferramenta para tipificar um projeto, ver os processos envolvidos, suas estimativas de esforço e o detalhamento (requisitos, ações e critérios de pronto) de cada processo. O conteúdo de detalhamento vem da planilha; o resto é a lógica da metodologia.

## As abas

| Aba | Para que serve |
|---|---|
| **Wizard** | Responde-se um funil de perguntas e ele classifica o projeto em um dos 25 tipos, recomenda os processos, estima esforço e monta um roadmap. É o ponto de entrada para escopar um projeto novo. |
| **Bundles** | Catálogo de referência dos 25 tipos de projeto (famílias A–E), cada um com um conjunto inicial de processos recomendados. Nome e família de cada tipo vêm da aba **Tipos** (ao vivo); os atributos que definem a recomendação ficam no código. |
| **Processos** | Biblioteca de todos os processos, por etapa, com descrição e os tempos de esforço (💪 humano / 🦾 IA), em horas e somente leitura. O nome do processo vira **link** quando ele tem detalhamento preenchido; sem conteúdo, aparece em verde fraco (sem link). |
| **Reqs & Evals** | Navegação do detalhamento por etapa → processo → átomo. Cada átomo mostra Requisitos, Ações e Evals. Somente leitura — reflete a planilha. |
| **Página do processo** | Não é uma aba: abre ao clicar no nome de um processo (com conteúdo) na aba Processos. Mostra, numa página só, a descrição + todos os átomos com seus Requisitos/Ações/Evals. Tem **Voltar** e navegação **anterior/próximo** entre os processos que têm conteúdo. |

## Como preencher a planilha para aparecer no app

O detalhamento vive na aba **Reqs & Evals** da planilha. **Uma linha = um átomo** (uma tarefa dentro de um processo).

| Coluna | O que colocar |
|---|---|
| **Etapa** | O nome da fase (ex.: `Descoberta`, `Design da Estrutura`). |
| **Processo** | O nome do processo **exatamente** como aparece no app (ex.: `User Research`, `Wireframes`, `Documento de Visão`). |
| **Átomo (tarefa)** | O título da tarefa. |
| **Tag** | `humano` ou `ia`. |
| **Requisitos** | Um ou mais itens, separados por ` | ` (espaço-barra-espaço). |
| **Ações** | Idem, separados por ` | `. |
| **Evals** | Idem, separados por ` | `. |

### Regras de ouro

1. **O nome do Processo precisa bater exatamente** com o do app. Se estiver diferente (acento, maiúscula, espaço), a linha é **ignorada** silenciosamente. O jeito seguro é copiar o nome da aba Processos. (Nomes ignorados aparecem no console do navegador, para facilitar a correção.)
2. **Separe múltiplos itens com ` | `** — cada item vira um marcador na lista.
3. **Tag só aceita** `humano` ou `ia` (qualquer outra coisa cai em `humano`).
4. **Um processo só fica clicável** na aba Processos (e ganha página de detalhe) **quando tem pelo menos uma linha** na aba Reqs & Evals.
5. **Descrição e tempos (💪/🦾) ficam na aba Processos** da planilha (não na Reqs & Evals). Também são lidos ao vivo — editar descrição/Humano(h)/IA(h)/Revisão e recarregar o app já atualiza.
6. **As colunas são reconhecidas pelo nome do cabeçalho, não pela posição.** Pode reordenar ou inserir colunas auxiliares sem quebrar o app. Não mude os *nomes* dos cabeçalhos: Processos — `id` / Etapa / Processo / Descrição / Humano / IA / Revisão; Reqs & Evals — Processo / Átomo / Tag / Requisitos / Ações / Evals; Tipos — Código / Família / Nome do tipo.
7. **A coluna `id` (Processos) é uma chave técnica, não o nome que se lê** (isso é a coluna Processo). Não edite o `id` casualmente: amarra o processo às etapas, ao detalhe e aos átomos, e é referenciado pela lógica do Wizard. Renomear um `id` é **mudança estrutural** — avise o assistente para propagar no código do Wizard também.
8. **Se você renomear um Processo (coluna nome), atualize o mesmo nome na aba Reqs & Evals.** O vínculo dos átomos é pelo nome do processo; renomeou num lado e não no outro, os átomos daquele processo deixam de aparecer (o console avisa quais nomes não bateram).
9. **Adicionar um processo novo** reflete na biblioteca/Bundles ao recarregar, mas o **Wizard só passa a recomendá-lo depois de o assistente sincronizar o código** (o selo vermelho sinaliza). **Adicionar um tipo novo** na aba Tipos precisa dos atributos que hoje só existem no código → é mudança estrutural (avise o assistente).

## Como ver o que você preencheu

- **Na versão publicada (produção):** o app lê **as 3 abas** ao carregar — **Processos** (estrutura + descrição + tempos), **Tipos** (nome e família dos 25) e **Reqs & Evals** (átomos). Salve na planilha e **recarregue a página** do app. Isso inclui **estrutura**: adicionar/remover/reordenar processo, renomear um processo, criar etapa nova — tudo reflete ao recarregar.
- **Selo de sincronia** (canto inferior direito): mostra o estado. Verde "planilha ao vivo" = as 3 abas foram lidas; âmbar "snapshot embutido"/"parcial" = alguma aba não foi lida (preview/offline/não publicada) e está usando o último snapshot; vermelho "divergência" = o Wizard aponta para um processo que não existe mais na planilha (avise o assistente). Passe o mouse para o detalhe.
- **Uma exceção que fica no código:** a **recomendação do Wizard** (quais processos ele sugere por tipo). Um processo novo aparece na biblioteca/Bundles na hora, mas o Wizard só passa a recomendá-lo depois que o assistente sincroniza o código — o selo vermelho avisa quando há essa pendência.
- **No preview aqui do chat:** o preview não busca a planilha ao vivo (restrição de segurança do ambiente); usa o snapshot. Para atualizar, peça no chat **"sincroniza"**.

## Como testar (checklist rápido)

1. Abra a URL de produção.
2. Percorra as abas Wizard, Bundles, Processos, Reqs & Evals.
3. Na aba Processos, confira que só os processos com conteúdo estão clicáveis.
4. Clique num processo com conteúdo → confira descrição, átomos, Requisitos/Ações/Evals.
5. Use **anterior/próximo** e **Voltar**.
6. Edite uma linha na planilha, recarregue o app, confirme que apareceu.

---

# Parte 2 — Arquitetura e contexto

Referência técnica para não depender da memória do chat. Descreve como a V1 está montada e por quê.

## Visão em uma frase

**O Google Sheets é a fonte da verdade; a aplicação é um arquivo HTML único que lê os dados da planilha e apenas os exibe (read-only).**

## Fluxo de dados

```mermaid
flowchart LR
    S[Google Sheets<br/>abas: Processos, Tipos, Reqs & Evals] -->|sync| A

    subgraph A["outrai-scoping.html (arquivo único)"]
      D["#app-data<br/>(JSON embutido = snapshot)"]
      R["Renderização data-driven<br/>Wizard · Bundles · Processos · Reqs & Evals · Página"]
      D --> R
    end

    S -.->|"fetch CSV ao vivo · 3 abas<br/>Processos + Tipos + Reqs & Evals<br/>(só quando hospedado)"| R

    classDef box fill:#141414,stroke:#4DF5A0,color:#F4F4EE;
    class S,D,R box;
```

Há **dois caminhos** para o dado da planilha chegar na tela:

1. **Snapshot embutido (`#app-data`)** — sempre presente. Bloco `<script type="application/json">` dentro do HTML, parseado no carregamento. Faz o app funcionar offline, no preview e como fallback.
2. **Leitura ao vivo (`syncAll()`)** — ao carregar, o app busca as **3 abas** por CSV, na ordem: `syncProcessosFromSheet()` (reconstrói `phases` + `detail`), `syncTiposFromSheet()` (nome/família dos tipos) e `syncAtomsFromSheet()` (`atoms`). Ao final, `runSyncDiagnostics()` cruza o que o Wizard usa com o que existe na planilha e atualiza o selo. Cada aba tem fallback independente ao embutido; ativa de fato **quando hospedado** e as abas acessíveis.

## Componentes internos

| Peça | Papel |
|---|---|
| `#app-data` (JSON) | Snapshot dos dados. Chaves: `phases`, `detail`, `atoms`, `families`, `types`. |
| `DATA` | Objeto em memória, resultado do parse do `#app-data`. |
| `fetchCSV(url)` | Busca uma aba como CSV (timeout 8s) e devolve as linhas, ou `null` em falha. |
| `syncProcessosFromSheet()` | **Reconstrói `phases` (estrutura: etapas/ordem/id/nome) + `detail` (descrição/tempos/revisão)** a partir da aba Processos. Preserva ids de etapa por nome; gera id para etapa nova. Muta as estruturas no lugar e recomputa caches (`PROC_LABEL`, `PROC_DESC`, `ID_BY_LABEL`, `ESTIMATES`). Fallback ao embutido. |
| `syncTiposFromSheet()` | Atualiza `name` e `cat` (família) dos 25 tipos por `Código`. Atributos ficam no código. Fallback ao embutido. |
| `syncAtomsFromSheet()` | Aba Reqs & Evals → `DATA.atoms` (nome de Processo → id via `ID_BY_LABEL`). Fallback ao embutido. |
| `syncAll()` | Orquestra as três, na ordem Processos → Tipos → Átomos, e chama o diagnóstico. |
| `runSyncDiagnostics()` / `updateSyncBadge()` | Cruzam ids do Wizard × ids vivos; escrevem avisos no console e pintam o selo (ok/embutido/divergência). |
| `render*()` | Desenham cada aba a partir de `DATA` — data-driven, nada hardcoded por processo. |
| `window.storage` | Uso **opcional** (edições de estimativa e textos dos bundles). Não é essencial. |

## Esquema dos dados

- `DATA.phases`: `[{ id, name, procs: [[procId, label], ...] }]` — estrutura canônica das etapas e processos (41 processos).
- `DATA.detail[procId]`: `{ description, estimate:{ humanHours, aiHours }, internalReviewHours, requirements[], evals[], activities[] }` — descrição e tempos.
- `DATA.atoms[procId]`: `[{ titulo, tag:'humano'|'ia', req[], acoes[], evals[] }]` — o detalhamento editado na planilha.
- `DATA.families` / `DATA.types`: as 5 famílias (A–E) e os 25 tipos.

## Mapeamento planilha → app (aba Reqs & Evals)

O parser é **guiado pelo cabeçalho**: cada coluna é localizada pelo *nome* na primeira linha (normalizado — sem acento, minúsculas, por trecho-chave), não pela posição. Reordenar colunas ou inserir colunas auxiliares **não quebra** a leitura.

| Coluna (reconhecida por) | Vira |
|---|---|
| "Processo" | `procId` (correspondência exata do rótulo → id) |
| "Átomo"/"tarefa" | `titulo` |
| "Tag" | `tag` (`ia` se for "ia", senão `humano`) |
| "Requisitos" / "Ações" / "Evals" | arrays, quebrando cada célula em ` | ` |

Comportamentos de borda, todos com *fallback* seguro ao snapshot embutido:

- **Rótulo de Processo não reconhecido** → linha descartada, com aviso no console listando o nome. É a fragilidade a vigiar; por isso a regra de "nome exato".
- **Cabeçalho sem coluna "Processo"** (ex.: renomearam a coluna) → leitura ao vivo abortada, mantém o embutido, avisa no console os cabeçalhos que viu.
- **CSV vazio/ilegível, offline ou CORS** → mantém o embutido.

## Mapeamento planilha → app (aba Processos)

Guiado por cabeçalho; a **chave é o `id`**. **Reconstrói a estrutura inteira** (etapas/ordem/processos) e o detalhe.

| Coluna (reconhecida por) | Vira |
|---|---|
| "id" | chave do processo em `phases` e `detail` |
| "Etapa" | agrupa os processos em fases; ordem das fases pela primeira aparição (por "Ordem") |
| "Processo" | rótulo exibido do processo |
| "Ordem" | ordena os processos (e, por consequência, as etapas) |
| "Descrição" | `detail[id].description` (célula vazia **não** apaga a atual) |
| "Humano (h)" / "IA (h)" | `detail[id].estimate.humanHours` / `.aiHours` |
| "Revisão interna (h)" | `detail[id].internalReviewHours` |

- **Etapa nova** (nome inédito) → vira fase nova, com id gerado a partir do nome (slug). Etapas existentes preservam o id atual (mapa nome→id).
- **id duplicado** → o último vence, com aviso no console.
- **Sem colunas `id`/Etapa/Processo** → leitura da aba abortada, mantém o embutido.

## Mapeamento planilha → app (aba Tipos)

Guiado por cabeçalho; **chave = Código**. Só atualiza tipos que já existem.

| Coluna | Vira |
|---|---|
| "Código" | chave do tipo |
| "Nome do tipo" | `type.name` |
| "Família" | `type.cat` (aceito só se a família existir) |

- **Código novo** na planilha → ignorado (não tem os atributos que o Wizard usa); adicionar tipo é **estrutural**, feito no código.

## Diagnóstico de sincronia

Ao final da leitura ao vivo, o app compara os ids de processo que o **Wizard** referencia com os que **existem na planilha**, e pinta o selo no canto:

| Selo | Significado |
|---|---|
| verde **planilha ao vivo** | as 3 abas foram lidas da planilha |
| âmbar **snapshot embutido** / **parcial · N/3** | uma ou mais abas não foram lidas (preview/offline/não publicada) → usando o snapshot |
| vermelho **divergência** | o Wizard aponta para processo(s) que não existem mais na planilha → o código precisa ser sincronizado (o console lista quais) |

O console também informa, sem alarme, processos novos na planilha que o Wizard ainda não usa. Esse é o mecanismo que garante que uma divergência **apareça**, em vez de passar despercebida.

## Restrição técnica que molda tudo

No preview de artefato (aqui no chat), o sandbox **bloqueia busca de dados externos (CSP)**. Por isso o app não lê a planilha ao vivo neste ambiente e depende do snapshot embutido. A leitura ao vivo só funciona **num host próprio**.

## Governança e acessos

Três planos de acesso **independentes**:

| Plano | Quem | Como se controla |
|---|---|---|
| **Editar os dados** (Reqs & Evals, estimativas, tipos/bundles, etapas, processos, átomos) | Felipe e Alice | Planilha **Restrita**; os dois como **Editores** |
| **Evoluir/publicar o app** | Felipe | Conta do host só dele; só ele faz deploy |
| **Usar o app** (Wizard etc.) | Aberto | Deploy público, sem trava de acesso |

**Como as escritas na planilha acontecem** (o app nunca escreve; quem escreve são as pessoas, e o assistente às vezes ajuda dentro da sessão do Chrome de Felipe — não de forma autônoma):

| Tipo de mudança | Quem/como | Frequência |
|---|---|---|
| **Estrutura** (coluna/aba nova, renome de cabeçalho) | Assistente executa na sessão de Felipe, confirmando cada gravação; e ajusta o app no mesmo passo | rara |
| **Conteúdo em volume** (popular processos inteiros) | Assistente gera bloco pronto (TSV/CSV no formato da aba), Felipe/Alice colam | futuro |
| **Conteúdo pontual** | Felipe/Alice, copy-paste | recorrente |

**Protocolo de mudança estrutural:** como o mesmo assistente evolui o app e mexe na estrutura da planilha, os dois lados mudam juntos. Graças ao parser guiado por cabeçalho, inserir/reordenar colunas não exige mudança no app; só **renomear um cabeçalho** reconhecido pede ajuste coordenado (planilha + app no mesmo passo).

## Publicação (pôr no ar)

Como o **app é aberto** (qualquer um com a URL usa o Wizard), a escolha de host fica livre; não é preciso repositório privado. Opções *forever-free* para um arquivo estático:

| Host | Free | Precisa repo público? | Restringe acesso de graça? |
|---|---|---|---|
| **Netlify** (Starter) | Sim | Não (upload direto do arquivo) | Não |
| **Cloudflare Pages** | Sim | Não | **Sim** — Cloudflare Access (login por e-mail, ~50 usuários) |
| **GitHub Pages** | Só repo **público** | Sim | Não |

> Nota: GitHub Pages grátis só serve repositório **público** — foi o que barrou o uso inicial. Com o app aberto, repo público deixa de ser problema, mas Netlify (upload direto) evita expor o código num repo.

**Recomendação:** se URL pública basta, **Netlify** (arrastar o `index.html`, conta grátis para fixar a URL) — simples e alinhado a "centralizar em Felipe". Se um dia quiser travar o acesso sem custo, **Cloudflare Pages + Access**.

**Para a leitura ao vivo funcionar** mantendo o documento privado: **Publicar na web as 3 abas como CSV** — **"Processos"**, **"Tipos"** e **"Reqs & Evals"** (Arquivo → Compartilhar → Publicar na web, uma aba por vez, formato CSV). Isso expõe só os valores dessas abas (o resto do documento segue restrito) e dá URLs `…/pub?...output=csv` estáveis, buscáveis de qualquer host. Passe as três URLs ao assistente; elas viram as constantes `PROC_CSV_URL` (Processos), `TIPOS_CSV_URL` (Tipos) e `SHEET_CSV_URL` (Reqs & Evals) no app.

**Decisões/execuções que são de Felipe (o assistente não faz):** criar/entrar na conta do host e publicar; definir editores e publicar-na-web as abas. Envolvem credenciais e permissões da conta Google.

**Alerta de privacidade:** as URLs dos CSVs publicados são públicas para quem as tiver (embora só exponham essas três abas). A página do app também é pública por URL, salvo se usar Cloudflare Access.

**Pendências para fechar a V1:** (1) host escolhido; (2) URLs de publicação das 3 abas. Com elas, o assistente troca as três constantes e valida o primeiro deploy.

**Verificação pós-deploy:** abra a URL publicada e olhe o **selo de sincronia** no canto: verde "planilha ao vivo" confirma que as 3 abas foram lidas. Se ficar âmbar/parcial, uma aba não foi publicada; se ficar vermelho, há divergência entre Wizard e planilha (o console diz qual). No meio-tempo o app exibe o último snapshot embutido.

## Escopo e limitações conhecidas da V1

- **A leitura ao vivo cobre as 3 abas:** Processos (estrutura + descrição + tempos), Tipos (nome/família) e Reqs & Evals (átomos). Adicionar/remover/reordenar/renomear processos e criar etapas refletem ao recarregar.
- **O que fica no código (a exceção a vigiar):** a **engine de recomendação do Wizard** — as regras condicionais que decidem, por tipo, quais processos são recomendados. Referenciam ids de processo. Se um id for criado/removido/renomeado na planilha, o Wizard só acompanha depois de o assistente sincronizar o código; o **selo de sincronia** avisa (vermelho) quando há essa divergência.
- **Tipos ao vivo só nome/família.** Os atributos de comportamento de cada tipo (que alimentam o Wizard) ainda vivem no código, pois a aba Tipos não tem essas colunas. Expandir a aba é candidato a V1.1.
- **O conteúdo atual de Reqs & Evals são exemplos** autorados para calibração (4 processos), não definições canônicas.
- **O app é read-only:** nunca escreve na planilha. Ver *Governança e acessos*.
- **Estado em `window.storage`** (estimativas/blurbs) pode não sobreviver a republicações; não é a casa do dado.

## Como sai uma nova versão

1. Ajustes de código/lógica são feitos aqui no chat sobre o arquivo atual.
2. Se mudou dado da planilha: rodar o sync (relê a planilha → regenera `#app-data`).
3. Baixar o `index.html` resultante e **redeployar** no host.

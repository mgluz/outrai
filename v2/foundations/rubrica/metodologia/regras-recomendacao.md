---
camada: rubrica
grupo: metodologia
titulo: Regras de Recomendação
---

# Regras de Recomendação de Processos

Dado um tipo de projeto, cada processo recebe: `on` (recomendado), `opt` (opcional), `off` (não aplicável).

## Variáveis de decisão
- `O` = nível do output (visao=1, concept=2, design=3, noar=4)
- `hasRV` = exige descoberta + definição
- `preexist` = novo ou existente
- `scope` = negocio | multiproduto | produto | feature
- `dev` = inclui implementação
- `branding` = inclui naming + identidade

## Regras-chave
- Start: sempre `on`
- Descoberta: `on` se hasRV, senão `opt` (Pocket ativa para features existentes)
- Definição: `on` se hasRV, senão `off`/`opt`
- Design Estrutura: `on` se O≥3, `opt` se O=2, `off` se O=1
- Concept/Happy Path/Layouts: mutuamente exclusivos
- Pocket vs. Descoberta completa: mutuamente exclusivos
- Implementação: `on` se dev=true
- Implantação: regras pendentes de definição

## Ciclo de validação
Ao fim de cada etapa (exceto Start, Implementação, Implantação): Apresentação (0,5d) + Espera (1d) + Ajustes (1d) = 2,5d corridos.
Exceção: features não têm ciclo de validação.

## Overrides
- A3: Key Features = `on`
- B3: Key Features, Backlog, Arquitetura, Labeling, User Flows, Wireframes, Lista de Telas = `on`; Concept = `off`, Happy Path = `on`, Prototipação = `on`

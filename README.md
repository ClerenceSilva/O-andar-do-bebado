# O Andar do Bêbado — Simulação Monte Carlo

Trabalho da disciplina **CE-312 — Estatística Computacional** (UFPR), que modela o clássico problema do "andar do bêbado" (*random walk*) como uma cadeia de Markov em ℤ² e estima suas propriedades por simulação de Monte Carlo com 100.000 réplicas.

## O problema

Um indivíduo parte do cruzamento (0, 0) de uma malha urbana e tenta chegar à sua casa, em (3, 4). A cada esquina ele escolhe uma das quatro direções cardeais com probabilidades fixas (Leste 0,35 / Norte 0,45 / Sul 0,10 / Oeste 0,10), com dois componentes de tempo:

- **Tempo de deslocamento:** 5 minutos por quadra percorrida
- **Tempo de decisão:** com probabilidade 0,30, +1 minuto de hesitação a cada esquina

O processo termina quando o indivíduo chega ao destino ou quando o tempo acumulado atinge o limite estabelecido. Como a decisão em cada esquina depende só da posição atual (não do histórico), o processo é uma Cadeia de Markov, e como P(T ≤ 60) não tem forma fechada, a estimativa é feita via simulação.

## Objetivos do trabalho

1. Estimar P(chegada ≤ 60 min) com intervalo de confiança
2. Obter a distribuição do número de chegadas em 100 tentativas independentes
3. Caracterizar a distribuição dos tempos de chegada
4. Construir a curva P(T ≤ t*) para diferentes limites de tempo t*

## Metodologia

- **Base analítica prévia:** dedução do tempo mínimo de chegada (35 min), da estrutura discreta dos tempos possíveis em "famílias" separadas por múltiplos de 10 (o que explica a multimodalidade observada depois) e da probabilidade teórica do caminho mínimo — usada como *sanity check* da simulação
- **Simulação:** função `simular_bebado()` em R, rodada N = 100.000 vezes (`TEMPO_MAX = 180` min)
- **Intervalos de confiança:** Wilson (via inversão do teste de score) e Bootstrap não paramétrico (10.000 reamostragens), comparados lado a lado
- **Validação distribucional:** teste de aderência qui-quadrado comparando o número de chegadas em grupos de 100 tentativas contra a Binomial(100, p̂) teórica
- **CDF empírica** dos tempos de chegada com banda de confiança bootstrap
- **Curva p(t\*)** reaproveitando as simulações já rodadas (sem re-simular para cada t\*), avaliando a proporção de chegadas para cada tempo-limite de 35 a 180 min

## Principais resultados

- Verificação de sanidade: nenhuma chegada com t < 35 min (limite teórico mínimo) e P(t = 35) simulada bate com o valor teórico calculado analiticamente
- Os dois métodos de IC (Wilson e Bootstrap) convergem para limites praticamente idênticos com N = 100.000
- O teste qui-quadrado não rejeita a hipótese de que o número de chegadas em 100 tentativas segue Binomial(100, p̂), confirmando que as tentativas são i.i.d.
- A distribuição dos tempos de chegada é multimodal, com "vazios" estruturais entre famílias de tempo (ex.: nenhuma chegada possível entre 43 e 44 min) — exatamente como previsto pela análise combinatória da introdução

## Estrutura do repositório

```
├── Trabalho Bebado Vinicius S.qmd        # Relatório completo em Quarto (PDF)
├── Trabalho-Bebado-Vinicius-S.pdf        # Relatório renderizado
├── andar do bebado html.qmd              # Versão em apresentação (Reveal.js)
├── Trabalho Bebado Vinicius S.html       # Apresentação renderizada em HTML
├── Trabalho-Bebado-Vinicius-S_cache/     # Cache de renderização do Quarto (PDF)
├── Trabalho-Bebado-Vinicius-S_files/     # Figuras geradas (PDF)
├── andar-do-bebado-html_cache/           # Cache de renderização do Quarto (HTML)
├── andar-do-bebado-html_files/           # Figuras geradas (HTML)
├── trabalho bebado.txt / trabai deek.txt # Anotações/rascunhos de apoio
```

## Tecnologias utilizadas

- **R** (simulação, `wilson_ic`, teste qui-quadrado, bootstrap)
- **Quarto** — relatório em PDF e apresentação Reveal.js a partir do mesmo conteúdo analítico
- **ggplot2**, **dplyr**, **scales** para análise e visualização

## Como reproduzir

1. Abra o projeto no RStudio (ou qualquer ambiente com Quarto instalado)
2. Instale as dependências: `ggplot2`, `dplyr`, `scales`
3. Renderize o relatório em PDF:

```r
quarto::quarto_render("Trabalho Bebado Vinicius S.qmd")
```

4. Ou renderize a versão em apresentação:

```r
quarto::quarto_render("andar do bebado html.qmd")
```

> A simulação principal roda N = 100.000 réplicas com `set.seed(777)`, garantindo reprodutibilidade exata dos resultados reportados.

## Autor

Vinicius de Lima Santana — Estatística, UFPR

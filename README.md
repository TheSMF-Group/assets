# assets

Pacote de imagens para os projetos de futebol: retratos de jogadores, escudos de
clubes e troféus. Tudo em PNG, com um manifesto por pasta relacionando arquivo →
entidade. Montado em 26/08/2026.

406 MB no total. Fora de qualquer repositório de propósito — ver "Peso" no fim.

```
~/assets/
  faces/            15.157 png   357M   <tm_id>.png
  clubs/               811 png    31M   <continente>/<pais>/<club-id>.png
  trophys/              55 png    14M   <CODIGO>.png
  jogadores.json                        manifesto de faces/ (+ .csv)
  clubs/clubes.json                     manifesto de clubs/
  trophys/trofeus.json                  manifesto de trophys/
```

---

## faces/

Retratos 100x130 dos jogadores, baixados do CDN do Transfermarkt.

**O nome do arquivo é o `tm_id` do Transfermarkt, não o nome do jogador.**
`3373.png` é o Ronaldinho. Para ir do arquivo à pessoa, use `jogadores.json`:

```json
{"arquivo": "3373.png", "tm_id": 3373, "nome": "Ronaldinho",
 "nacionalidade": "Brazil", "nascimento": "1980-03-21",
 "ultimo_clube": "Clube Atlético Mineiro", "ultima_temporada": 2014}
```

Mesmo conteúdo em `jogadores.csv`, para abrir em planilha.

`ultimo_clube` é o clube da temporada mais recente do jogador, **não** o clube
atual — o banco é histórico e a maioria destes jogadores está aposentada. O campo
`ultima_temporada` diz de quando é: Messi/2025 é atual, Basler/2002 não.

**Origem:** os 15.157 são o *pool* do [theconnections](../theconnections) — os
jogadores que o jogo realmente mostra, não os 189.058 do banco. Destes, 15.157 de
19.920 têm foto; os outros 4.763 não têm retrato no Transfermarkt e precisam de
placeholder em qualquer UI.

**Regenerar:** `python3 ~/theconnections/baixar_fotos.py` (retomável, pula o que
já existe, ~14 min a 17,7/s para o lote completo). Ele lê os alvos de
`theconnections/dados.js` e escreve direto aqui.

Cuidado ao mexer no parse de nome de arquivo: o CDN usa **três** formatos —
`2-1461223425.jpg`, o antigo `s_27_123_2012_1.jpg` e o antigo com iniciais na
frente `l_m_s_195_72_2009_1.jpg`. Um parse que só entende o primeiro perde 2.145
jogadores em silêncio, justamente os mais velhos.

---

## clubs/

Escudos dos clubes do [thefenomeno](../thefenomeno), organizados por continente e
país. Convertidos de `.webp` para PNG, com transparência preservada.

| continente | escudos | países |
|---|---|---|
| europa | 341 | 21 |
| america-do-sul | 196 | 9 |
| africa | 138 | 10 |
| asia | 78 | 5 |
| america-do-norte | 45 | 2 |
| oceania | 13 | 1 |

O nome do arquivo é o `id` do clube no jogo (`real-madrid.png`), que é a mesma
chave usada em `thefenomeno/src/game/clubs.ts` e em `public/crests/`.

`clubes.json` traz clube, país, continente, confederação e liga de cada um.

**Australia está em `oceania/`, não em `asia/`.** No jogo os 13 clubes australianos
estão na AFC — correto no futebol. Mas a pasta é geográfica, então foram para
Oceania. As outras cinco pastas batem 1:1 com as confederações (UEFA → europa,
CONMEBOL → america-do-sul, CAF → africa, CONCACAF → america-do-norte, AFC → asia).

**Faltam 53.** O thefenomeno tem 864 clubes no código e 811 escudos; para os 53
sem imagem o jogo desenha um escudo SVG gerado. Eles não estão aqui.

---

## trophys/

55 imagens de competições do thefenomeno. O nome do arquivo é o código da
competição usado no jogo (`UCL.png`, `LG_BRA1.png`, `ST_carioca.png`).

**53 têm fundo transparente.** São as versões recortadas que o jogo serve em
produção, puxadas de `TheSMF-Group/thefenomeno-assets` (o repo público que
alimenta o jsDelivr). Não use os `.jpeg` de `thefenomeno/_assets_in/trophies-src/`
como fonte: são as imagens brutas, com fundo, e o recorte já foi feito uma vez.

As duas exceções opacas são `LEAGUE_ONE` e `EFL_CARABAO`, que existem só como
jpeg em `trophies-src/` — não estão no `tmap.json` nem no repo de produção, e os
códigos foram inventados aqui. Se o jogo passar a usá-las, elas precisam de
recorte e de um código canônico.

`_default.png` é a taça genérica usada para qualquer competição sem arte própria.

## Peso

406 MB. Não versionar. `theconnections/.gitignore` já ignora `fotos/`, e esta
pasta vive fora de qualquer repositório justamente para não entrar em nenhum por
acidente.

O disco da máquina estava com 11 GB livres de 228 GB quando isto foi montado, o
que é apertado. As faces foram **movidas** de `theconnections/fotos/`, não
copiadas. Se for liberar espaço, `faces/` é 85% do peso e é reconstruível pelo
script em ~14 min.

Os PNG das faces são ~5x o JPEG de origem (357 MB contra ~56 MB) sem ganho de
qualidade: a fonte é JPEG lossy de 100x130. O formato foi uma escolha deliberada;
se um dia o peso incomodar, rodar o script salvando `.jpg` resolve sem tocar em
mais nada.

---

## Proveniência

| pasta | fonte |
|---|---|
| `faces/` | CDN do Transfermarkt (variante `medium`), via `theconnections/football.db` |
| `clubs/` | `thefenomeno/public/crests/` + metadados de `thefenomeno/src/game/clubs.ts` |
| `trophys/` | `TheSMF-Group/thefenomeno-assets` (recortados) + 2 jpeg de `_assets_in/trophies-src/` |

Imagens de terceiros, sem licença de redistribuição. Uso local/interno.

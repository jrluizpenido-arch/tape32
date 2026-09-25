# TAPE 32

App de ranking NFL por explosões 20+, turnovers, flags em 3º/4º, margem e SOS.

## Estrutura

```
nfl-stats-app/
├── index.html          # App (abre no navegador)
├── data/
│   ├── season.json     # Dados da temporada (carregado automaticamente)
│   └── schema.md       # Documentação do formato
└── scripts/
    ├── build_from_nflverse.R   # Gera season.json via nflfastR (recomendado)
    └── build_from_nflverse.py  # Alternativa em Python
```

## Como atualizar com dados reais (nflverse)

### Opção 1 — R (melhor)

```bash
# Instalar pacotes uma vez
install.packages(c("nflreadr", "dplyr", "jsonlite"))

# Na pasta do projeto
Rscript scripts/build_from_nflverse.R 2026 1 2
# → escreve data/season.json
```

### Opção 2 — Python

```bash
pip install nfl_data_py pandas
python scripts/build_from_nflverse.py --season 2026 --weeks 1-2
```

Ou com CSV baixado do nflverse:

```bash
python scripts/build_from_nflverse.py --pbp play_by_play_2026.csv --weeks 1-2
```

Depois é só recarregar o `index.html` no navegador. O app lê `data/season.json` sozinho.

## Nota Tape (pesos)

| Componente | Peso |
|------------|------|
| Explosões ofensivas | 18% |
| Explosões permitidas | 16% |
| Saldo de turnovers | 18% |
| Flags em 3º/4º | 10% |
| Margem de pontos | 18% |
| SOS | 12% |
| Recorde (W-L %) | 8% |

## Servir localmente

Alguns navegadores bloqueiam `fetch` de arquivo local. Use um servidor simples:

```bash
cd nfl-stats-app
python3 -m http.server 8080
# abra http://localhost:8080
```

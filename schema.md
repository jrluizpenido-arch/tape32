# TAPE 32 — Schema de dados

## Arquivo principal
`data/season.json` (ou `data/2026.json`)

O app tenta carregar esse arquivo. Se falhar, usa os dados embutidos.

## Estrutura

```json
{
  "season": 2026,
  "as_of": "2026-09-22",
  "weeks_included": [1, 2],
  "source": "nflverse / nflfastR play-by-play",
  "teams": [
    {
      "abbr": "BUF",
      "name": "Bills",
      "conf": "AFC",
      "div": "East",
      "wins": 2,
      "losses": 0,
      "ties": 0,
      "exp": 14,
      "perm": 9,
      "int_thrown": 1,
      "fumbles_lost": 0,
      "int_forced": 2,
      "fumbles_recovered": 1,
      "saldo": 2,
      "flags_3rd4th": 2,
      "margin": 15,
      "sos": 48,
      "by_week": {
        "1": { "exp": 8, "perm": 4, "saldo": 1, "flags_3rd4th": 1, "margin": 5, "wins": 1, "losses": 0 },
        "2": { "exp": 6, "perm": 5, "saldo": 1, "flags_3rd4th": 1, "margin": 10, "wins": 1, "losses": 0 }
      },
      "sample_plays": [
        {
          "week": 2,
          "game_id": "2026_02_DET_BUF",
          "clock": "12:41",
          "qtr": 1,
          "down": "1st & 10",
          "yardline": "BUF 28",
          "desc": "J.Allen pass deep right to K.Shakir for 34 yards.",
          "yards": 34,
          "type": "pass"
        }
      ]
    }
  ]
}
```

## Campos obrigatórios por time

| Campo | Tipo | Descrição |
|-------|------|-----------|
| abbr | string | Abreviação ESPN/nflverse (BUF, SF, …) |
| name | string | Nome curto |
| conf | "AFC" \| "NFC" | Conferência |
| div | "East"\|"North"\|"South"\|"West" | Divisão |
| wins, losses, ties | number | Recorde no período |
| exp | number | Jogadas ofensivas de 20+ jardas (passe ou corrida) |
| perm | number | Jogadas de 20+ permitidas pela defesa |
| int_thrown | number | Interceptações lançadas (giveaway) |
| fumbles_lost | number | Fumbles perdidos (giveaway) |
| int_forced | number | Interceptações forçadas (takeaway) |
| fumbles_recovered | number | Fumbles recuperados (takeaway) |
| saldo | number | takeaways − giveaways |
| flags_3rd4th | number | Penalidades defensivas que geraram 1st down em 3º ou 4º |
| margin | number | Diferença de pontos (marcados − sofridos) no período |
| sos | number | Proxy 0–100 da força dos adversários (opcional; app pode recalcular) |

## Campos opcionais

- `by_week`: objeto com chave da semana → mesmos campos parciais  
- `sample_plays`: lista de jogadas 20+ para o detalhe do time (relógio, down, descrição)

## Como gerar a partir do nflverse

Ver `scripts/build_from_nflverse.R` ou `scripts/build_from_nflverse.py`.

Resumo das regras no PBP:

1. **Explosão ofensiva**: `yards_gained >= 20` e `play_type` in (pass, run) e não special teams.
2. **Explosão permitida**: mesma jogada, creditada ao `defteam`.
3. **Giveaway**: `interception == 1` ou `fumble_lost == 1` (posteam).
4. **Takeaway**: interceptação ou fumble recuperado pelo `defteam`.
5. **Flag 3º/4º**: `penalty == 1`, `down` in (3, 4), time defensor, e a penalidade resultou em first down automático (ex.: DPI, defensive holding, etc.).

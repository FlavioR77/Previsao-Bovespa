# 📈 Previsão do Ibovespa com ARIMA e SARIMAX — Eliminação Reversa

> **Descoberta principal:** A Bolsa de Shanghai (China) é a **única variável externa** com poder preditivo estatisticamente comprovado sobre o Ibovespa — superando Dólar, Petróleo, Ouro, Minério de Ferro e S&P 500.

---

## 🎯 Objetivo

Desenvolver um modelo quantitativo para prever os movimentos diários do Ibovespa, respondendo a duas perguntas:

1. O histórico do próprio Ibovespa é suficiente para prever seus movimentos?
2. Se não, qual variável externa oferece vantagem preditiva real?

---

## 🧠 Metodologia

### Fase 1 — Baseline (ARIMA Simples)
O modelo analisa apenas o histórico do Ibovespa, sem variáveis externas.  
Utiliza `auto_arima` para encontrar automaticamente a melhor configuração.

**Resultado:** `ARIMA(0,1,0)` — o Ibovespa é um **Passeio Aleatório**.  
A aposta mais segura para o preço de amanhã é o preço de hoje.

### Fase 2 — Eliminação Reversa (Backward Elimination com SARIMAX)
Um algoritmo iterativo coloca 6 variáveis externas em competição e elimina, rodada a rodada, a que possui o maior p-valor (pior relevância estatística), até restar apenas variáveis com p-valor ≤ 0.05.

| Rodada | Eliminada | p-valor |
|--------|-----------|---------|
| 1 | Petróleo | 0.396 |
| 2 | Dólar | 0.452 |
| 3 | Minério de Ferro | 0.324 |
| 4 | Ouro | 0.822 |
| 5 | S&P 500 | 0.079 |
| **6 — Vencedor 🏆** | **Shanghai (aprovada)** | **0.000** |

### Fase 3 — Modelo Final (SARIMAX + Shanghai)
O modelo coroado utiliza exclusivamente a Bolsa de Shanghai como variável exógena, com AIC otimizado e resíduos validados pelo teste Ljung-Box.

---

## 📊 Resultados

| Métrica | ARIMA Simples | SARIMAX + Shanghai |
|---------|---------------|---------------------|
| Variáveis externas | Nenhuma | Bolsa de Shanghai |
| AIC | Baseline | **-6.802,88** (mínimo) |
| P-valor | 0.285 ❌ | **0.000** ✅ |
| Ljung-Box (Q) | — | **0.15** ✅ (> 0.05) |
| Diagnóstico | Passeio Aleatório | Modelo robusto e válido |

---

## 💡 Conclusões

**1. Mais dados ≠ mais qualidade**  
Inserir variáveis em excesso prejudica modelos financeiros. Dólar e Minério perderam relevância estatística assim que o ruído foi isolado.

**2. China > EUA no curto prazo**  
A eliminação do S&P 500 na última rodada foi a grande reviravolta. O desempenho da Bolsa de Shanghai dita a direção diária do pregão brasileiro com força suficiente para tornar Wall Street dispensável neste modelo.

**3. A bússola está no Oriente**  
Se o Ibovespa é essencialmente um Passeio Aleatório, monitorar o fechamento noturno de Shanghai — e não os indicadores americanos tradicionais — é a única vantagem (*edge*) estatística comprovada para o dia seguinte na B3.

---

## 🗂️ Estrutura do Projeto

```
📁 previsao-ibovespa/
│
├── Prevendo_Bovespa.ipynb   # Notebook principal com todo o pipeline
└── README.md                # Este arquivo
```

---

## ⚙️ Requisitos

```bash
pip install yfinance pmdarima pandas numpy matplotlib watermark
```

| Biblioteca | Uso |
|------------|-----|
| `yfinance` | Download dos dados de mercado (Ibovespa e variáveis exógenas) |
| `pmdarima` | `auto_arima` para seleção automática do modelo ARIMA/SARIMAX |
| `pandas` | Manipulação e alinhamento de séries temporais |
| `numpy` | Transformação logarítmica das séries |
| `matplotlib` | Visualização do gráfico Ibovespa vs. Shanghai |
| `watermark` | Registro das versões dos pacotes utilizados |

---

## 🚀 Como Executar

```bash
# 1. Clone o repositório
git clone https://github.com/seu-usuario/previsao-ibovespa.git
cd previsao-ibovespa

# 2. Instale as dependências
pip install yfinance pmdarima pandas numpy matplotlib watermark

# 3. Abra o notebook
jupyter notebook Prevendo_Bovespa.ipynb
```

Execute as células em ordem. O pipeline completo inclui:
- Download automático dos dados via `yfinance`
- Transformação logarítmica e ajuste de fuso horário (Lag)
- Treinamento do modelo ARIMA baseline
- Loop de Eliminação Reversa com SARIMAX
- Visualização do gráfico comparativo Ibovespa vs. Shanghai

---

## 📌 Detalhes Técnicos

**Janela de dados:** ~4,5 anos (~1.120 dias úteis), evitando viés de Mudança de Regime.

**Ajuste de fuso horário (Lag):**
- Mercados ocidentais (Dólar, S&P 500, Petróleo, Ouro, Minério): `shift(1)` — dado do dia anterior, pois fecham após a B3.
- Shanghai: sem deslocamento — fecha ~11h antes da abertura do Brasil, portanto o dado do mesmo dia já está disponível.

**Critério de seleção:** AIC (Akaike Information Criterion) — quanto mais negativo, melhor. Penaliza modelos com variáveis desnecessárias.

**Validação:** Teste Ljung-Box — p-valor > 0.05 confirma que os resíduos são ruído branco, indicando que o modelo extraiu toda a informação disponível dos dados.

---

## 👤 Autor

**Flavio Renan Sant'Anna**  
Análise quantitativa de mercados financeiros brasileiros.

---

*Este projeto é de caráter educacional e não constitui recomendação de investimento.*

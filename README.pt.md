# 📚 Sistema de Recomendação para E-commerce com Modelos de Linguagem

Sistema de recomendação inteligente para plataformas e-commerce, que utiliza modelos de linguagem (LLM) baseados em Transformers, como o BERT, para compreender pedidos em linguagem natural e sugerir livros relevantes com base em embeddings semânticos.

Projeto final da licenciatura em Engenharia Informática (UTAD, 2025). O catálogo vem do [Books to Scrape](https://books.toscrape.com/), uma loja simulada criada para praticar *web scraping*; as compras e o feedback dos utilizadores são **simulados**.

---

## 🧭 Visão geral

O utilizador escreve o que procura (por exemplo, *"quero um livro de mistério"*) e o sistema:

1. interpreta o pedido e deteta a categoria mais provável;
2. filtra o catálogo por categoria, avaliação e preço;
3. compara o pedido com cada livro através de embeddings semânticos;
4. refina e diversifica os resultados;
5. devolve as recomendações com uma explicação e etiquetas.

## ✨ Funcionalidades

- Pesquisa semântica multilingue (pedidos em português, catálogo em inglês)
- Filtros opcionais por categoria e preço
- Explicação e etiquetas para cada recomendação
- Registo de feedback (1 a 5 estrelas ou gostei/não gostei) e consulta do histórico
- API REST documentada (Swagger) e chatbot de consola
- Avaliação offline comparativa com testes de significância estatística

## 🛠️ Tecnologias

| Área | Ferramentas |
|---|---|
| Linguagem | Python |
| Dados e análise | pandas, numpy, scipy, scikit-learn, matplotlib, seaborn, ftfy |
| Recolha de dados | requests, BeautifulSoup |
| IA e NLP | PyTorch, Hugging Face Transformers, Sentence-Transformers, `bert-base-multilingual-cased`, `paraphrase-multilingual-MiniLM-L12-v2` |
| API | FastAPI, Uvicorn, Pydantic |
| Outros | Git e GitHub |

## 🏗️ Estrutura do projeto

```
.
├── api/
│   ├── main.py               # API FastAPI (7 endpoints)
│   ├── models.py             # Modelos Pydantic (pedidos e respostas)
│   └── utils/
│       ├── data_loader.py    # Carregamento, validação e cache dos dados
│       ├── logger.py         # Logging com rotação de ficheiros
│       └── text_processor.py # Processamento de texto
├── core/
│   ├── personalizacao.py     # Pesquisa semântica e perfis de utilizador
│   ├── refinamento.py        # Refinamento por popularidade, sazonalidade e preço
│   └── recomendador.py       # Orquestração do motor de recomendação
├── dados/                    # Datasets (livros, feedbacks, livros limpos)
├── outputs_a/                # Análise exploratória (gráficos e tabelas)
├── outputs_p/                # Variáveis derivadas
├── resultados/               # Modelo ajustado e métricas (gerado por modelo.py)
├── cache/                    # Embeddings e perfis em cache (gerado em execução)
├── testes/
│   └── teste_offline_ab.py   # Avaliação offline comparativa
├── scraper.py
├── preprocessamento.py
├── analise.py
├── download_model.py
├── modelo.py
└── bot.py
```

## ⚙️ Instalação

### Pré-requisitos

- **Python 3.10 ou superior**
- **pip**
- Ligação à internet (para recolher os dados e descarregar os modelos pré-treinados na primeira execução)
- Memória RAM livre suficiente para o treino do modelo (GPU opcional)

### Ambiente virtual

```bash
python -m venv .venv

# Windows (PowerShell)
.venv\Scripts\Activate.ps1

# macOS / Linux
source .venv/bin/activate
```

> **Windows:** se o PowerShell bloquear a ativação (*"running scripts is disabled on this system"*), executa primeiro `Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass` e volta a ativar o ambiente.

### Dependências

```bash
pip install pandas numpy scipy scikit-learn matplotlib seaborn ftfy requests beautifulsoup4 torch transformers datasets accelerate sentence-transformers fastapi uvicorn
```

## ▶️ Ordem de execução

### 1. `scraper.py`
```bash
python scraper.py
```
Recolhe os dados dos livros a partir da fonte online, armazenando-os localmente para posterior processamento. Gera dois ficheiros principais:
- `dados/livros.csv` (informação detalhada dos livros)
- `dados/feedbacks.csv` (compras e feedbacks simulados dos utilizadores)

### 2. `preprocessamento.py`
```bash
python preprocessamento.py
```
Realiza a limpeza, normalização e preparação dos dados recolhidos, tornando-os adequados para análise e modelação (inclui a vetorização TF-IDF e a criação de variáveis derivadas, como sazonalidade, fidelização, valor médio e intervalo entre compras). Os dados processados são guardados em:
- `dados/livros_limpos.csv` (dados limpos e normalizados)
- `outputs_p/` (ficheiros de dados prontos para análise e modelação)

### 3. `analise.py`
```bash
python analise.py
```
Efetua a análise exploratória dos dados, identificando padrões, tendências e características relevantes para o sistema de recomendação. Produz relatórios e visualizações que são guardados em:
- `outputs_a/` (gráficos, tabelas e relatórios de análise exploratória)

### 4. `download_model.py`
```bash
python download_model.py
```
Faz o download do modelo pré-treinado necessário para as etapas de recomendação baseadas em linguagem natural.

### 5. `modelo.py`
```bash
python modelo.py
```
Utiliza os dados preparados e o modelo descarregado para fazer o *fine-tuning* do BERT multilingue na classificação do sentimento do feedback (negativo, neutro, positivo), e avalia o resultado (exatidão, F1 e matriz de confusão). Os resultados do treino e da avaliação são guardados em:
- `resultados/` (modelo ajustado, métricas e outputs de avaliação)

### 6. API (`api/main.py`)
```bash
python -m uvicorn api.main:app --reload
```
Inicia a API que disponibiliza o sistema de recomendação para utilização externa, permitindo pedidos de recomendação em tempo real. A documentação interativa fica em `http://127.0.0.1:8000/docs`. Deve ficar a correr num terminal enquanto o passo 8 é executado.

### 7. `testes/teste_offline_ab.py`
```bash
python testes/teste_offline_ab.py
```
Executa o teste offline comparativo entre o sistema tradicional e o sistema com modelos de linguagem, utilizando os dados e modelos preparados nas etapas anteriores. Os resultados do teste são apresentados no terminal.

### 8. `bot.py`
```bash
python bot.py
```
Executa o chatbot que interage com a API, facilitando a interação com utilizadores finais. Tem de ser executado **noutro terminal**, com a API (passo 6) a correr.

> **Notas**
> - O feedback simulado do passo 1 é gerado aleatoriamente, por isso cada execução produz dados ligeiramente diferentes e, consequentemente, resultados diferentes nos passos seguintes. Falhas pontuais de rede durante o scraping são indicadas no terminal e o processo continua.
> - O passo 5 consome bastante memória RAM. Fecha outras aplicações durante o treino e, se ocorrer um erro de memória, reduz o tamanho do lote (`per_device_train_batch_size`) em `modelo.py`.
> - Se a pasta `resultados/` não existir, a API funciona, mas ignora a análise de sentimento.
> - Na primeira execução da API, os modelos são descarregados e os embeddings dos livros são calculados, o que pode demorar alguns minutos.
> - Se alterares o dataset, apaga `cache/embeddings_cache.pkl` para que os embeddings sejam recalculados.

## 🔌 API

Documentação interativa (Swagger): `http://127.0.0.1:8000/docs`

| Método | Rota | Descrição |
|---|---|---|
| GET | `/` | Estado da API |
| GET | `/health` | Verificação de saúde |
| POST | `/recomendar` | Gera recomendações a partir de um pedido em linguagem natural |
| POST | `/feedback` | Regista o feedback do utilizador |
| GET | `/historico/{usuario_id}` | Histórico de interações de um utilizador |
| GET | `/categorias` | Lista as categorias disponíveis |
| GET | `/estatisticas` | Estatísticas de funcionamento |

## 📊 Avaliação e resultados

A comparação entre o sistema tradicional (popularidade e preferências de categoria) e o sistema com modelos de linguagem foi feita **offline**, com dados simulados e partição temporal. Não foi realizado um teste A/B real.

| Métrica | Tradicional | Modelos de linguagem |
|---|---|---|
| Precisão | 0,014 | 0,011 |
| Recall | 0,018 | 0,015 |
| F1 | 0,016 | 0,013 |
| Hit rate | 0,130 | 0,110 |

Estes valores correspondem à execução documentada no relatório do projeto; uma nova execução pode dar valores diferentes, dado que o feedback simulado é gerado aleatoriamente.

O t-test pareado e o teste de Wilcoxon indicam que as diferenças **não são estatisticamente significativas** (p > 0,8), pelo que não é possível afirmar que um sistema seja melhor do que o outro. As métricas clássicas só valorizam livros que coincidem com o histórico e não medem novidade nem diversidade.

## ⚠️ Limitações conhecidas

**Técnicas**
- A qualidade das recomendações depende dos dados históricos disponíveis.
- A personalização por utilizador não está implementada: o perfil e o histórico são guardados, mas não influenciam a ordenação, que depende do pedido (pesquisa semântica, filtros e diversificação por categoria).
- Explicabilidade limitada das recomendações baseadas em embeddings.

**Metodológicas**
- A avaliação baseia-se em dados offline simulados; é necessária validação com utilizadores reais em ambiente de produção.
- Os dados históricos podem ficar desatualizados face a tendências emergentes.
- O feedback recolhido é registado, mas não é usado para re-treinar o modelo nem integrado em tempo real.

## 🚀 Melhorias futuras

- Usar o histórico para ordenar e excluir livros já vistos
- Integrar o treino multimodal (texto e variáveis de comportamento)
- Embeddings mais especializados e melhor interpretação de pedidos mais complexos
- Acrescentar métricas de novidade e diversidade
- Testes A/B com utilizadores reais
- Interface web, em substituição do chatbot de consola, com filtros de categoria, preço e disponibilidade
- Testes automatizados (unitários e de integração)
- Migrar os dados para uma base de dados
- Anonimização dos dados e conformidade com o RGPD em produção

## 👩‍💻 Autora

**Maria Torres Coelho** 
🔗 [LinkedIn](https://www.linkedin.com/in/maria-coelho-ba14b6284/)

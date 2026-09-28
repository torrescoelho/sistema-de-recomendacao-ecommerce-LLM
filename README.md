# sistema-de-recomendacao-ecommerce-LLM
Sistema de recomendação inteligente para plataformas e-commerce, usando modelos de linguagem de grande escala (LLM) como o BERT para compreender pedidos em linguagem natural e sugerir livros relevantes com base em embeddings semânticos.
## Requisitos e Dependências

- **Python 3.8+**
- **pip** (gestor de pacotes Python)

### Instalar dependências

```bash
pip install -r requirements.txt
```

Se não existir um ficheiro `requirements.txt`, instala manualmente:

```bash
pip install pandas scikit-learn matplotlib transformers torch flask
```

Para garantir o correto funcionamento do sistema de recomendação, a execução dos scripts deve seguir a seguinte 
## Ordem de Execução:

1. **scraper.py**  
Recolhe os dados dos livros a partir da fonte online, armazenando-os localmente para posterior processamento.
Gera dois ficheiros principais: -livros.csv (informação detalhada dos livros)
                                -feedbacks.csv (feedbacks simulados dos utilizadores)


2. **preprocessamento.py** 
Realiza a limpeza, normalização e preparação dos dados recolhidos, tornando-os adequados para análise e modelação.
Os dados processados são guardados na pasta: -dados_limpos.csv (dados limpos e normalizados)
                                             -outputs_p (ficheiros de dados prontos para análise e modelação)

3. **analise.py**  
Efetua a análise exploratória dos dados, identificando padrões, tendências e características relevantes para o sistema de recomendação. 
Produzindo relatórios e visualizações que são guardados em: -outputs_a (gráficos, tabelas e relatórios de análise exploratória)

4. **download_model.py**
Faz o download do modelo pré-treinado necessário para as etapas de recomendação baseadas em linguagem natural.

5. **modelo.py** 
Utiliza os dados preparados e o modelo descarregado para treinar, ajustar e avaliar o sistema de recomendação. Os resultados do treino e avaliação são guardados em: -resultados (métricas, modelos treinados e outputs de avaliação)

6. **api/main.py**  
Inicia a API que disponibiliza o sistema de recomendação para utilização externa, permitindo pedidos de recomendação em tempo real.

7. **testes/teste_offline_ab.py**  
Executa o teste offline comparativo entre o sistema tradicional e o sistema LLM, utilizando os dados e modelos preparados nas etapas anteriores. Os resultados do teste são apresentados no terminal. 

8. **bot.py** 
Executa o bot que interage com a API, simulando ou facilitando a interação com utilizadores finais.


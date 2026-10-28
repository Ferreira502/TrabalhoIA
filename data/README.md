# Dados da competição

| Arquivo | Estado | Uso |
| --- | --- | --- |
| `treino.csv` | Recebido; 69.833 linhas e 50 colunas | Treino e validação interna |
| `teste_sem_rotulo.csv` | Recebido | Gerar previsões somente após definir o pipeline com o treino |
| `modelo_de_submissao.csv` | Recebido | Conferir formato, ids e ordem da submissão |

O teste tem 24.333 linhas, 50 colunas (incluindo `id`) e nenhuma coluna de classe. O modelo de submissão tem os mesmos 24.333 ids, na mesma ordem. O treino tem a coluna `class` com `normal` ou `anomaly` e os 49 atributos correspondentes aos do teste (sem `id`). Não use os arquivos antigos `KDDTrain+` e `KDDTest+` neste trabalho.

Usem o `treino.csv` já presente para executar ou adaptar o notebook.
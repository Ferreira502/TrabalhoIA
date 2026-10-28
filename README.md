# Trabalho de IA — Detecção de Intrusão

Projeto do grupo conforme o [enunciado atualizado](docs/enunciado_novo.pdf), de 23/10/2026.

## Arquivos recebidos

- `data/treino.csv`: treino original com cabeçalho e coluna `class`; 69.833 linhas.
- `data/teste_sem_rotulo.csv`: teste sem classe, com coluna `id`.
- `data/modelo_de_submissao.csv`: modelo com `id` e `class_prevista`.

O arquivo original `treino.csv` já está disponível. O notebook antigo que lia `KDDTrain+.txt` precisa ser adaptado ao novo esquema.

## Etapa 1 — entrega 01/11 (2,00 pontos)

Entregar notebook executado com caracterização do treino, classes `normal` e `anomaly`, desbalanceamento, atributos categóricos, valores ausentes, inconsistências, redundâncias e possíveis vazamentos, além de um diagnóstico breve. A análise deve ser feita no arquivo de treino disponibilizado pela professora. O teste sem rótulo serve para a submissão final; decisões de pré-processamento e escolha de modelo devem ser validadas somente dentro do treino.

Os novos dados incluem atributos derivados e operacionais além dos 41 atributos tradicionais da NSL-KDD. Investiguem especialmente `tipo_alerta_soc`, `tempo_resposta_analista_min` e `score_ids_legado` quanto à disponibilidade no momento da previsão. Alta correlação com a classe, sozinha, não prova vazamento. Atributos como `src_bytes_kb`, `dst_bytes_kb`, `total_bytes`, `same_srv_pct` e `protocolo_cod` também pedem análise de redundância.

## Próximas etapas

| Etapa | Prazo | Entrega |
| --- | --- | --- |
| 2. Pré-processamento | 07/11 | Comparar técnicas de balanceamento, codificação, transformação e redução/seleção |
| 3. Classificadores | 21/11 | Comparar ao menos três algoritmos, com validação dentro do treino |
| 4. Final | 28/11 | Notebook reproduzível, relatório de 3 a 5 páginas e `submissao_<número_da_equipe>.csv` |

A submissão final deve conter exatamente `id,class_prevista`, uma linha por id do teste, na mesma ordem, com valores `normal` ou `anomaly`. O gabarito do teste não é disponibilizado às equipes; a métrica oficial é F1 macro. O relatório final deve declarar o uso de IA generativa para apoio à redação ou depuração, conforme o enunciado.
# Etapa 01 — Análise exploratória e diagnóstico dos dados

**Trabalho:** Detecção de intrusão em redes — Base NSL-KDD  
**Disciplina:** Inteligência Artificial  
**Professora:** Cristiane Neri Nobre

**Integrantes:**

- Gabriel Ferreira Pereira
- Gabriel Matos Nogueira
- Daniel Santos Garcia
- Daniel Rocha de Souza
- Rafael Henrique Silva

**Notebook no Google Colab:** [Acessar o notebook da equipe](https://colab.research.google.com/drive/1k7ayFb_HMa4LdXCq9hVAPYpwFf-_YzWr?usp=sharing)

Este roteiro organiza o conteúdo do relatório em PDF da Etapa 01. Os textos abaixo podem servir como base para a redação da equipe. As indicações de figuras e tabelas devem ser substituídas pelos resultados correspondentes do notebook antes da exportação.

## 1. Objetivo e caracterização da base

**Texto sugerido:**

O objetivo desta etapa é caracterizar os dados fornecidos para a competição de detecção de intrusão e identificar problemas que orientarão o pré-processamento. A análise considera a distribuição das classes, os atributos categóricos, os valores ausentes, as inconsistências, as redundâncias e os possíveis problemas de vazamento de dados.

A análise exploratória foi realizada sobre o arquivo `treino.csv`, que contém 69.833 registros e 50 colunas: 49 atributos descritivos e a variável de classe. No notebook, a coluna `class` foi renomeada para `target`, preservando seus valores: `normal` e `anomaly`. As verificações desta etapa utilizam somente o treino.

Além dos 41 atributos tradicionais da NSL-KDD, o arquivo contém oito atributos adicionais: `src_bytes_kb`, `dst_bytes_kb`, `total_bytes`, `same_srv_pct`, `protocolo_cod`, `score_ids_legado`, `tipo_alerta_soc` e `tempo_resposta_analista_min`. A presença dessas colunas exige investigar tanto a repetição de informações quanto sua disponibilidade no momento da previsão.

**Elemento a incluir:** tabela resumida com o número de registros, colunas, atributos descritivos e classes. A saída da seção “Caracterização da Base” fornece esses dados.

## 2. Classes e desbalanceamento

**Texto sugerido:**

O conjunto de treino apresenta forte desbalanceamento. A classe `normal` representa aproximadamente 94,16% dos registros, enquanto `anomaly` representa 5,84%. Existem cerca de 16,1 conexões normais para cada conexão anômala.

| Classe | Quantidade | Percentual |
| --- | ---: | ---: |
| normal | 65.754 | 94,1589% |
| anomaly | 4.079 | 5,8411% |
| Total | 69.833 | 100% |

Esse resultado mostra a limitação da acurácia isolada: prever todas as conexões como normais produziria aproximadamente 94,16% de acurácia, sem identificar qualquer anomalia. Nas etapas seguintes, a comparação deve considerar o F1 macro, métrica oficial da competição, e as métricas por classe. As técnicas de balanceamento serão comparadas dentro das partições de treino da validação.

**Elemento a incluir:** a tabela de classes do notebook. Um gráfico de barras é opcional; caso seja usado, deve ser produzido a partir dessas mesmas contagens.

## 3. Atributos categóricos e diferenças de formatação

**Texto sugerido:**

Foram identificados quatro atributos textuais: `protocol_type`, `service`, `flag` e `tipo_alerta_soc`. A comparação dos valores originais com uma versão padronizada revelou categorias distintas apenas por diferenças de caixa ou por espaços. Essa verificação foi realizada em uma estrutura auxiliar, preservando a base original.

| Atributo | Categorias originais | Categorias após padronização |
| --- | ---: | ---: |
| protocol_type | 6 | 3 |
| service | 96 | 67 |
| flag | 19 | 11 |
| tipo_alerta_soc | 2 | 2 |

As quantidades de categorias desconsideram valores ausentes. O atributo `service` possui a maior cardinalidade entre os atributos textuais. Na próxima etapa, a escolha da codificação deve considerar o número e a frequência das categorias, evitando atribuir uma ordem artificial aos serviços.

A inspeção também encontrou atributos numéricos com poucos valores distintos. Essa característica não define, sozinha, um atributo categórico: variáveis como `num_failed_logins` e `num_shells` representam contagens e precisam ser interpretadas conforme seu significado.

**Elemento a incluir:** tabela do complemento “Categorias e diferenças de formatação”. Se necessário, selecionar exemplos da tabela de frequências que tornem visíveis os espaços e as diferenças de caixa.

## 4. Valores ausentes e inconsistências

**Texto sugerido:**

Há valores ausentes em diferentes atributos e com proporções bastante distintas. Em `src_bytes`, foram encontrados 27.906 valores ausentes, aproximadamente 39,96% do treino. Em `tempo_resposta_analista_min`, são 63.804 ausências, aproximadamente 91,37%. A interpretação dessa segunda coluna também depende da investigação de possível informação posterior ao evento.

Foram identificadas 358 durações negativas e 189 valores negativos de `src_bytes`, incompatíveis com a interpretação de duração e quantidade de bytes. Cinco atributos de taxa apresentam valores fora do intervalo esperado de zero a um:

| Atributo | Valores fora de [0, 1] |
| --- | ---: |
| rerror_rate | 683 |
| dst_host_same_srv_rate | 670 |
| dst_host_diff_srv_rate | 623 |
| dst_host_srv_diff_host_rate | 739 |
| dst_host_srv_rerror_rate | 714 |

Essas contagens são calculadas por atributo; uma mesma linha pode apresentar mais de um problema. Valores extremos nas estatísticas descritivas também devem ser investigados conforme o domínio, pois uma magnitude elevada, por si só, não comprova erro. Na Etapa 02, serão definidas e comparadas estratégias de tratamento, com parâmetros estimados apenas no treino de cada validação.

**Elementos a incluir:** tabela de ausências com quantidades e, se calculados, percentuais; tabela das violações de domínio. Evitar reproduzir toda a saída de mínimos e máximos sem interpretar os casos relevantes.

## 5. Redundâncias, constantes e conflitos de classe

**Texto sugerido:**

A base apresenta 11.355 duplicatas exatas além da primeira ocorrência, equivalentes a aproximadamente 16,26% dos registros. Essa contagem considera todas as colunas, incluindo a classe. Caso registros iguais sejam distribuídos entre treino e validação, a estimativa de desempenho pode ser favorecida pela repetição de exemplos.

A investigação de redundância encontrou quatro relações matemáticas entre os atributos. Todas foram verificadas nos registros em que os valores necessários estavam disponíveis:

| Atributo | Relação verificada |
| --- | --- |
| src_bytes_kb | src_bytes / 1024 |
| dst_bytes_kb | dst_bytes / 1024 |
| total_bytes | src_bytes + dst_bytes |
| same_srv_pct | same_srv_rate × 100 |

Além disso, `protocolo_cod` apresenta correspondência de um para um com `protocol_type` após a padronização textual: `tcp` corresponde a 0, `udp` a 1 e `icmp` a 2. O atributo `num_outbound_cmds` é constante, com valor zero em todo o treino. Esses resultados fornecem evidências para avaliar a remoção de informações repetidas ou sem variação na próxima etapa.

Também foram encontrados 1.702 grupos com os mesmos 49 atributos descritivos e classes diferentes, envolvendo 3.730 linhas. O agrupamento considera os valores originais e os padrões de ausência. Esses conflitos sugerem possível ruído nos rótulos, mas não permitem determinar automaticamente qual classe está correta ou como os conflitos surgiram. Eles constituem uma análise distinta da contagem de duplicatas exatas.

**Elementos a incluir:** resumo das fórmulas, tabela de protocolo versus código e contagens de duplicatas e conflitos. Correlação elevada entre duas variáveis pode sugerir redundância, mas não substitui a verificação de seu significado.

## 6. Investigação de possíveis vazamentos de dados

**Texto sugerido:**

A associação com a classe foi investigada por correlação de Pearson para os atributos numéricos, usando `anomaly = 1` e `normal = 0`. O notebook apresenta a tabela completa, com o sinal da correlação, sua magnitude e a quantidade de observações válidas. A correlação de `num_outbound_cmds` é indefinida por se tratar de uma coluna constante. Para os atributos textuais, foram examinadas as contagens por classe e as taxas de anomalia por categoria padronizada.

Três atributos adicionais merecem atenção especial:

- **`score_ids_legado`:** apresenta correlação de aproximadamente 0,5734 com a classe binária. É necessário verificar como o escore foi produzido e se estaria disponível antes da previsão.
- **`tipo_alerta_soc`:** a categoria `ALERTA` aparece em 3.379 anomalias e 1.657 conexões normais. A associação é forte, mas a coluna não corresponde exatamente ao rótulo. Sua utilização depende de conhecer quando o alerta é produzido.
- **`tempo_resposta_analista_min`:** está preenchido em aproximadamente 76,5% das anomalias e ausente em 95,6% das conexões normais. Se representar uma informação registrada após o incidente ou sua classificação, seu uso introduzirá informação futura. O padrão de ausência também merece investigação.

A associação estatística isolada não confirma vazamento. A decisão de manter ou excluir essas colunas deve considerar sua origem e sua disponibilidade no momento em que o modelo será utilizado. A presença de duplicatas entre futuras partições de validação constitui outro risco metodológico identificado.

**Elementos a incluir:** uma seleção das correlações mais relevantes, o cruzamento entre `tipo_alerta_soc` e classe e os percentuais de preenchimento de `tempo_resposta_analista_min`. A tabela numérica completa permanece disponível no notebook.

## 7. Conclusão — Breve diagnóstico dos dados

**Texto sugerido:**

A análise exploratória identificou uma base fortemente desbalanceada, com valores ausentes, diferenças de formatação nas categorias, violações de domínio, atributos redundantes, uma coluna constante e registros repetidos ou com classes conflitantes. Esses problemas precisam ser considerados antes do treinamento para que a comparação dos classificadores seja confiável.

Na Etapa 02, o grupo deverá avaliar a padronização das categorias, o tratamento dos valores inválidos e ausentes, a redução de informações redundantes e as estratégias de balanceamento. A origem dos atributos operacionais suspeitos também precisa ser esclarecida para decidir sobre sua utilização. As transformações que dependem de estimativas dos dados devem ser ajustadas apenas no treino de cada partição de validação, com cuidado para evitar a presença de exemplos repetidos em partições diferentes.

Os resultados desta etapa constituem o diagnóstico inicial. As decisões definitivas de pré-processamento serão justificadas pela análise de domínio e pelas comparações das etapas seguintes.

## 8. Declaração de uso de IA

**Texto sugerido, a conferir e complementar pela equipe:**

Foi utilizado o ChatGPT/Codex como apoio à revisão e complementação das verificações do notebook e à organização do roteiro de redação do relatório. A equipe deve revisar os cálculos, as interpretações e o texto antes da entrega, além de declarar outras utilizações de IA que tenham ocorrido.

## Conferência antes de gerar o PDF

- Conferir os nomes dos integrantes e testar o acesso ao Colab com uma conta que não seja a proprietária do notebook.
- Atualizar no Colab a versão que contém os complementos feitos no notebook local.
- Executar o notebook em sequência com o arquivo de treino e salvar as saídas.
- Substituir as instruções deste roteiro por tabelas e figuras selecionadas, com legendas.
- Revisar o texto da equipe e a declaração de uso de IA.
- Exportar o relatório para PDF e conferir se tabelas, nomes e link do Colab estão legíveis.

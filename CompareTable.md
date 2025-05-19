Lemma and Stopwords with different K

| With lemma and stopwords | f score |
| ------------------------ | ------- |
| 3                        | 0.1625  |
| 4                        | 0.1691  |
| 5                        | 0.1608  |
| 6                        | 0.1574  |

| No lemma NO StopWords | f score |
| --------------------- | ------- |
| 3                     | 0.1659  |
| 4                     | 0.1623  |
| 5                     | 0.1591  |
| 6                     | 0.1529  |

| With lemma NO stopWords | f score    |
| ----------------------- | ---------- |
| 3                       | 0.1649     |
| **4**                   | **0.1734** |
| 5                       | 0.1616     |
| 6                       | 0.1502     |

| No lemma With STOPWORDS | f score |
| ----------------------- | ------- |
| 3                       | 0.1700  |
| 4                       | 0.1688  |
| 5                       | 0.1619  |
| 6                       | 0.1523  |

| NUM of TFIDF_TOP_K | f score    |
| ------------------ | ---------- |
| 200                | 0.1591     |
| **500**            | **0.1734** |
| 800                | 0.1570     |
| 1000               | 0.1575     |



With or with out attention mechanism

The first on/off indicate whether use CLASS WEIGHT or not

The second on/off indicates whethere use MIX_EVIDENCE or not

|                       | Evidence Retrieval F-score (F) | Claim Classification Accuracy (A) | Harmonic Mean of F and A |
| --------------------- | ------------------------------ | --------------------------------- | ------------------------ |
| NOAttentionOFFOFF     | 0.17336631622345908            | 0.45454545454545453               | 0.2509998209275951       |
| NOAttentionONOFF      | 0.17336631622345908            | 0.461038961038961                 | 0.2519797018578497       |
| WithAttentionOFFOFF   | 0.17336631622345908            | 0.44805194805194803               | 0.24999946147731847      |
| WithAttentionONOFF    | 0.17336631622345908            | 0.474025974025974                 | 0.25388049301438886      |
| **WithAttentionONON** | **0.17336631622345908**        | **0.512987012987013**             | **0.2591512707145685**   |

Evidence Retrieval F-score (F)    = 0.17336631622345908
Claim Classification Accuracy (A) = 0.5584415584415584
Harmonic Mean of F and A          = 0.26459118346442284
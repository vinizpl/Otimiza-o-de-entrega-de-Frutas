# Problema de Otimização de Logística

Este projeto apresenta um modelo de Programação Linear Inteira para resolver um problema de otimização logística. O objetivo principal é determinar a melhor rota de transporte (combinação de caminhão e centro de destino) para cada cliente produtor de frutas, de forma a **minimizar a deterioração total** da carga.

A decisão está sujeita a um conjunto de restrições, incluindo a garantia de que cada cliente seja atendido uma única vez e que os custos de transporte não ultrapassem o orçamento máximo de cada cliente.

## Formulação Matemática

O problema pode ser modelado matematicamente da seguinte forma:

### Conjuntos e Índices
* `i` $\in$ `{1, ..., p}`: Índice para os **tipos de caminhão**.
* `j` $\in$ `{1, ..., m}`: Índice para os **centros de destino**.
* `k` $\in$ `{1, ..., n}`: Índice para os **clientes**.

### Parâmetros de Entrada
* **$d_{kj}$**: Distância (em km) entre o cliente `k` e o centro de destino `j`.
* **$c_{i}$**: Custo do transporte por km (€/km, R$/km, etc.) associado ao caminhão do tipo `i`.
* **$t_{k}$**: Taxa de deterioração por km da fruta vendida pelo cliente `k`.
* **$I_{k}$**: Investimento máximo (orçamento) disponível do cliente `k` para o transporte.

### Variável de Decisão
A variável de decisão do modelo é:

$$
Y_{ijk} = 
\begin{cases} 
1, & \text{se o caminhão } i \text{ é escolhido para levar a carga do cliente } k \text{ ao centro } j \\
0, & \text{caso contrário}
\end{cases}
$$

### Função Objetivo
O objetivo é **minimizar a deterioração total** acumulada em todas as entregas. A deterioração para uma entrega é o produto da taxa de deterioração específica do cliente pela distância percorrida.

$$
\text{Minimizar} \quad Z = \sum_{k=1}^{n} \sum_{j=1}^{m} \sum_{i=1}^{p} t_k \cdot d_{kj} \cdot Y_{ijk}
$$

### Restrições (Sujeito a)

1.  **Restrição de Atribuição Única:** Cada cliente deve ser atendido por exatamente um caminhão e ter sua carga enviada para um único centro.
    $$
    \sum_{i=1}^{p} \sum_{j=1}^{m} Y_{ijk} = 1 \quad \forall k \in \{1, \dots, n\}
    $$

2.  **Restrição Orçamentária:** O custo total do transporte para cada cliente (produto do custo por km do caminhão pela distância) não pode exceder seu orçamento máximo.
    $$
    \sum_{i=1}^{p} \sum_{j=1}^{m} (c_i \cdot d_{kj}) \cdot Y_{ijk} \leq I_k \quad \forall k \in \{1, \dots, n\}
    $$

3.  **Natureza da Variável:** A variável de decisão deve ser binária.
    $$
    Y_{ijk} \in \{0, 1\} \quad \forall i, j, k
    $$

---

## Implementação e Execução

O modelo foi implementado em Python utilizando a biblioteca **Google OR-Tools**. O script lê os dados dos clientes de um arquivo CSV, monta e resolve o problema de otimização.

### Dependências
Para executar o código, você precisa da biblioteca `ortools`. Instale-a usando pip:
```bash
pip install ortools
```

### Estrutura do Arquivo de Entrada (`clientes.csv`)
O script espera um arquivo chamado `clientes.csv` no mesmo diretório. O arquivo deve conter as seguintes colunas:
* `id`: Identificador único do cliente.
* `investimento`: Orçamento máximo do cliente.
* `taxa_deterioracao`: A taxa de deterioração do produto do cliente.
* `dist_c1`, `dist_c2`, ..., `dist_c5`: A distância do cliente para cada um dos 5 centros de destino.

**Exemplo de `clientes.csv`:**
```csv
id,investimento,taxa_deterioracao,dist_c1,dist_c2,dist_c3,dist_c4,dist_c5
1,100,0.2,150,200,80,300,500
2,120,0.3,180,220,100,280,450
3,80,0.15,90,110,150,250,400
4,200,0.25,210,100,300,400,150
```


### Como Executar
1.  **Salve o código:** Salve o bloco de código Python acima em um arquivo, por exemplo, `solver.py`.
2.  **Crie o arquivo de dados:** Crie o arquivo `clientes.csv` no mesmo diretório, com os dados dos seus clientes, seguindo o formato do exemplo.
3.  **Execute via terminal:** Abra um terminal ou prompt de comando, navegue até a pasta onde salvou os arquivos e execute o script:
    ```bash
    python solver.py
    ```
4.  **Analise os resultados:** O script imprimirá no terminal a alocação ótima encontrada para cada cliente, detalhando o caminhão escolhido, o centro de destino, o custo da rota e a deterioração associada.

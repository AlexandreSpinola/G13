# Projeto de Algoritmos em Grafos - Roteamento de Veículos (MCGRP)

**Universidade Federal de Lavras (UFLA)**
**Disciplina:** GCC262 - Grafos e suas Aplicações
**Professor:** Mayron César O. Moreira

**Integrantes da dupla:** Alexandre Marques Spinola, Gustavo do Carmo Resende

---

## 1. Visão Geral do Projeto

Este projeto aborda uma variação do Problema do Carteiro Chinês Misto (MCGRP), um desafio de otimização combinatória com grande aplicação em logística. O objetivo é desenvolver um sistema para encontrar um conjunto de rotas de custo mínimo para uma frota de veículos, que deve atender a uma série de serviços distribuídos em um grafo (em nós, arestas ou arcos).

A solução foi implementada em Python 3 e segue a metodologia de duas fases principais:
1.  **Fase Construtiva (Etapa 2):** Geração de uma solução inicial válida através de uma heurística inteligente.
2.  **Fase de Melhoria (Etapa 3):** Refinamento da solução inicial utilizando um algoritmo de busca local para otimizar o custo total.

## 2. Estrutura e Arquitetura do Código

O código foi projetado de forma modular e orientado a objetos para garantir clareza, manutenibilidade e um baixo acoplamento entre os componentes lógicos.

* `main.py`: O ponto de entrada do programa. Ele gerencia o fluxo de execução, lendo cada arquivo de instância, acionando os algoritmos e salvando a solução final.

### Classes Principais:

* **`Grafo`**: A classe responsável por toda a interação com os dados. Ela realiza a leitura dos arquivos de instância `.dat`, armazena as informações do problema (capacidade, depósito, serviços, etc.) e executa o pré-processamento essencial, que é o cálculo da matriz de distâncias mínimas entre todos os nós via algoritmo de **Floyd-Warshall**.

* **`Rota`**: Modela a rota de um único veículo. É responsável por controlar a sequência de visitas, o custo acumulado e a carga total, assegurando que as restrições sejam cumpridas.

* **`Solucao`**: Representa a solução completa para uma instância, contendo um conjunto de objetos `Rota`. Ela calcula o custo total e formata o arquivo de saída `.dat` conforme o padrão exigido.

* **`PathScanning`** e **`InsertionHeuristic`** (Etapa 2): São as duas heurísticas construtivas utilizadas para gerar a solução inicial. O sistema adota uma **estratégia híbrida**, escolhendo a heurística mais apropriada com base nas características da instância.

* **`BuscaLocal`** (Etapa 3): Implementa o algoritmo de melhoria. Após a geração de uma solução inicial, esta classe tenta otimizá-la iterativamente, aplicando movimentos de vizinhança para reduzir o custo total.

## 3. Algoritmos Implementados

A solução final é o resultado da combinação de uma heurística construtiva com uma busca local.

### Etapa 2: Estratégia Híbrida para Construção da Solução

O programa analisa cada instância e escolhe a melhor ferramenta para o trabalho:

1.  **Para Frotas Ilimitadas (`#Vehicles: -1`):**
    * **Algoritmo:** `PathScanning`.
    * **Lógica:** Esta heurística é puramente gulosa e sequencial. Ela preenche uma rota ao máximo, sempre escolhendo o serviço mais próximo, antes de criar uma nova rota.
    * **Justificativa:** Essa abordagem naturalmente minimiza o número de veículos utilizados, o que é ideal quando não há um limite de frota, pois cada rota a menos economiza o custo de uma viagem de ida e volta ao depósito.

2.  **Para Frotas Fixas (`#Vehicles > 0`):**
    * **Algoritmo:** `InsertionHeuristic`.
    * **Lógica:** Esta heurística é mais estratégica. Ela considera todas as rotas disponíveis e, para cada serviço restante, encontra a melhor posição de inserção (aquela que causa o menor aumento de custo) em qualquer uma das rotas.
    * **Justificativa:** É superior para distribuir os serviços de forma mais equilibrada entre um número predefinido de veículos. Caso seja impossível atender a todos os serviços sem exceder o limite da frota, o algoritmo tem a permissão de criar rotas extras para garantir que a solução seja sempre completa.

### Etapa 3: Busca Local para Melhoria

Após a construção da solução inicial, um algoritmo de **Busca Local** é aplicado para refiná-la. Ele opera com a estratégia de *first improvement* (primeira melhora) e explora duas vizinhanças clássicas, em sequência:

1.  **Transferência (Relocate Inter-Rotas):** Tenta mover um serviço de sua rota atual para uma posição em outra rota.
2.  **Re-inserção (Relocate Intra-Rota):** Tenta encontrar uma posição melhor para um serviço dentro de sua própria rota.

O processo continua até que nenhum dos dois movimentos consiga mais reduzir o custo da solução, atingindo assim um ótimo local.

## 4. Como Executar o Projeto

### Pré-requisitos
* Python 3.x

### Passos
1.  Assegure que todas as dependências (`os`, `math`, `sys`, `copy`, `time`, `traceback`, `random`) estejam disponíveis na sua instalação Python (são todas bibliotecas padrão).
2.  Crie uma pasta chamada `MCGRP` no mesmo diretório do script e adicione todos os arquivos de instância (`.dat`) nela.
3.  Defina o nome da pasta de saída na função `main()`. O padrão atual é `G13`.
    ```python
    # Dentro da função main()
    pasta_entrada, pasta_saida = 'MCGRP/', 'G13/'
    ```
4.  Execute o script principal pelo terminal:
    ```bash
    python nome_do_arquivo.py
    ```
O script irá iterar sobre cada instância, aplicar os algoritmos e salvar a solução final otimizada na pasta de saída designada.

## 5. Formato do Arquivo de Saída

Cada arquivo `sol-*.dat` gerado segue estritamente o formato:
* **Linha 1:** Custo total da solução final.
* **Linha 2:** Número total de rotas na solução.
* **Linha 3:** Tempo total de execução do algoritmo em nanossegundos (Etapa 2 + Etapa 3).
* **Linha 4:** Tempo para gerar a solução inicial em nanossegundos (apenas Etapa 2).
* **Linhas seguintes:** A descrição detalhada de cada rota.

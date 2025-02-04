# Blockchain-Contest-Round-1

Deadline: 23:59, February 4th (Dubai time) 
Tasks: Blockchain Validation (https://contest.com/docs/BlockValidationChallenge) (C++ code optimization)

ContestBot (https://t.me/contestbot) is now ready to accept submissions for Blockchain Contest Round 1. (https://t.me/contest/393)

for “TON Block Validation Challenge” 
an archive containing the following: 
– solution.patch – a patch to the original branch.
– README.md – a description of the implemented optimizations and their effectiveness. If you tried some optimizations that didn't work, kindly provide details regarding them as well.

For this challenge, your submissions will be tested in the following environment:
OS: ubuntu-22.04
Compiler: clang-14
Hardware: AX-42 Hetzner

for “TON Trustless Bridge Challenge”
an archive containing the following:
1. Code of Lite-client smart-contract and script for generation messages for that contract with build instructions.
2. Code of Transaction-checker smart-contract and script for generation messages for that contract with build instructions.
3. Addresses of smart-contracts deployed in Testnet and Fastnet respectively, with the tx_hash of transactions that demonstrate both successful and unsuccessful checks of blocks and transactions.

git clone --recursive https://github.com/ton-blockchain/ton.git
cd ton
git checkout validation-contest
Your solution will be the contest/solution/contest-solution CMake target.
The entry point of the solution is run_contest_solution in contest/solution/solution.cpp. It takes:
1. block_id - id of the block (shard, seqno and hashes).
2. block_data - serialized block data, but without state update (it is replaced with an empty cell).
3. collated_data - serialized collated data (Merkle proofs of shard states).
4. promise - the Promise object to return the result to.
4a) If the block is valid, the solution should return the serialized Merkle update.
4b) If the block is invalid, the solution should return any td::Status::Error.

So, I would like to suggest using Dijkstra's (Дейкстры) algorithm https://ru.wikipedia.org/wiki/Алгоритм_Дейкстры to optimize the code.
This is the most optimal algorithm for finding the shortest path in a weighted graph.This algorithm will help optimize TON Core using graph theory.
Dijkstra's algorithm:

#include <iostream>
#include <vector>
#include <queue>
#include <limits> // Для infinity

using namespace std;

// Структура для представления ребра графа
struct Edge {
    int to;         // Вершина, в которую ведет ребро
    int weight;     // Вес ребра
};

// Функция алгоритма Дейкстры
vector<int> dijkstra(const vector<vector<Edge>>& graph, int start_node) {
    int num_nodes = graph.size();

    // Вектор для хранения расстояний от стартовой вершины до всех остальных
    vector<int> distances(num_nodes, numeric_limits<int>::max()); // Изначально все расстояния бесконечны
    distances[start_node] = 0; // Расстояние от стартовой вершины до самой себя равно 0

    // Очередь с приоритетом для хранения вершин, которые нужно посетить (вершины с меньшим расстоянием имеют больший приоритет)
    priority_queue<pair<int, int>, vector<pair<int, int>>, greater<pair<int, int>>> pq;
    pq.push({0, start_node}); // Помещаем стартовую вершину в очередь с приоритетом 0

    while (!pq.empty()) {
        // Извлекаем вершину с наименьшим расстоянием из очереди
        int current_distance = pq.top().first;
        int current_node = pq.top().second;
        pq.pop();

        // Если расстояние до текущей вершины уже меньше, чем текущее известное расстояние, пропускаем её
        if (current_distance > distances[current_node]) {
            continue;
        }

        // Перебираем все ребра, исходящие из текущей вершины
        for (const Edge& edge : graph[current_node]) {
            int neighbor = edge.to;
            int weight = edge.weight;

            // Если мы нашли более короткий путь до соседней вершины
            if (distances[neighbor] > distances[current_node] + weight) {
                distances[neighbor] = distances[current_node] + weight; // Обновляем расстояние
                pq.push({distances[neighbor], neighbor}); // Помещаем соседнюю вершину в очередь с новым расстоянием
            }
        }
    }

    return distances; // Возвращаем вектор с расстояниями от стартовой вершины до всех остальных
}

int main() {
    // Пример графа (матрица смежности)
    // В графе 5 вершин (0, 1, 2, 3, 4)
    vector<vector<Edge>> graph(5);

    // Добавляем ребра в граф (направленный граф)
    graph[0].push_back({1, 4});
    graph[0].push_back({2, 2});

    graph[1].push_back({2, 5});
    graph[1].push_back({3, 10});

    graph[2].push_back({4, 3});

    graph[3].push_back({4, 11});

    // Запускаем алгоритм Дейкстры, начиная с вершины 0
    vector<int> distances = dijkstra(graph, 0);

    // Выводим результаты
    cout << "Расстояния от вершины 0:" << endl;
    for (int i = 0; i < distances.size(); ++i) {
        cout << "До вершины " << i << ": ";
        if (distances[i] == numeric_limits<int>::max()) {
            cout << "Бесконечность" << endl;
        } else {
            cout << distances[i] << endl;
        }
    }

    return 0;
}

1.  Структура Edge:  Определяет структуру ребра, содержащую информацию о вершине, в которую ведет ребро (to) и весе ребра (weight).

2.  Функция dijkstra:
    *   Принимает граф (представленный как vector<vector<Edge>>) и стартовую вершину (start_node).
    *   distances: Вектор для хранения кратчайших расстояний от стартовой вершины до каждой вершины графа. Инициализируется значениями "бесконечность" (numeric_limits<int>::max()).  Расстояние от стартовой вершины до себя самой устанавливается равным 0.
    *   priority_queue:  Очередь с приоритетом (pq) используется для выбора вершины для посещения на каждой итерации.  Вершины с меньшим известным расстоянием имеют более высокий приоритет (извлекаются первыми).  Тип pair<int, int> хранит расстояние и номер вершины.  greater<pair<int, int>> обеспечивает, что вершина с наименьшим расстоянием находится в начале очереди.
    *   Алгоритм:
        *   Пока очередь не пуста:
            *   Извлечь вершину с наименьшим расстоянием (current_node).
            *   Если известное расстояние до вершины (current_distance) больше, чем текущее значение в distances, это означает, что мы уже нашли более короткий путь, поэтому пропускаем эту вершину.
            *   Для каждого соседа neighbor текущей вершины:
                *   Если расстояние до neighbor можно улучшить, обновите distances[neighbor] и добавьте neighbor в очередь.
    *   Возвращает вектор distances с кратчайшими расстояниями.

3.  Функция main:
    *   Создает пример графа, используя vector<vector<Edge>>.  Каждый элемент graph[i] - это вектор ребер, исходящих из вершины i.
    *   Вызывает функцию dijkstra для вычисления кратчайших расстояний.
    *   Выводит результаты.  Если расстояние до какой-либо вершины равно "бесконечности", это означает, что до этой вершины нет пути из стартовой вершины.

Как использовать код:

1.  Скопируйте код в файл с расширением .cpp (например, dijkstra.cpp).
2.  Скомпилируйте код с помощью компилятора C++ (например, g++):
    g++ dijkstra.cpp -o dijkstra
3. Запустите исполняемый файл:
       ./dijkstra
   
The code outputs the shortest distances from vertex 0 to all other vertices in the graph. You can change the graph in the main function to test the algorithm on other graphs.

Dijkstra's algorithm is a method of finding the shortest paths from one vertex of a graph to all the others.
How it works: the algorithm builds a route step by step, selecting at each step the vertex with the smallest known distance from the starting vertex and updating the distances to neighboring vertices.

The Dijkstra's algorithm is left to be used in TON Core. 

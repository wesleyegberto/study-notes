# Grafos

Um grafo é uma tupla de:
* conjunto de vértices - V(G)
* conjunto de arestas - E(G)
* função de incidência - Phi(G)

A função de incidência PhiG(e) associa cada aresta com dois vértices do grafo.
Uma aresta é composta por um par de vértices.
Ex.: e = wv ou e = {w,v} ou e = w-v
A aresta e é composta pelos vértices w e v.

Em um grafo dirigido, as arestas tem um início (primeiro vértice) e um fim (segundo vértice).
Ex.: e = wv; onde w é o início e o v é o fim da aresta e.

### Arestas Antiparalelas
Em um grafo dirigido, duas arestas são antiparalelas se o fim de uma é o início da outra.
Ex.: dados e=wv e f=vx, a aresta e termina em v e a aresta f inicia em v.

### Aresta paralelas
Duas arestas são paralelas se ambas são formadas pelos mesmos vérticies, mas num grafo dirigido esses vértices precisar estarem na mesma ordem (início e fim).
Ex.: dados e=wv e f=wv de um grafo dirigido, e e f são paralelos.

### Arestas loops
Arestas compostas por apenas um vértice. Ex.: e=vv

### Grafo Simples
Grafo sem loops e sem arestas paralelas.

### Grafo Completo
Um grafo é completo se dados dois vértices quaisquer eles são adjacentes.

### Grafo Bipartido
Um grafo é bipartido se seus vértices pode ser particionado em dois conjuntos X e Y onde todas as arestas são compostas por um vértice em X e outro em Y.
Um grafo G bipartido é denotado por G[X, Y].

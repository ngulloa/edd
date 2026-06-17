# DFS, BFS y aplicaciones

## Objetivo de aprendizaje

Comprender cuándo usar DFS y cuándo usar BFS en problemas de grafos, cómo se implementan sus variantes principales y qué información adicional debe almacenarse para resolver aplicaciones frecuentes: detección de ciclos, orden topológico, componentes conectadas, componentes fuertemente conectadas, caminos mínimos en grafos no ponderados y puentes.

La meta no es memorizar una única versión del pseudocódigo, sino reconocer qué recorrido corresponde usar según el enunciado y qué campos auxiliares necesita cada variante.

## Idea central

DFS y BFS son recorridos de grafos. Ambos visitan vértices y aristas, pero exploran el grafo con estrategias distintas.

| Técnica | Idea | Estructura auxiliar típica | Sirve especialmente para |
|---|---|---|---|
| DFS | Profundizar por una rama hasta no poder avanzar y luego retroceder. | Recursión o pila. | Ciclos, tiempos, orden topológico, componentes, CFC, puentes. |
| BFS | Explorar por niveles desde un origen. | Cola FIFO. | Distancia mínima en número de aristas, niveles, camino más corto en grafos no ponderados. |

DFS responde preguntas de estructura global del grafo. BFS responde bien preguntas de cercanía desde un origen.

## Antes de partir: vocabulario mínimo

| Concepto | Significado |
|---|---|
| Vértice o nodo | Elemento del grafo. |
| Arista | Relación entre dos vértices. Puede ser dirigida o no dirigida. |
| Vecino de `u` | Vértice alcanzable directamente desde `u`. |
| Lista de adyacencia | Para cada vértice guarda su lista de vecinos. |
| Color blanco | Nodo no visitado. |
| Color gris | Nodo descubierto, pero aún en proceso. |
| Color negro | Nodo terminado: ya se procesaron sus vecinos. |
| `start[u]` | Tiempo en que `u` fue descubierto por DFS. |
| `end[u]` | Tiempo en que `u` fue finalizado por DFS. |
| Padre | Nodo desde el cual se descubrió otro nodo. |
| Grafo transpuesto | Grafo dirigido con las mismas aristas, pero invertidas. |
| Componente conectada | En grafo no dirigido, grupo maximal de nodos conectados entre sí. |
| Componente fuertemente conectada | En grafo dirigido, grupo maximal donde todo nodo puede llegar a todo otro nodo del grupo. |
| Puente | Arista cuya eliminación aumenta la cantidad de componentes conectadas. |

## Cuándo usar DFS o BFS

| Enunciado pide... | Técnica recomendada | Motivo |
|---|---|---|
| Recorrer todo el grafo | DFS o BFS | Ambos sirven; DFS suele ser más simple para aplicaciones estructurales. |
| Saber si hay ciclo dirigido | DFS con colores | Una arista hacia un nodo gris revela ciclo. |
| Ordenar tareas con dependencias | Orden topológico con DFS | Solo funciona si el grafo es dirigido acíclico. |
| Agrupar componentes conectadas | DFS o BFS | Cada recorrido desde un nodo no visitado descubre una componente. |
| Componentes fuertemente conectadas | Kosaraju | Usa DFS con tiempos y grafo transpuesto. |
| Distancia mínima en número de aristas | BFS | Explora por niveles desde el origen. |
| Camino más corto en grafo no ponderado | BFS con padres | La primera vez que se descubre un nodo se obtiene su distancia mínima. |
| Puentes | DFS con `disc` y `low` | Detecta si un subárbol puede volver a un ancestro. |

## Conceptos centrales

### DFS

DFS, o búsqueda en profundidad, avanza por una rama hasta no poder seguir. Cuando llega a un nodo sin vecinos nuevos, retrocede y prueba otras ramas.

La versión básica usa colores:

- blanco: no visitado;
- gris: visitado y en proceso;
- negro: visitado y terminado.

Los colores evitan loops infinitos y permiten distinguir estados relevantes. En grafos dirigidos, encontrar una arista hacia un nodo gris significa que hay un ciclo.

### DFS con tiempos

DFS con tiempos marca dos momentos por cada nodo:

- `start[u]`: cuando `u` se descubre;
- `end[u]`: cuando se termina de explorar todo lo alcanzable desde `u`.

La propiedad importante es que para dos nodos `u` y `v`, sus intervalos `[start[u], end[u]]` son disjuntos o uno está contenido en el otro. Esto permite razonar sobre ancestros, descendientes y orden topológico.

### Orden topológico

Un orden topológico de un grafo dirigido es una secuencia de vértices donde toda arista `(u, v)` deja a `u` antes que `v`.

Solo existe si el grafo dirigido es acíclico. Si hay ciclos, no existe orden topológico válido.

La idea con DFS es insertar cada nodo al inicio de una lista cuando termina. Así, los nodos con mayor `end` quedan antes.

### Kosaraju

Kosaraju encuentra componentes fuertemente conectadas en grafos dirigidos.

La idea tiene tres pasos:

1. Ejecutar DFS sobre `G` para obtener nodos en orden decreciente de término.
2. Construir el grafo transpuesto `G^T`, invirtiendo todas las aristas.
3. Ejecutar DFS sobre `G^T` siguiendo el orden anterior.

Cada llamada DFS en el transpuesto descubre una componente fuertemente conectada.

### BFS

BFS, o búsqueda en anchura, explora por niveles desde un nodo inicial. Primero visita los nodos a distancia 1, luego los de distancia 2, y así sucesivamente.

Por eso usa una cola FIFO. Cuando todas las aristas tienen el mismo costo, BFS entrega distancias mínimas en número de aristas.

BFS no reemplaza a Dijkstra en grafos ponderados. Si las aristas tienen costos distintos, BFS ya no garantiza caminos de costo mínimo, salvo transformaciones especiales explícitas.

### Puentes

En grafos no dirigidos, una arista `(u, v)` es puente si al eliminarla se separa una componente.

DFS permite detectarlo guardando:

- `disc[u]`: tiempo de descubrimiento de `u`;
- `low[u]`: menor tiempo de descubrimiento alcanzable desde `u` usando aristas del árbol DFS y, a lo más, una arista de retroceso.

Si `v` es hijo DFS de `u` y `low[v] > disc[u]`, entonces `(u, v)` es puente.

## Pseudocódigos

### DFS básico

**Input:** grafo `G = (V, E)` representado por listas de adyacencia.  
**Output:** nodos marcados según recorrido DFS.

```text
DFS(G):
    for u in V(G):
        color[u] <- blanco
        parent[u] <- NIL

    for u in V(G):
        if color[u] = blanco:
            DFSVisit(G, u)

DFSVisit(G, u):
    color[u] <- gris

    for v in N(u):
        if color[v] = blanco:
            parent[v] <- u
            DFSVisit(G, v)

    color[u] <- negro
```

### Detección de ciclos en grafo dirigido

**Input:** grafo dirigido `G`.  
**Output:** `true` si existe un ciclo; `false` en caso contrario.

```text
TieneCiclo(G):
    for u in V(G):
        color[u] <- blanco

    for u in V(G):
        if color[u] = blanco:
            if CicloDesde(G, u):
                return true

    return false

CicloDesde(G, u):
    color[u] <- gris

    for v in N(u):
        if color[v] = gris:
            return true
        if color[v] = blanco:
            if CicloDesde(G, v):
                return true

    color[u] <- negro
    return false
```

### DFS con tiempos

**Input:** grafo `G`.  
**Output:** tiempos `start[u]` y `end[u]` para cada vértice.

```text
DFSConTiempos(G):
    tiempo <- 1

    for u in V(G):
        color[u] <- blanco
        parent[u] <- NIL
        start[u] <- 0
        end[u] <- 0

    for u in V(G):
        if color[u] = blanco:
            tiempo <- DFSVisitTiempo(G, u, tiempo)

DFSVisitTiempo(G, u, tiempo):
    color[u] <- gris
    start[u] <- tiempo
    tiempo <- tiempo + 1

    for v in N(u):
        if color[v] = blanco:
            parent[v] <- u
            tiempo <- DFSVisitTiempo(G, v, tiempo)

    color[u] <- negro
    end[u] <- tiempo
    tiempo <- tiempo + 1
    return tiempo
```

### Orden topológico

**Input:** grafo dirigido acíclico `G`.  
**Output:** lista `L` con un orden topológico.

```text
TopSort(G):
    L <- lista vacia
    tiempo <- 1

    for u in V(G):
        start[u] <- 0
        end[u] <- 0

    for u in V(G):
        if start[u] = 0:
            tiempo <- TopDFSVisit(G, u, L, tiempo)

    return L

TopDFSVisit(G, u, L, tiempo):
    start[u] <- tiempo
    tiempo <- tiempo + 1

    for v in N(u):
        if start[v] = 0:
            tiempo <- TopDFSVisit(G, v, L, tiempo)

    end[u] <- tiempo
    insertar u como cabeza de L
    tiempo <- tiempo + 1
    return tiempo
```

### Kosaraju

**Input:** grafo dirigido `G`.  
**Output:** representante de la componente fuertemente conectada de cada nodo.

```text
Kosaraju(G):
    L <- nodos de G ordenados por end decreciente usando DFSConTiempos
    GT <- Transpuesto(G)

    for u in V(G):
        rep[u] <- NIL

    for u in L:
        if rep[u] = NIL:
            Assign(GT, u, u)

    return rep

Assign(GT, u, r):
    if rep[u] != NIL:
        return

    rep[u] <- r

    for v in N_GT(u):
        Assign(GT, v, r)
```

### BFS

**Input:** grafo `G` y vértice inicial `s`.  
**Output:** distancia en número de aristas desde `s` y padre BFS de cada nodo.

```text
BFS(G, s):
    for u in V(G):
        color[u] <- blanco
        dist[u] <- infinito
        parent[u] <- NIL

    color[s] <- gris
    dist[s] <- 0

    Q <- cola vacia
    Enqueue(Q, s)

    while Q no esta vacia:
        u <- Dequeue(Q)

        for v in N(u):
            if color[v] = blanco:
                color[v] <- gris
                dist[v] <- dist[u] + 1
                parent[v] <- u
                Enqueue(Q, v)

        color[u] <- negro
```

### Reconstrucción de camino con BFS

**Input:** arreglo `parent`, origen `s` y destino `t`.  
**Output:** camino desde `s` hasta `t`, si existe.

```text
ReconstruirCamino(parent, s, t):
    if t = s:
        return [s]

    if parent[t] = NIL:
        return NO_HAY_CAMINO

    camino <- ReconstruirCamino(parent, s, parent[t])
    agregar t al final de camino
    return camino
```

### Puentes con DFS

**Input:** grafo no dirigido `G`.  
**Output:** lista de aristas que son puentes.

```text
BridgeDFS(G):
    tiempo <- 0
    puentes <- lista vacia

    for u in V(G):
        visited[u] <- false
        parent[u] <- NIL

    for u in V(G):
        if not visited[u]:
            BridgeVisit(G, u, tiempo, puentes)

    return puentes

BridgeVisit(G, u, tiempo, puentes):
    visited[u] <- true
    disc[u] <- tiempo
    low[u] <- tiempo
    tiempo <- tiempo + 1

    for v in N(u):
        if not visited[v]:
            parent[v] <- u
            BridgeVisit(G, v, tiempo, puentes)
            low[u] <- min(low[u], low[v])

            if low[v] > disc[u]:
                agregar (u, v) a puentes

        else if v != parent[u]:
            low[u] <- min(low[u], disc[v])
```

Nota técnica para Codex: si se implementa en código real, `tiempo` debe pasarse por referencia, envolverse en objeto mutable o manejarse como variable externa para que el incremento persista entre llamadas recursivas. En pseudocódigo académico se puede presentar como contador global.

## Complejidad

Con listas de adyacencia:

| Algoritmo | Complejidad temporal | Memoria auxiliar | Comentario |
|---|---:|---:|---|
| DFS básico | `O(|V| + |E|)` | `O(|V|)` | Visita cada vértice y arista una vez. |
| DFS con tiempos | `O(|V| + |E|)` | `O(|V|)` | Agrega marcas temporales. |
| Detección de ciclos | `O(|V| + |E|)` | `O(|V|)` | Usa colores. |
| TopSort | `O(|V| + |E|)` | `O(|V|)` | Requiere DAG. |
| Kosaraju | `O(|V| + |E|)` | `O(|V| + |E|)` | Requiere construir `G^T`. |
| BFS | `O(|V| + |E|)` | `O(|V|)` | Usa cola FIFO. |
| Puentes con DFS | `O(|V| + |E|)` | `O(|V|)` | Solo para grafos no dirigidos. |

Con matriz de adyacencia, revisar vecinos puede costar `O(|V|)` por nodo, por lo que un recorrido completo suele costar `O(|V|^2)`.

## Ejemplo guiado 1: DFS con colores para detectar ciclo

Grafo dirigido:

```text
A -> B
B -> C
C -> D
D -> B
A -> E
```

Preguntamos si hay ciclo.

1. `A` pasa de blanco a gris.
2. Desde `A` se visita `B`; `B` pasa a gris.
3. Desde `B` se visita `C`; `C` pasa a gris.
4. Desde `C` se visita `D`; `D` pasa a gris.
5. Desde `D` se intenta visitar `B`.
6. `B` está gris, es decir, está en la ruta recursiva actual.
7. Hay ciclo: `B -> C -> D -> B`.

El punto clave no es que `B` ya haya sido visitado; lo importante es que sigue gris. Si estuviera negro, no indicaría ciclo por sí solo.

## Ejemplo guiado 2: BFS para distancia mínima no ponderada

Grafo no ponderado:

```text
A: B, C
B: D
C: D, E
D: F
E: F
F: []
```

Aplicamos `BFS(G, A)`.

| Paso | Cola | Nodo extraído | Descubrimientos nuevos |
|---|---|---|---|
| Inicio | `[A]` | - | `dist[A] = 0` |
| 1 | `[B, C]` | `A` | `B`, `C` con distancia 1 |
| 2 | `[C, D]` | `B` | `D` con distancia 2 |
| 3 | `[D, E]` | `C` | `E` con distancia 2; `D` ya estaba descubierto |
| 4 | `[E, F]` | `D` | `F` con distancia 3 |
| 5 | `[F]` | `E` | `F` ya estaba descubierto |
| 6 | `[]` | `F` | ninguno |

Resultado:

| Nodo | Distancia desde `A` | Padre |
|---|---:|---|
| `A` | 0 | `NIL` |
| `B` | 1 | `A` |
| `C` | 1 | `A` |
| `D` | 2 | `B` |
| `E` | 2 | `C` |
| `F` | 3 | `D` |

BFS garantiza distancia mínima en número de aristas porque procesa todos los nodos de distancia `d` antes que los de distancia `d + 1`.

## Ejemplo guiado 3: orden topológico

Supongamos las dependencias:

```text
Introduccion -> Hash
Introduccion -> Grafos
Grafos -> DFS
DFS -> Kosaraju
DFS -> TopSort
```

Una arista `u -> v` significa: `u` debe estudiarse antes que `v`.

Un orden topológico válido es:

```text
Introduccion, Grafos, DFS, TopSort, Kosaraju, Hash
```

Otro orden también puede ser válido:

```text
Introduccion, Hash, Grafos, DFS, Kosaraju, TopSort
```

No existe necesariamente un único orden topológico.

## Errores típicos

| Error | Corrección |
|---|---|
| Aplicar TopSort a un grafo con ciclos. | Primero verificar que el grafo sea un DAG. |
| Creer que cualquier nodo visitado en DFS implica ciclo. | En grafo dirigido, ciclo se detecta al llegar a un nodo gris, no negro. |
| Usar BFS para rutas más baratas en grafos ponderados. | BFS sirve para mínimo número de aristas si no hay pesos. Para pesos no negativos, usar Dijkstra. |
| Olvidar recorrer desde todos los nodos. | En grafos no necesariamente conexos, el `for u in V(G)` es necesario. |
| Confundir componente conectada con componente fuertemente conectada. | Conectada es para no dirigido; fuertemente conectada exige ida y vuelta en dirigido. |
| Tratar el orden de vecinos como único. | El recorrido puede cambiar según el orden de las listas de adyacencia. |
| Confundir `start` y `end`. | `start` ocurre al descubrir; `end` al terminar todos los vecinos. |
| Creer que Kosaraju usa TopSort real en grafos cíclicos. | Usa orden decreciente por tiempo de término; no es TopSort si hay ciclos. |
| Detectar puentes en grafos dirigidos con el algoritmo de puentes no dirigidos. | La versión estándar de `low` para puentes aplica a grafos no dirigidos. |

## Problema representativo de interrogación

### Enunciado

Una universidad quiere planificar el orden en que un estudiante debe estudiar ciertos contenidos. Cada par `(u, v)` significa que el contenido `u` debe estudiarse antes que `v`.

Se entrega el siguiente grafo dirigido:

```text
A -> B
A -> C
B -> D
C -> D
D -> E
F -> C
```

1. Explica qué algoritmo usarías para decidir si existe un orden válido de estudio.
2. Si existe, entrega un orden válido.
3. Indica qué cambiaría si se agrega la arista `E -> B`.
4. Explica la complejidad.

### Solución esperada

1. El problema se modela como un grafo dirigido de dependencias. Para que exista un orden válido, el grafo debe ser acíclico. Se puede usar DFS con colores para detectar ciclos y luego DFS con tiempos para construir un orden topológico.

2. Un orden válido es:

```text
F, A, C, B, D, E
```

También puede ser válido:

```text
A, F, B, C, D, E
```

Lo importante es respetar todas las aristas: `A` antes que `B` y `C`, `F` antes que `C`, `B` y `C` antes que `D`, y `D` antes que `E`.

3. Si se agrega `E -> B`, aparece un ciclo:

```text
B -> D -> E -> B
```

Entonces no existe orden topológico. El algoritmo de detección de ciclos lo detectaría al encontrar una arista hacia un nodo gris durante DFS.

4. Con listas de adyacencia, tanto la detección de ciclos como TopSort cuestan:

$$
O(|V| + |E|)
$$

porque cada vértice y cada arista se procesa una cantidad constante de veces.

## Preguntas de repaso

1. ¿Por qué BFS entrega distancias mínimas en grafos no ponderados?
2. ¿Qué significa que un nodo esté gris en DFS?
3. ¿Por qué una arista hacia un nodo negro no basta para afirmar que hay ciclo dirigido?
4. ¿Qué condición debe cumplir un grafo para tener orden topológico?
5. ¿Qué diferencia hay entre componente conectada y componente fuertemente conectada?
6. ¿Por qué Kosaraju necesita el grafo transpuesto?
7. ¿Qué representa `low[u]` en el algoritmo de puentes?
8. ¿Qué cambia en complejidad si uso matriz de adyacencia en vez de lista de adyacencia?
9. ¿Por qué el orden topológico no es necesariamente único?
10. ¿Qué información adicional se necesita para reconstruir un camino BFS?

## Ejercicios breves

1. Dado el grafo dirigido `A -> B`, `B -> C`, `C -> A`, ejecuta mentalmente DFS con colores e indica en qué paso se detecta el ciclo.
2. Dado el grafo no ponderado `1: 2,3`, `2: 4`, `3: 4`, `4: 5`, ejecuta BFS desde `1` y entrega `dist` y `parent`.
3. Propón un orden topológico para `A -> C`, `B -> C`, `C -> D`, `B -> E`.
4. Da un ejemplo de grafo dirigido donde cada nodo por separado sea su propia CFC.
5. Da un ejemplo de grafo dirigido donde tres nodos formen una misma CFC.
6. En un grafo no dirigido, explica intuitivamente por qué una arista que pertenece a un ciclo no puede ser puente.
7. Ejecuta DFS en un grafo con dos componentes y explica por qué se necesita el ciclo externo `for u in V(G)`.
8. Explica por qué BFS no debe reemplazar a Dijkstra cuando las aristas tienen pesos diferentes.

## Checklist de estudio

Antes de cerrar esta página, deberías poder:

- [ ] Explicar DFS sin leer pseudocódigo.
- [ ] Explicar BFS sin leer pseudocódigo.
- [ ] Distinguir blanco, gris y negro.
- [ ] Detectar ciclos dirigidos usando colores.
- [ ] Construir un orden topológico si el grafo es DAG.
- [ ] Explicar por qué no hay TopSort si existe un ciclo.
- [ ] Ejecutar BFS y obtener `dist` y `parent`.
- [ ] Reconstruir un camino usando padres.
- [ ] Explicar qué es una CFC.
- [ ] Describir los tres pasos de Kosaraju.
- [ ] Explicar qué es un puente.
- [ ] Usar `disc` y `low` para justificar puentes.
- [ ] Calcular complejidad `O(|V| + |E|)` con listas de adyacencia.
- [ ] Saber cuándo la matriz de adyacencia cambia el costo del recorrido.

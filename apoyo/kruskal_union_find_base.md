# Kruskal, MST y Union-Find

## Objetivo de aprendizaje

Comprender el algoritmo de Kruskal como una estrategia codiciosa para construir un árbol de cobertura mínimo en un grafo no dirigido ponderado, y entender por qué Union-Find permite verificar eficientemente si una arista forma un ciclo.

La meta es poder reconocer cuándo el enunciado pide conectar todos los vértices con costo mínimo, distinguir MST de caminos mínimos, escribir el pseudocódigo de Kruskal y justificar su complejidad usando las operaciones de conjuntos disjuntos.

## Idea central

Kruskal construye un MST desde abajo hacia arriba. Parte con cada vértice como un árbol separado y revisa las aristas desde la más barata hasta la más cara.

Una arista se agrega solo si conecta dos componentes distintas. Si sus extremos ya están en la misma componente, agregarla formaría un ciclo, por lo que se descarta.

| Elemento | Rol en Kruskal |
|---|---|
| Grafo no dirigido ponderado | Estructura donde se busca conectar todos los vértices. |
| Aristas ordenadas por costo | Permiten tomar primero las conexiones más baratas. |
| Bosque parcial `T` | Solución en construcción; al principio tiene `|V|` árboles. |
| Union-Find | Decide si dos vértices ya están conectados en `T`. |
| Ciclo | Razón para rechazar una arista. |
| MST | Árbol final que cubre todos los vértices con costo mínimo. |

## Antes de partir: vocabulario mínimo

| Concepto | Significado |
|---|---|
| Grafo ponderado | Grafo cuyas aristas tienen costo o peso. |
| Grafo no dirigido | Si existe arista entre `u` y `v`, se puede leer en ambos sentidos. |
| Árbol | Grafo conexo y sin ciclos. |
| Árbol de cobertura | Árbol que incluye todos los vértices del grafo. |
| MST | Árbol de cobertura de costo mínimo. En inglés: Minimum Spanning Tree. |
| Bosque | Colección de árboles separados. |
| Componente | Grupo de vértices conectados entre sí. |
| Ciclo | Camino que vuelve a un vértice ya visitado sin repetir aristas de forma trivial. |
| Corte | Separación de los vértices en dos grupos no vacíos. |
| Arista de corte | Arista que conecta ambos lados de un corte. |
| Conjunto disjunto | Estructura que mantiene particiones sin intersección entre sí. |
| Representante | Elemento usado para identificar un conjunto. |
| `MakeSet(x)` | Crea un conjunto que contiene solo a `x`. |
| `Find(x)` | Retorna el representante del conjunto de `x`. |
| `Union(x, y)` | Une los conjuntos que contienen a `x` e `y`. |
| Compresión de caminos | Optimización de `Find` que acorta rutas hacia el representante. |
| Unión por rango | Optimización de `Union` que evita árboles muy altos. |

## Cuándo usar Kruskal

Usa Kruskal cuando el problema pide conectar elementos con costo mínimo y se puede modelar como un grafo no dirigido con pesos en las aristas.

| Señal en el enunciado | Interpretación |
|---|---|
| “Conectar todos los nodos al menor costo” | Problema de MST. |
| “Red de cables/carreteras/tuberías mínima” | Cada conexión puede ser una arista ponderada. |
| “Evitar ciclos” | Se necesita construir un árbol o bosque. |
| “Grafo no dirigido ponderado” | Kruskal o Prim son candidatos naturales. |
| “Aristas disponibles con costos” | Kruskal suele ser directo porque ordena aristas. |
| “El grafo no es conexo” | Kruskal entrega un bosque mínimo, no un único MST. |

No uses Kruskal si el problema pide camino más corto desde un origen. Eso corresponde a BFS si el grafo no es ponderado o a Dijkstra si tiene pesos no negativos.

## Conceptos centrales

### MST

Un árbol de cobertura mínimo de un grafo `G = (V, E)` es un subconjunto de aristas que cumple tres condiciones:

1. Cubre todos los vértices de `V`.
2. No tiene ciclos.
3. Tiene costo total mínimo entre todos los árboles que cubren `V`.

Si el grafo es conexo, un MST tiene exactamente:

$$
|V| - 1
$$

aristas.

Si ya se han agregado `|V| - 1` aristas sin formar ciclos, el árbol está completo. Cualquier arista adicional formaría un ciclo.

### Kruskal como algoritmo codicioso

Kruskal es codicioso porque en cada paso toma una decisión local definitiva: revisa la arista más barata disponible que aún no se ha procesado.

La decisión local no es simplemente “agregar la arista más barata”. Es:

> Agregar la arista más barata que no forme ciclo con las aristas ya elegidas.

Esta condición es clave. Una arista barata puede ser rechazada si conecta vértices que ya están en la misma componente.

### Bosque parcial

Al inicio, Kruskal tiene `|V|` árboles separados: un árbol por cada vértice. A medida que agrega aristas, une árboles distintos.

Cada arista aceptada reduce en 1 la cantidad de componentes del bosque.

| Momento | Cantidad de componentes |
|---|---:|
| Inicio | `|V|` |
| Después de aceptar 1 arista | `|V| - 1` |
| Después de aceptar `k` aristas | `|V| - k` |
| MST completo | `1` |

### Por qué Union-Find evita ciclos

En el bosque parcial `T`, dos vértices están en el mismo conjunto si ya existe un camino entre ellos usando solo aristas aceptadas.

Para una arista `(u, v)`:

- si `Find(u) = Find(v)`, entonces `u` y `v` ya están conectados; agregar `(u, v)` formaría un ciclo;
- si `Find(u) != Find(v)`, entonces conecta dos componentes distintas; se puede agregar y luego unir sus conjuntos.

## Algoritmo principal

Kruskal tiene tres fases:

1. Crear un conjunto disjunto por cada vértice.
2. Ordenar las aristas por costo creciente.
3. Recorrer las aristas ordenadas y agregar solo las que unen componentes distintas.

## Pseudocódigos

### Kruskal

**Input:** grafo no dirigido ponderado `G = (V, E)` con función de costo `w(e)`.  
**Output:** conjunto `T` de aristas del MST, o bosque mínimo si el grafo no es conexo.

```text
Kruskal(G):
    T <- empty

    for v in V(G):
        MakeSet(v)

    ordenar E(G) por costo creciente

    for (u, v) in E(G):
        if Find(u) != Find(v):
            T <- T union {(u, v)}
            Union(u, v)

    return T
```

### MakeSet

**Input:** elemento `x`.  
**Output:** conjunto singleton `{x}` representado por `parent` y `rank`.

```text
MakeSet(x):
    parent[x] <- x
    rank[x] <- 0
```

### Find con compresión de caminos

**Input:** elemento `x`.  
**Output:** representante del conjunto que contiene a `x`.

```text
Find(x):
    if parent[x] != x:
        parent[x] <- Find(parent[x])
    return parent[x]
```

### Union por rango

**Input:** elementos `x` e `y`.  
**Output:** unión de los conjuntos que contienen a `x` e `y`.

```text
Union(x, y):
    rx <- Find(x)
    ry <- Find(y)

    if rx = ry:
        return

    if rank[rx] < rank[ry]:
        parent[rx] <- ry
    else if rank[rx] > rank[ry]:
        parent[ry] <- rx
    else:
        parent[ry] <- rx
        rank[rx] <- rank[rx] + 1
```

## Complejidad

La complejidad de Kruskal se analiza por partes.

| Parte | Costo |
|---|---:|
| Crear conjuntos singleton | `O(|V|)` |
| Ordenar aristas | `O(|E| log |E|)` |
| Consultas `Find` | `O(|E| α(|V|))` amortizado |
| Uniones `Union` | `O(|V| α(|V|))` amortizado |
| Total usual | `O(|E| log |E|)` |

Con compresión de caminos y unión por rango, las operaciones de Union-Find son casi constantes amortizadas. La función `α(n)` es la inversa de Ackermann y crece extremadamente lento.

Como en un grafo simple se cumple `|E| <= |V|^2`, también se suele escribir:

$$
O(|E| \log |V|)
$$

porque:

$$
\log |E| \in O(\log |V|)
$$

### Caso con pesos enteros en rango pequeño

Si los costos de las aristas son enteros en un rango acotado, por ejemplo de `1` a `|V|`, puede usarse Counting Sort para ordenar las aristas en tiempo lineal respecto de `|E| + |V|`.

En ese caso, si el grafo es conexo y `|V| = O(|E|)`, la ordenación deja de dominar y el costo queda cercano a:

$$
O(|E|\alpha(|V|))
$$

Este tipo de modificación es típico de preguntas de interrogación: no cambia Kruskal, cambia la forma de ordenar las aristas.

## Ejemplo guiado

Considera este grafo no dirigido ponderado:

| Arista | Costo |
|---|---:|
| `(A, B)` | 1 |
| `(B, C)` | 2 |
| `(C, D)` | 3 |
| `(A, C)` | 4 |
| `(D, E)` | 5 |
| `(C, E)` | 6 |
| `(E, F)` | 7 |
| `(B, D)` | 8 |

Las aristas ya están ordenadas de menor a mayor costo.

| Paso | Arista | Decisión | Razón | `T` acumulado |
|---:|---|---|---|---|
| 1 | `(A, B)` | Aceptar | `A` y `B` están en conjuntos distintos. | `{(A,B)}` |
| 2 | `(B, C)` | Aceptar | Une la componente `{A,B}` con `C`. | `{(A,B),(B,C)}` |
| 3 | `(C, D)` | Aceptar | Une `{A,B,C}` con `D`. | `{(A,B),(B,C),(C,D)}` |
| 4 | `(A, C)` | Rechazar | `A` y `C` ya están conectados; formaría ciclo. | sin cambio |
| 5 | `(D, E)` | Aceptar | Une `{A,B,C,D}` con `E`. | `{(A,B),(B,C),(C,D),(D,E)}` |
| 6 | `(C, E)` | Rechazar | `C` y `E` ya están conectados; formaría ciclo. | sin cambio |
| 7 | `(E, F)` | Aceptar | Une `{A,B,C,D,E}` con `F`. | `{(A,B),(B,C),(C,D),(D,E),(E,F)}` |

Como hay 6 vértices, el MST debe tener `6 - 1 = 5` aristas. Al aceptar `(E, F)`, Kruskal puede terminar.

Costo total:

$$
1 + 2 + 3 + 5 + 7 = 18
$$

## Diagrama conceptual sugerido

```{mermaid}
flowchart LR
    A["Aristas ordenadas por costo"] --> B["Tomar siguiente arista"]
    B --> C{"¿Find(u) != Find(v)?"}
    C -->|Sí| D["Agregar arista a T"]
    D --> E["Union(u, v)"]
    C -->|No| F["Descartar: formaría ciclo"]
    E --> G{"¿T tiene |V|-1 aristas?"}
    F --> G
    G -->|No| B
    G -->|Sí| H["MST completo"]
```

## Comparación con Prim, Dijkstra y BFS

| Algoritmo | Resuelve | No confundir con |
|---|---|---|
| Kruskal | MST en grafo no dirigido ponderado. | Camino más corto desde un origen. |
| Prim | MST en grafo no dirigido ponderado. | Dijkstra, aunque ambos usan una prioridad. |
| Dijkstra | Caminos más cortos desde un origen con pesos no negativos. | MST. |
| BFS | Caminos mínimos en número de aristas en grafo no ponderado. | MST o rutas ponderadas. |

Un MST minimiza el costo total de conectar todos los vértices. No garantiza que el camino entre dos vértices dentro del árbol sea el camino más corto en el grafo original.

## Errores típicos

| Error | Corrección |
|---|---|
| Usar Kruskal para caminos mínimos. | Kruskal construye MST; para caminos mínimos usar BFS o Dijkstra según corresponda. |
| Agregar toda arista barata sin verificar ciclo. | Solo se agrega si conecta componentes distintas. |
| Aplicar Kruskal a un grafo dirigido sin justificar transformación. | MST estándar se define en grafos no dirigidos. |
| Olvidar ordenar las aristas. | La elección codiciosa depende del orden creciente de costos. |
| Confundir `Find` con búsqueda en grafo. | `Find` solo retorna el representante de un conjunto disjunto. |
| No inicializar con `MakeSet` para cada vértice. | Cada vértice debe partir como componente singleton. |
| Pensar que Union-Find reconstruye caminos. | Union-Find responde conectividad de componentes, no rutas. |
| Decir que el MST siempre es único. | Solo se garantiza unicidad bajo condiciones adicionales, por ejemplo costos todos distintos. |

## Problema representativo de interrogación

### Enunciado

Considera el algoritmo de Kruskal para encontrar un árbol de cobertura mínimo en un grafo no dirigido ponderado `G = (V, E)`.

1. Explica para qué se usa Union-Find dentro de Kruskal.
2. Calcula la complejidad usual de Kruskal si se ordenan las aristas con un algoritmo de comparación.
3. Supón ahora que todos los costos de las aristas son enteros en el rango `1` a `|V|`. ¿Cómo cambia la complejidad si se usa Counting Sort para ordenar las aristas?

### Solución esperada

1. Union-Find se usa para saber si los extremos de una arista ya están en la misma componente del bosque parcial. Si `Find(u) = Find(v)`, agregar `(u, v)` formaría ciclo. Si son distintos, la arista se acepta y luego se ejecuta `Union(u, v)`.

2. La complejidad usual se separa en:

| Parte | Costo |
|---|---:|
| Inicialización | `O(|V|)` |
| Ordenación de aristas | `O(|E| log |E|)` |
| Operaciones Union-Find | `O(|E|α(|V|))` amortizado |

El término dominante usual es la ordenación, por lo que:

$$
O(|E|\log |E|)
$$

3. Si los costos son enteros en `1, ..., |V|`, las aristas pueden ordenarse con Counting Sort en:

$$
O(|E| + |V|)
$$

Si el grafo es conexo, `|V| = O(|E|)`, por lo que ordenar cuesta `O(|E|)`. El costo dominante pasa a ser el procesamiento con Union-Find:

$$
O(|E|\alpha(|V|))
$$

La idea clave es que no se cambia la lógica de Kruskal. Solo se reemplaza la etapa de ordenación.

## Preguntas de repaso

1. ¿Por qué Kruskal parte con un bosque y no con un árbol?
2. ¿Qué significa que una arista forme ciclo dentro del bosque parcial?
3. ¿Por qué `Find(u) = Find(v)` implica que no se debe agregar `(u, v)`?
4. ¿Cuántas aristas debe tener un MST si el grafo tiene `n` vértices?
5. ¿Qué parte domina la complejidad usual de Kruskal?
6. ¿Cuándo puede mejorar la etapa de ordenación de Kruskal?
7. ¿Por qué MST no es lo mismo que caminos mínimos?
8. ¿Qué ocurre si el grafo no es conexo?
9. ¿Qué diferencia hay entre representante, padre y rango en Union-Find?
10. ¿Qué optimización realiza la compresión de caminos?

## Ejercicios breves

1. Dado un conjunto de aristas ponderadas, ordénalas y simula Kruskal paso a paso indicando cuáles se aceptan y cuáles se rechazan.
2. Para cada arista procesada, escribe el resultado conceptual de `Find(u)` y `Find(v)`.
3. Construye un contraejemplo donde el MST no contenga el camino más corto entre dos vértices.
4. Explica qué pasaría si Kruskal no usara ninguna estructura para detectar ciclos.
5. Indica si las siguientes frases son verdaderas o falsas:
   - “Kruskal sirve para grafos dirigidos sin modificación”.
   - “Si una arista es barata, siempre se agrega”.
   - “Union-Find permite saber si dos vértices están en la misma componente”.
   - “Si todos los costos son distintos, el MST es único”.
   - “Un MST minimiza el costo total del árbol, no cada ruta individual”.

## Checklist de estudio

- [ ] Sé definir MST con mis palabras.
- [ ] Sé explicar por qué Kruskal es codicioso.
- [ ] Sé simular Kruskal con una tabla de aristas ordenadas.
- [ ] Sé indicar cuándo una arista se acepta o se rechaza.
- [ ] Sé escribir `MakeSet`, `Find`, `Union` y `Kruskal`.
- [ ] Sé explicar qué representa cada conjunto disjunto durante Kruskal.
- [ ] Sé calcular la complejidad usual `O(|E| log |E|)`.
- [ ] Sé justificar cuándo la ordenación puede mejorar con Counting Sort.
- [ ] Sé diferenciar Kruskal, Prim, Dijkstra y BFS.
- [ ] Sé explicar por qué MST no es caminos mínimos.

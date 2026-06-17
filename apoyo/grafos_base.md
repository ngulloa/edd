# Base conceptual — Grafos: conceptos y representación

## Título sugerido

Grafos: conceptos y representación

## Objetivo de aprendizaje

Al terminar esta página, el estudiante debería ser capaz de:

- reconocer cuándo un enunciado debe modelarse como grafo;
- distinguir vértices, aristas, caminos, ciclos y componentes;
- decidir si conviene usar un grafo dirigido o no dirigido;
- decidir si conviene usar lista o matriz de adyacencia;
- construir un grafo desde dependencias, rutas, relaciones o conexiones;
- explicar la complejidad de operaciones básicas según la representación elegida.

## Idea central

Un grafo modela relaciones entre objetos. Los objetos son vértices y las relaciones son aristas. La clave en una evaluación no es solo saber la definición formal, sino traducir un enunciado a:

$$
G = (V, E)
$$

donde:

- $V$ es el conjunto de vértices;
- $E$ es el conjunto de aristas.

Si la relación tiene dirección, se usa un grafo dirigido. Si la relación es simétrica, se usa un grafo no dirigido.

::: {.callout-important}
## Idea central

Un grafo no es una estructura para buscar por llave como una tabla de hash. Un grafo sirve para modelar relaciones: caminos, dependencias, conectividad, ciclos, componentes y rutas.
:::

## Antes de partir: vocabulario mínimo

| Concepto | Explicación práctica |
|---|---|
| Vértice o nodo | Objeto del problema: tarea, curso, ciudad, persona, celda, estado. |
| Arista | Relación entre dos vértices. |
| Grafo dirigido | La arista tiene dirección: $(u, v)$ permite ir desde $u$ hacia $v$. |
| Grafo no dirigido | La arista no tiene dirección: $\{u, v\}$ conecta ambos sentidos. |
| Grafo ponderado | Cada arista tiene un peso o costo. |
| Camino | Secuencia de vértices conectados por aristas. |
| Ciclo | Camino no vacío que vuelve al vértice inicial. |
| Vecino | Vértice conectado directamente por una arista. |
| Grado | Cantidad de aristas incidentes a un vértice. |
| Grado de entrada | En grafo dirigido, cantidad de aristas que llegan a un vértice. |
| Grado de salida | En grafo dirigido, cantidad de aristas que salen de un vértice. |
| Lista de adyacencia | Para cada vértice, se guarda una lista de sus vecinos. |
| Matriz de adyacencia | Celda $A[i][j]$ indica si existe la arista de $i$ a $j$. |
| Componente conectada | Grupo de vértices conectados entre sí en un grafo no dirigido. |
| Componente fuertemente conectada | En un grafo dirigido, grupo donde todos pueden llegar a todos. |
| DAG | Grafo dirigido acíclico. Se usa en dependencias y orden topológico. |

## Cuándo usar grafos

Un enunciado suele pedir grafos aunque no use la palabra “grafo”. Algunas señales son:

| Señal del enunciado | Modelo natural |
|---|---|
| “A depende de B” | Grafo dirigido con arista $B \to A$. |
| “Hay ruta entre ciudades” | Grafo dirigido o no dirigido según el sentido de la ruta. |
| “Conectar todos con costo mínimo” | Grafo no dirigido ponderado; MST. |
| “Detectar circularidad” | Grafo dirigido y detección de ciclos. |
| “Agrupar personas conectadas” | Componentes conectadas. |
| “Encontrar si dos elementos están conectados” | Grafo o Union-Find, según si hay que recorrer o solo unir/consultar. |
| “Ordenar tareas con requisitos” | Grafo dirigido acíclico y orden topológico. |
| “Todos pueden llegar a todos dentro del grupo” | Componentes fuertemente conectadas. |

::: {.callout-tip}
## Consejo de estudio

Antes de pensar en DFS, BFS, Kruskal o Dijkstra, primero escribe qué son los vértices y qué son las aristas. Si esa traducción está mal, el algoritmo posterior también estará mal.
:::

## Tipos de grafos

### Grafo dirigido

Se usa cuando la relación tiene sentido.

Ejemplos:

- requisitos entre tareas;
- calles de un solo sentido;
- enlaces web;
- “A debe ejecutarse antes que B”.

Si una tarea `B` requiere haber hecho `A`, la arista correcta es:

$$
A \to B
$$

porque desde el requisito se apunta hacia la tarea que queda habilitada.

### Grafo no dirigido

Se usa cuando la relación es simétrica.

Ejemplos:

- amistad mutua;
- cable físico entre dos computadores;
- caminos de doble sentido;
- relación “están conectados”.

La arista se escribe como:

$$
\{u, v\}
$$

No hay origen ni destino.

### Grafo ponderado

Se usa cuando las aristas tienen costo, distancia, tiempo o peso.

Ejemplo:

$$
(u, v, 7)
$$

puede significar que ir de $u$ a $v$ cuesta 7 unidades.

::: {.callout-warning}
## Error típico

No todo grafo con pesos pide caminos mínimos. Si el objetivo es conectar todos los vértices con costo mínimo, el problema es de árbol de cobertura mínima. Si el objetivo es llegar desde un origen a otros vértices con menor costo, el problema es de caminos mínimos.
:::

## Representaciones de grafos

### Lista de adyacencia

Una lista de adyacencia guarda, para cada vértice, sus vecinos.

Ejemplo para un grafo dirigido:

```text
0: [1, 2]
1: [2, 3]
2: [4]
3: []
4: []
```

Esto representa las aristas:

$$
(0,1), (0,2), (1,2), (1,3), (2,4)
$$

Ventaja principal: recorrer vecinos es eficiente.

### Matriz de adyacencia

Una matriz de adyacencia usa una tabla $V \times V$.

Si existe la arista $(i,j)$, entonces:

$$
A[i][j] = 1
$$

Si no existe:

$$
A[i][j] = 0
$$

Ejemplo:

| Desde / hacia | 0 | 1 | 2 | 3 | 4 |
|---|---:|---:|---:|---:|---:|
| 0 | 0 | 1 | 1 | 0 | 0 |
| 1 | 0 | 0 | 1 | 1 | 0 |
| 2 | 0 | 0 | 0 | 0 | 1 |
| 3 | 0 | 0 | 0 | 0 | 0 |
| 4 | 0 | 0 | 0 | 0 | 0 |

Ventaja principal: consultar si existe una arista específica es $O(1)$.

## Lista vs matriz de adyacencia

| Operación o criterio | Lista de adyacencia | Matriz de adyacencia |
|---|---:|---:|
| Memoria | $O(V + E)$ | $O(V^2)$ |
| Recorrer vecinos de $u$ | $O(\deg(u))$ | $O(V)$ |
| Consultar si existe $(u,v)$ | $O(\deg(u))$ si la lista no usa hash/set | $O(1)$ |
| Insertar arista | $O(1)$ si se agrega al final | $O(1)$ |
| Mejor uso típico | Grafos dispersos, DFS, BFS | Grafos densos o muchas consultas de existencia |

::: {.callout-note}
## Intuición

Si hay pocos vecinos por nodo, la lista de adyacencia evita revisar muchos ceros. Si se necesita preguntar muchas veces “¿existe la arista $(u,v)$?”, la matriz puede ser más conveniente.
:::

## Diagrama conceptual

```{mermaid}
flowchart LR
    Enunciado["Enunciado"] --> V["Definir vértices"]
    V --> E["Definir aristas"]
    E --> Tipo["Dirigido, no dirigido o ponderado"]
    Tipo --> Rep["Elegir representación"]
    Rep --> Alg["Elegir algoritmo"]
```

## Construcción de grafos desde dependencias

Si una tarea `T` tiene como requisito `R`, entonces `R` debe ocurrir antes que `T`. Por eso se agrega la arista:

$$
R \to T
$$

Ejemplo:

| Tarea | Requisitos |
|---|---|
| A | ninguno |
| B | A |
| C | A |
| D | B, C |
| E | D |

Aristas:

$$
A \to B,\quad A \to C,\quad B \to D,\quad C \to D,\quad D \to E
$$

Lista de adyacencia:

```text
A: [B, C]
B: [D]
C: [D]
D: [E]
E: []
```

## Pseudocódigo claro

### Construir grafo desde dependencias

**Input:** conjunto de tareas; para cada tarea, lista de requisitos.  
**Output:** grafo dirigido $G = (V,E)$ donde cada arista va desde requisito hacia tarea.

```text
BuildGraph(tareas):
    V <- conjunto de todas las tareas
    E <- empty

    for tarea T in tareas:
        for requisito R in requisitos[T]:
            agregar arista (R, T) a E

    return G = (V, E)
```

### Agregar arista en lista de adyacencia

**Input:** lista de adyacencia `Adj`, vértices `u`, `v`, booleano `dirigido`.  
**Output:** `Adj` actualizado con la arista.

```text
AddEdgeList(Adj, u, v, dirigido):
    agregar v a Adj[u]

    if not dirigido:
        agregar u a Adj[v]
```

### Agregar arista en matriz de adyacencia

**Input:** matriz `A`, vértices `u`, `v`, booleano `dirigido`.  
**Output:** matriz actualizada con la arista.

```text
AddEdgeMatrix(A, u, v, dirigido):
    A[u][v] <- 1

    if not dirigido:
        A[v][u] <- 1
```

### Consultar existencia de arista en matriz

**Input:** matriz de adyacencia `A`, vértices `u`, `v`.  
**Output:** `true` si existe la arista de `u` a `v`; `false` en caso contrario.

```text
HasEdgeMatrix(A, u, v):
    return A[u][v] = 1
```

### Consultar existencia de arista en lista

**Input:** lista de adyacencia `Adj`, vértices `u`, `v`.  
**Output:** `true` si `v` aparece en la lista de vecinos de `u`.

```text
HasEdgeList(Adj, u, v):
    for x in Adj[u]:
        if x = v:
            return true

    return false
```

### Construir grafo transpuesto

El grafo transpuesto $G^T$ invierte todas las aristas de un grafo dirigido.

**Input:** grafo dirigido $G = (V,E)$.  
**Output:** grafo transpuesto $G^T$.

```text
Transpose(G):
    V_T <- V(G)
    E_T <- empty

    for (u, v) in E(G):
        agregar arista (v, u) a E_T

    return G_T = (V_T, E_T)
```

## Complejidad

La complejidad en grafos se expresa usualmente en función de:

- $V$: cantidad de vértices;
- $E$: cantidad de aristas.

| Procedimiento | Con lista de adyacencia | Con matriz de adyacencia |
|---|---:|---:|
| Crear estructura vacía | $O(V)$ | $O(V^2)$ |
| Agregar todas las aristas | $O(E)$ | $O(E)$ |
| Recorrer todos los vecinos de todos los nodos | $O(V + E)$ | $O(V^2)$ |
| Preguntar si existe una arista específica | $O(\deg(u))$ | $O(1)$ |
| Transponer grafo | $O(V + E)$ | $O(V^2)$ si se copia explícitamente |

::: {.callout-warning}
## Error típico

No se puede decir que un algoritmo sobre grafos es simplemente $O(n)$ sin aclarar qué es $n$. En grafos, normalmente se usa $V$ para vértices y $E$ para aristas.
:::

## Ejemplo guiado

### Enunciado

Un sistema tiene cinco tareas:

- `Diseño` no tiene requisitos.
- `BaseDatos` requiere `Diseño`.
- `Backend` requiere `Diseño` y `BaseDatos`.
- `Frontend` requiere `Diseño`.
- `Deploy` requiere `Backend` y `Frontend`.

Se quiere representar el problema para luego decidir si las tareas pueden ordenarse sin circularidad.

### Paso 1: vértices

Cada tarea es un vértice:

$$
V = \{Diseño, BaseDatos, Backend, Frontend, Deploy\}
$$

### Paso 2: aristas

Si `X` es requisito de `Y`, agregamos:

$$
X \to Y
$$

Entonces:

```text
Diseño -> BaseDatos
Diseño -> Backend
BaseDatos -> Backend
Diseño -> Frontend
Backend -> Deploy
Frontend -> Deploy
```

### Paso 3: lista de adyacencia

```text
Diseño: [BaseDatos, Backend, Frontend]
BaseDatos: [Backend]
Backend: [Deploy]
Frontend: [Deploy]
Deploy: []
```

### Paso 4: interpretación

Este grafo es dirigido porque las dependencias tienen sentido. Si hubiera una arista `Deploy -> Diseño`, aparecería una dependencia circular y no sería posible ejecutar las tareas en un orden válido.

```{mermaid}
flowchart LR
    Diseno["Diseño"] --> BD["BaseDatos"]
    Diseno --> Backend["Backend"]
    Diseno --> Frontend["Frontend"]
    BD --> Backend
    Backend --> Deploy["Deploy"]
    Frontend --> Deploy
```

## Errores típicos

| Error | Por qué está mal | Corrección |
|---|---|---|
| Invertir las aristas de dependencia | Cambia el significado del orden. | Si `B` requiere `A`, usar $A \to B$. |
| Usar grafo no dirigido para requisitos | La relación no es simétrica. | Usar grafo dirigido. |
| Usar matriz para grafo enorme y disperso sin justificar | Puede desperdiciar memoria $O(V^2)$. | Usar lista si hay pocas aristas por nodo. |
| Confundir componente conectada con CFC | En dirigidos importa poder ir y volver. | Usar CFC solo si la alcanzabilidad es mutua. |
| Confundir MST con caminos mínimos | Son objetivos distintos. | MST conecta todo; caminos mínimos optimizan rutas desde origen. |
| Decir “hay ciclo” sin aclarar si el grafo es dirigido | Los ciclos tienen significado distinto según el tipo de grafo. | Explicitar si el grafo es dirigido o no dirigido. |

## Problema representativo de interrogación

<details>
<summary>Ver enunciado</summary>

Una empresa debe instalar módulos de software. Cada módulo puede depender de otros módulos:

| Módulo | Depende de |
|---|---|
| A | — |
| B | A |
| C | A |
| D | B, C |
| E | D |
| F | C |

1. Modela el problema como un grafo.
2. Indica si debe ser dirigido o no dirigido.
3. Escribe la lista de adyacencia.
4. Indica qué representación conviene si hay 100.000 módulos y cada módulo depende de a lo más 4 módulos.
5. Explica qué significaría encontrar un ciclo.

</details>

<details>
<summary>Ver solución</summary>

1. Cada módulo es un vértice:

$$
V = \{A, B, C, D, E, F\}
$$

2. El grafo debe ser dirigido, porque “depender de” tiene sentido. Si `B` depende de `A`, entonces `A` debe instalarse antes que `B`, por lo tanto se agrega:

$$
A \to B
$$

3. Aristas:

$$
A \to B,\quad A \to C,\quad B \to D,\quad C \to D,\quad D \to E,\quad C \to F
$$

Lista de adyacencia:

```text
A: [B, C]
B: [D]
C: [D, F]
D: [E]
E: []
F: []
```

4. Conviene lista de adyacencia. Hay muchos módulos, pero cada uno tiene pocas dependencias. La matriz usaría $O(V^2)$ memoria, mientras que la lista usa $O(V + E)$.

5. Un ciclo significa una dependencia circular. Por ejemplo, si además `A` dependiera de `E`, tendríamos:

```text
A -> B -> D -> E -> A
```

Eso haría imposible instalar los módulos en un orden válido, porque cada uno espera a otro dentro del ciclo.

</details>

## Preguntas de repaso

1. ¿Qué debe ser vértice y qué debe ser arista en un problema de requisitos?
2. Si una arista representa una calle de un solo sentido, ¿el grafo es dirigido o no dirigido?
3. ¿Por qué una matriz de adyacencia puede ser mala opción en grafos dispersos?
4. ¿Qué operación es más rápida en una matriz: consultar si existe una arista o recorrer solo los vecinos reales?
5. ¿Qué significa que un grafo dirigido sea acíclico?
6. ¿Cuál es la diferencia entre componente conectada y componente fuertemente conectada?
7. ¿Por qué no conviene elegir el algoritmo antes de modelar el grafo?

## Ejercicios breves

1. Modela como grafo una malla curricular donde algunos cursos son prerrequisitos de otros.
2. Para el grafo con aristas $(0,1)$, $(0,2)$, $(2,3)$ y $(3,1)$, escribe la lista de adyacencia.
3. Para el mismo grafo, escribe la matriz de adyacencia.
4. Decide si usarías lista o matriz en un grafo con 1.000 vértices y 900.000 aristas.
5. Decide si usarías lista o matriz en un grafo con 100.000 vértices y grado máximo 4.
6. Da un ejemplo de relación que deba ser no dirigida.
7. Da un ejemplo de relación que deba ser dirigida.
8. Explica qué arista agregarías si la tarea `T` requiere la tarea `R`.

## Checklist de estudio

- [ ] Puedo definir un grafo como $G = (V,E)$.
- [ ] Puedo identificar vértices y aristas desde un enunciado.
- [ ] Distingo grafo dirigido y no dirigido.
- [ ] Distingo grafo ponderado y no ponderado.
- [ ] Sé cuándo usar lista de adyacencia.
- [ ] Sé cuándo usar matriz de adyacencia.
- [ ] Puedo construir un grafo desde dependencias.
- [ ] Sé que en dependencias la arista va desde requisito hacia tarea.
- [ ] Puedo explicar qué significa un ciclo en un grafo de dependencias.
- [ ] No confundo MST con caminos mínimos.
- [ ] No confundo componente conectada con componente fuertemente conectada.
- [ ] Expreso complejidades usando $V$ y $E$.

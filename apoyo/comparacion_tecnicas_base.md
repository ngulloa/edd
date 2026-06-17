# Comparación de técnicas

## Objetivo de aprendizaje

Aprender a reconocer qué estructura de datos o técnica algorítmica corresponde usar frente a un enunciado de evaluación, justificando la elección por el tipo de operación requerida, el modelo del problema y la complejidad esperada.

La meta de esta página no es repetir todas las páginas anteriores, sino construir una guía de decisión: leer un enunciado, identificar sus señales, elegir una técnica candidata, descartar alternativas incorrectas y explicar la complejidad.

## Idea central

En la evaluación, el punto no suele ser memorizar pseudocódigo de forma mecánica, sino reconocer qué problema se tiene delante.

Cada técnica responde a una pregunta distinta:

| Pregunta del enunciado | Técnica o estructura probable | Razón |
|---|---|---|
| ¿Necesito buscar por llave? | Tabla de hash | Acceso esperado eficiente por clave. |
| ¿Necesito obtener mínimo o máximo repetidamente? | Heap | Acceso eficiente a prioridad. |
| ¿Necesito respetar orden de llegada? | Cola FIFO | El primero en llegar debe salir primero. |
| ¿Necesito asignar valores bajo restricciones? | Backtracking / CSP | Hay variables, dominios y restricciones. |
| ¿Necesito encontrar todas las configuraciones? | Backtracking | Se debe explorar el espacio de soluciones. |
| ¿Necesito tomar decisiones locales definitivas? | Greedy | Puede existir una elección segura. |
| ¿Necesito optimizar con subproblemas repetidos? | Programación dinámica | Hay recurrencia y subproblemas solapados. |
| ¿Necesito modelar relaciones, rutas o dependencias? | Grafo | Los objetos son vértices y las relaciones son aristas. |
| ¿Necesito recorrer niveles o distancias no ponderadas? | BFS | Explora por capas. |
| ¿Necesito detectar ciclos, tiempos o componentes? | DFS | Explora profundidad y estructura del grafo. |
| ¿Necesito conectar todo con costo mínimo? | Kruskal o Prim | Es un problema de MST. |
| ¿Necesito saber si dos elementos están conectados en un bosque? | Union-Find | Mantiene componentes disjuntas. |

## Antes de partir: vocabulario mínimo

| Concepto | Significado para decidir técnica |
|---|---|
| Llave | Identificador usado para acceder a un valor. Sugiere hash. |
| Prioridad | Criterio para extraer máximo o mínimo. Sugiere heap. |
| Restricción | Condición que una asignación no puede romper. Sugiere CSP/backtracking. |
| Dominio | Valores posibles para una variable. Es central en backtracking. |
| Estado parcial | Asignación incompleta que puede extenderse o descartarse. |
| Elección local | Decisión tomada con información actual. Sugiere greedy, pero debe justificarse. |
| Subproblema | Problema más pequeño dentro del problema original. |
| Subproblemas solapados | Los mismos subproblemas aparecen varias veces. Sugiere DP. |
| Recurrencia | Fórmula que conecta subproblemas. Es la base de DP. |
| Grafo | Modelo de relaciones entre objetos. |
| DAG | Grafo dirigido acíclico. Sugiere orden topológico. |
| MST | Árbol de cobertura mínimo. Sugiere Kruskal o Prim. |
| Camino mínimo | Ruta de menor costo desde un origen. Sugiere BFS o Dijkstra, no Kruskal. |
| Componente | Grupo de vértices conectados. Puede requerir DFS, BFS o Union-Find. |

## Conceptos centrales

### 1. La estructura se elige por la operación dominante

No todas las estructuras sirven para lo mismo. Una tabla de hash es muy buena para buscar por llave, pero no entrega orden. Un heap permite extraer mínimo o máximo, pero no buscar cualquier llave de forma eficiente. Union-Find responde conectividad entre componentes, pero no reconstruye caminos.

| Necesidad operacional | Buena elección | Mala elección típica |
|---|---|---|
| Buscar `rut -> datos` | Hash | Heap, si no se busca por prioridad. |
| Atender el pedido más antiguo de cada tipo | Hash + cola | Solo hash, porque se pierde orden de llegada dentro de cada tipo. |
| Extraer repetidamente el menor costo | Heap | Hash, porque no mantiene mínimo global. |
| Saber si agregar una arista crea ciclo en Kruskal | Union-Find | DFS repetido, si se requiere eficiencia. |
| Procesar dependencias sin ciclos | Grafo dirigido + TopSort | Hash ordenado, porque el orden depende de aristas. |
| Ruta más corta sin pesos | BFS | Kruskal, porque MST no da caminos mínimos. |
| Conectar todos los nodos al menor costo | Kruskal / Prim | Dijkstra, porque Dijkstra resuelve caminos desde un origen. |

### 2. Greedy, DP y backtracking se parecen, pero responden distinto

Estas tres técnicas pueden aparecer en problemas de optimización o búsqueda, pero su lógica es distinta.

| Técnica | Qué hace | Cuándo usar | Riesgo típico |
|---|---|---|---|
| Backtracking | Explora decisiones, retrocede si falla. | Variables, dominios y restricciones claras. | No deshacer cambios o detenerse antes de tiempo. |
| Greedy | Toma una decisión local definitiva. | Existe una elección segura justificable. | Usarlo sin demostrar optimalidad. |
| Programación dinámica | Resuelve subproblemas y guarda resultados. | Hay recurrencia y subproblemas solapados. | Definir mal el estado o el orden de llenado. |

Una señal importante: si el problema pide encontrar **todas** las soluciones o probar combinaciones bajo restricciones, backtracking suele ser natural. Si pide optimizar y las mismas decisiones parciales se repiten muchas veces, puede convenir DP. Si basta tomar una decisión local que siempre puede formar parte de una solución óptima, puede ser greedy.

### 3. Grafos no son una técnica única

Modelar como grafo solo es el primer paso. Después hay que elegir qué hacer con el grafo.

| Señal | Algoritmo o técnica |
|---|---|
| “¿Existe un camino?” | DFS o BFS. |
| “Camino con menor número de aristas” | BFS. |
| “Detectar ciclo dirigido” | DFS con colores. |
| “Ordenar tareas con dependencias” | Orden topológico. |
| “Componentes fuertemente conectadas” | Kosaraju u otro algoritmo de CFC. |
| “Aristas cuya eliminación desconecta” | Puentes con DFS. |
| “Conectar todo con costo mínimo” | Kruskal o Prim. |
| “Camino más corto ponderado no negativo” | Dijkstra. |

### 4. Complejidad se justifica por fases

En preguntas de desarrollo, conviene separar el algoritmo en partes:

| Fase | Ejemplo de costo |
|---|---|
| Construir estructura | `O(n)`, `O(|V| + |E|)`. |
| Ordenar | `O(n log n)` o `O(|E| log |E|)`. |
| Recorrer grafo | `O(|V| + |E|)`. |
| Llenar tabla DP | número de estados × costo por transición. |
| Explorar backtracking | tamaño del árbol de búsqueda, usualmente exponencial. |
| Operaciones de heap | `O(log n)` por inserción/extracción. |
| Operaciones de hash | `O(1)` esperado, no garantizado si hay muchas colisiones. |
| Union-Find optimizado | casi constante amortizado por operación. |

## Pseudocódigos de apoyo

### Diagnóstico de técnica

**Input:** enunciado de un problema.  
**Output:** técnica candidata y razón principal.

```text
DiagnosticarTecnica(enunciado):
    if aparecen llaves y consultas por identificador:
        return "Hash", "acceso por clave"

    if aparece minimo o maximo repetido:
        return "Heap", "acceso por prioridad"

    if aparecen variables, dominios y restricciones:
        if se piden todas las soluciones o factibilidad general:
            return "Backtracking", "exploracion de asignaciones"
        if hay subproblemas repetidos y objetivo optimo:
            return "Programacion dinamica", "recurrencia con memoria"

    if aparecen decisiones locales definitivas:
        if se puede justificar eleccion segura:
            return "Greedy", "decision local optima"
        else:
            return "Contraejemplo o DP/Backtracking", "greedy no justificado"

    if aparecen relaciones entre objetos:
        construir grafo
        if se piden dependencias:
            return "TopSort", "DAG"
        if se piden ciclos o componentes:
            return "DFS", "estructura del recorrido"
        if se pide distancia no ponderada:
            return "BFS", "recorrido por capas"
        if se pide conectar todo con costo minimo:
            return "Kruskal/Prim", "MST"

    return "Analizar operaciones dominantes", "falta senal clara"
```

### Plantilla para responder una pregunta de técnica

**Input:** problema de desarrollo.  
**Output:** respuesta estructurada para evaluación.

```text
ResponderProblema(enunciado):
    identificar objetivo del problema
    identificar operaciones dominantes
    proponer estructura o tecnica
    explicar que guarda cada estructura
    escribir algoritmo o pseudocodigo
    justificar correctitud a nivel conceptual
    calcular complejidad por fases
    mencionar errores o alternativas que no aplican
```

### Comparar greedy, DP y backtracking

**Input:** problema de optimización o búsqueda.  
**Output:** técnica más razonable.

```text
ElegirEntreBusquedaOptimizacion(problema):
    if se deben probar asignaciones bajo restricciones:
        if se piden todas las configuraciones:
            return Backtracking
        if solo se pide existencia:
            return Backtracking con podas

    if existe una decision local que siempre es segura:
        return Greedy

    if la solucion optima depende de subproblemas repetidos:
        definir estado
        definir recurrencia
        return ProgramacionDinamica

    return FuerzaBrutaMejorada o modelamiento adicional
```

## Complejidad: tabla de referencia rápida

| Técnica o estructura | Complejidad típica | Comentario |
|---|---:|---|
| Hash búsqueda/inserción | `O(1)` esperada | Depende de colisiones y factor de carga. |
| Heap insertar/extraer | `O(log n)` | Bueno para prioridad. |
| BFS / DFS | `O(|V| + |E|)` | Con listas de adyacencia. |
| Orden topológico | `O(|V| + |E|)` | Solo tiene sentido en DAG. |
| Kosaraju | `O(|V| + |E|)` | Requiere DFS en `G` y `G^T`. |
| Kruskal | `O(|E| log |E|)` | Domina la ordenación de aristas. |
| Union-Find optimizado | `O(α(n))` amortizado | Casi constante. |
| Backtracking | `O(K^n)` típico | `n` variables, dominio tamaño `K`. |
| DP con tabla 2D | `O(nS)` o similar | Depende de cantidad de estados. |
| Greedy | Depende de ordenar/seleccionar | Debe justificarse optimalidad. |

## Ejemplo guiado: leer señales del enunciado

### Enunciado A

“Se reciben miles de solicitudes. Cada solicitud tiene un tipo y un nombre. Cuando un tipo está listo, se debe atender a la persona que pidió ese tipo hace más tiempo.”

**Lectura:**

- “tipo” funciona como llave.
- “hace más tiempo” exige orden FIFO dentro de cada tipo.
- Se necesita acceso rápido por tipo.

**Elección:** hash + cola.

**Razón:** el hash permite ir al tipo en `O(1)` esperado; la cola permite sacar al más antiguo en `O(1)`.

### Enunciado B

“Hay que ubicar estaciones en una grilla. Algunas celdas están prohibidas. Se debe verificar si existe una forma de cubrir todas las zonas prioritarias.”

**Lectura:**

- Variables: estaciones.
- Dominios: celdas disponibles.
- Restricciones: no usar celdas prohibidas y cubrir zonas.

**Elección:** backtracking / CSP.

**Razón:** se asignan valores bajo restricciones y se puede retroceder si una asignación no sirve.

### Enunciado C

“Dado un conjunto de aristas con costos, conectar todas las sedes con costo total mínimo.”

**Lectura:**

- Objetos: sedes como vértices.
- Conexiones: aristas con costo.
- Objetivo: conectar todo, no encontrar camino desde una sede.

**Elección:** Kruskal o Prim.

**Razón:** es MST. Si se tienen aristas como lista, Kruskal es directo.

### Enunciado D

“Dado un monto `S` y elementos que pueden usarse o no, decidir si existe un subconjunto que sume exactamente `S`. Además, se repiten muchos subproblemas `p(k, S)`.”

**Lectura:**

- Hay decisión incluir/no incluir.
- La recursión repite estados por índice y suma restante.
- Se puede guardar una tabla.

**Elección:** programación dinámica.

**Razón:** hay subproblemas solapados y una recurrencia clara.

## Errores típicos

| Error | Por qué está mal | Corrección |
|---|---|---|
| Ordenar una tabla de hash | Hash no mantiene orden útil. | Usar hash solo para acceso por llave; agregar heap/lista si se requiere orden. |
| Usar heap para buscar cualquier elemento | Heap solo garantiza acceso rápido al mínimo/máximo. | Usar hash si se busca por identificador. |
| Usar greedy sin prueba | Una decisión local puede bloquear el óptimo. | Dar demostración por intercambio/contradicción o contraejemplo. |
| Usar backtracking cuando hay DP evidente | Se recalculan subproblemas repetidos. | Definir estado y memoizar. |
| Usar DP sin recurrencia clara | La tabla no tiene significado. | Definir subproblema, caso base y transición. |
| Aplicar TopSort con ciclos | El orden topológico solo existe en DAG. | Primero detectar ciclos. |
| Usar Kruskal para caminos mínimos | MST minimiza costo total de conexión, no rutas desde origen. | Usar BFS o Dijkstra según pesos. |
| Usar Union-Find para reconstruir caminos | Union-Find solo responde componentes. | Usar BFS/DFS con padres si se necesita camino. |
| Confundir restricción con objetivo | Una restricción define factibilidad; el objetivo define qué solución es mejor. | Separar `Feasible` de función objetivo. |

## Problema representativo

### Enunciado

Para cada caso, indica una técnica o estructura adecuada, justifica por qué y menciona una alternativa incorrecta común.

1. Se debe registrar si un estudiante tiene autorización para entrar a un laboratorio usando su identificador único.
2. Se deben asignar `k` estaciones en una grilla con celdas prohibidas para cubrir zonas prioritarias.
3. Se deben conectar varias sedes con cables de costo conocido minimizando el costo total.
4. Se debe encontrar el menor número de aristas entre un nodo inicial y todos los demás en un grafo no ponderado.
5. Se quiere decidir si un arreglo puede dividirse en dos subconjuntos de igual suma, y aparecen llamados repetidos del tipo `p(i, S)`.
6. Se debe escoger una secuencia de actividades sin traslape maximizando cierta ganancia.

### Solución esperada

| Caso | Técnica adecuada | Justificación | Alternativa incorrecta común |
|---|---|---|---|
| 1 | Hash | Consulta por llave/identificador. | Heap, porque no se consulta por prioridad. |
| 2 | Backtracking / CSP | Hay variables, dominios y restricciones. | Greedy sin prueba de optimalidad. |
| 3 | Kruskal o Prim | Es MST: conectar todos con costo mínimo. | Dijkstra, porque no pide caminos desde origen. |
| 4 | BFS | Grafo no ponderado y distancia por número de aristas. | DFS, porque no garantiza menor distancia. |
| 5 | Programación dinámica | Estado repetido `p(i, S)` y recurrencia incluir/no incluir. | Backtracking puro, porque recalcula estados. |
| 6 | DP o greedy según condición | Si hay ganancias arbitrarias, suele requerir DP; si no hay ganancias y solo se maximiza cantidad, puede ser greedy por término más temprano. | Greedy por mayor ganancia inmediata. |

## Preguntas de repaso

1. ¿Qué diferencia hay entre buscar por llave y buscar por prioridad?
2. ¿Por qué una tabla de hash no debe tratarse como una estructura ordenada?
3. ¿Qué señales indican que un problema debe modelarse como CSP?
4. ¿Qué debe demostrarse para que una estrategia greedy sea aceptable?
5. ¿Qué elementos mínimos necesita una solución de programación dinámica?
6. ¿Por qué BFS encuentra caminos mínimos en grafos no ponderados?
7. ¿Por qué Kruskal no resuelve caminos mínimos?
8. ¿Qué responde Union-Find y qué no responde?
9. ¿Cuándo un grafo admite orden topológico?
10. ¿Cómo se justifica una complejidad por fases?

## Ejercicios breves

1. Da un ejemplo donde hash + cola sea mejor que solo hash.
2. Da un contraejemplo para una estrategia greedy incorrecta.
3. Convierte un problema de asignación de horarios en variables, dominios y restricciones.
4. Distingue si un problema de subconjuntos conviene resolverlo con backtracking o DP.
5. Explica por qué un MST puede no contener el camino más corto entre dos nodos.
6. Propón qué estructura usarías para mantener los tres eventos con mayor prioridad mientras llegan datos.
7. Dado un grafo con dependencias entre cursos, explica cuándo existe un orden válido.
8. Dado un grafo no ponderado, escribe qué usarías para distancia mínima desde un origen.

## Checklist de estudio

Antes de cerrar esta página, deberías poder:

- distinguir hash, heap, cola, grafo y Union-Find por operación dominante;
- explicar por qué una tabla de hash no tiene orden útil;
- modelar un problema de backtracking con variables, dominios y restricciones;
- decidir cuándo un problema greedy necesita demostración o contraejemplo;
- definir subproblema, recurrencia, caso base y orden de llenado en DP;
- elegir BFS o DFS según el tipo de pregunta en grafos;
- distinguir MST de caminos mínimos;
- explicar por qué Kruskal usa Union-Find;
- justificar complejidad separando fases;
- responder una pregunta mixta sin mezclar roles de estructuras.

# Algoritmos codiciosos (Greedy)

## Objetivo de aprendizaje

Comprender cuándo una estrategia codiciosa puede usarse para resolver un problema de optimización, cómo identificar sus partes principales y cómo justificar o refutar su correctitud. La meta de estudio no es memorizar una plantilla, sino reconocer cuándo una decisión local puede llevar a una solución global óptima y cuándo puede fallar.

## Vocabulario mínimo

| Concepto | Definición práctica |
|---|---|
| Algoritmo codicioso | Técnica que construye una solución tomando decisiones locales definitivas. |
| Decisión local | Elección que parece la mejor en el estado actual del problema. |
| Solución parcial | Conjunto de decisiones tomadas hasta cierto momento. |
| Solución factible | Solución que cumple todas las restricciones del problema. |
| Solución óptima | Solución factible que maximiza o minimiza la función objetivo. |
| Función objetivo | Cantidad que se quiere optimizar, por ejemplo ganancia, costo, cantidad de tareas o distancia cubierta. |
| Select | Regla que decide qué candidato se considera ahora. |
| Feasible | Prueba que indica si agregar el candidato mantiene la solución factible. |
| Union | Operación que incorpora el candidato a la solución parcial. |
| Elección segura | Decisión local que puede pertenecer a alguna solución óptima. |
| Contraejemplo | Instancia pequeña que muestra que una estrategia codiciosa no siempre produce el óptimo. |

## Conceptos centrales

Un algoritmo codicioso parte con una solución vacía y va incorporando elementos uno a uno. En cada paso elige el candidato que parece mejor según algún criterio local. La decisión es definitiva: no se deshace después, a diferencia de backtracking.

La forma conceptual es:

1. Hay un conjunto de candidatos.
2. Hay restricciones de factibilidad.
3. Hay una función objetivo que se quiere maximizar o minimizar.
4. Se define una regla de selección local.
5. Se agrega un candidato si no rompe las restricciones.

La advertencia central es que una estrategia codiciosa plausible no necesariamente es correcta. Para usarla en una evaluación hay que justificar por qué la elección local es segura o mostrar un contraejemplo si la estrategia falla.

## Cuándo usar greedy

| Señal en el enunciado | Posible lectura |
|---|---|
| “Elegir repetidamente el mejor candidato disponible” | Puede ser greedy, pero hay que justificar. |
| “Minimizar cantidad de intervalos/salas/costos” | Puede admitir una elección local segura. |
| “Conectar todo con costo mínimo” | Puede ser MST; Kruskal y Prim son codiciosos. |
| “Tomar decisiones sin volver atrás” | Greedy, no backtracking. |
| “Cada decisión afecta subproblemas futuros repetidos” | Podría ser programación dinámica, no greedy. |
| “Hay ganancias/pesos y objetos fraccionables” | Mochila fraccionable: ordenar por ganancia/peso. |
| “Hay ganancias/pesos y cada objeto se toma o no se toma” | Mochila 0/1: normalmente DP, no greedy simple. |

## Diferencia con otras técnicas

| Técnica | Qué hace | Diferencia clave |
|---|---|---|
| Backtracking | Explora alternativas y retrocede si una rama falla. | Greedy no retrocede. |
| Programación dinámica | Resuelve subproblemas solapados y guarda resultados. | Greedy no llena una tabla de recurrencia. |
| Divide y vencerás | Divide en subproblemas independientes del mismo tipo. | Greedy selecciona candidatos, no necesariamente divide el problema. |
| Kruskal | Greedy específico para MST. | Usa Union-Find para mantener factibilidad sin ciclos. |

## Estructura conceptual de una solución greedy

Para escribir una respuesta de interrogación, conviene declarar explícitamente:

| Parte | Pregunta guía | Ejemplo |
|---|---|---|
| Candidatos | ¿Qué elementos puedo elegir? | Charlas, aristas, objetos, intervalos. |
| Select | ¿Cuál elijo ahora? | El que termina antes, menor costo, mayor razón valor/peso. |
| Feasible | ¿Qué condición no debe romperse? | No traslapar charlas, no formar ciclo, no exceder capacidad. |
| Union | ¿Cómo actualizo la solución? | Agrego charla, arista u objeto/fracción. |
| Objetivo | ¿Qué estoy optimizando? | Cantidad, ganancia, costo total o cobertura. |
| Justificación | ¿Por qué la decisión local es segura? | Argumento de intercambio, contradicción o contraejemplo si falla. |

## Pseudocódigo base

**Input:** conjunto o arreglo de candidatos `A`.  
**Output:** solución `S` construida codiciosamente.

```text
Greedy(A):
    S <- empty

    while quedan candidatos en A:
        x <- Select(A)

        if Feasible(S, x):
            S <- Union(S, x)

        quitar x de A

    return S
```

Este pseudocódigo es abstracto. En un problema real, `Select`, `Feasible` y `Union` deben definirse concretamente.

## Pseudocódigo: selección de charlas sin ganancia

Problema: escoger la mayor cantidad de charlas compatibles. Cada charla tiene inicio `s` y término `f`. Dos charlas son compatibles si no se traslapan.

Estrategia correcta: ordenar por hora de término creciente y elegir cada charla compatible con la última elegida.

**Input:** arreglo de charlas `A`, donde cada charla tiene `inicio` y `fin`.  
**Output:** subconjunto máximo de charlas mutuamente compatibles.

```text
SeleccionCharlas(A):
    ordenar A por fin creciente
    S <- empty
    ultimo_fin <- -infinito

    for charla in A:
        if charla.inicio >= ultimo_fin:
            agregar charla a S
            ultimo_fin <- charla.fin

    return S
```

Complejidad: ordenar toma `O(n log n)` y el recorrido toma `O(n)`, por lo tanto la complejidad total es `O(n log n)`.

## Pseudocódigo: cubrimiento de un segmento con intervalos

Problema: cubrir el segmento `[L, R]` con la menor cantidad de intervalos disponibles.

Estrategia: desde el punto actualmente cubierto, elegir entre todos los intervalos que empiezan antes o en ese punto el que llega más lejos.

**Input:** intervalo objetivo `[L, R]` y arreglo de intervalos `I`.  
**Output:** subconjunto de intervalos que cubre `[L, R]`, o `FAIL` si no se puede cubrir.

```text
CubrirSegmento(I, L, R):
    ordenar I por inicio creciente
    S <- empty
    actual <- L
    i <- 0

    while actual < R:
        mejor_fin <- actual
        mejor_intervalo <- NIL

        while i < largo(I) and I[i].inicio <= actual:
            if I[i].fin > mejor_fin:
                mejor_fin <- I[i].fin
                mejor_intervalo <- I[i]
            i <- i + 1

        if mejor_intervalo = NIL:
            return FAIL

        agregar mejor_intervalo a S
        actual <- mejor_fin

    return S
```

Complejidad: `O(n log n)` por el ordenamiento y `O(n)` por el barrido.

## Pseudocódigo: mochila fraccionable

Problema: hay objetos con peso `w[i]` y ganancia `p[i]`. Se puede tomar una fracción de cada objeto. La mochila tiene capacidad `M`.

Estrategia correcta para la versión fraccionable: ordenar por razón ganancia/peso.

$$
\text{razon}(i) = \frac{p_i}{w_i}
$$

**Input:** objetos con pesos `w`, ganancias `p` y capacidad `M`.  
**Output:** fracciones elegidas `x[i]` y ganancia total.

```text
MochilaFraccionable(objetos, M):
    ordenar objetos por p[i] / w[i] de mayor a menor
    ganancia <- 0
    capacidad <- M

    for objeto in objetos:
        if capacidad = 0:
            break

        if objeto.peso <= capacidad:
            tomar fraccion 1 del objeto
            capacidad <- capacidad - objeto.peso
            ganancia <- ganancia + objeto.ganancia
        else:
            fraccion <- capacidad / objeto.peso
            tomar fraccion del objeto
            ganancia <- ganancia + fraccion * objeto.ganancia
            capacidad <- 0

    return ganancia
```

Complejidad: `O(n log n)` por ordenar los objetos.

## Caso relacionado: Kruskal como algoritmo codicioso

Kruskal es greedy porque considera las aristas por costo creciente y agrega una arista si no forma ciclo. La decisión local es “tomar la arista más barata disponible que mantenga el bosque acíclico”.

**Input:** grafo no dirigido ponderado `G = (V, E)`.  
**Output:** árbol de cobertura mínimo `T`.

```text
Kruskal(G):
    T <- empty
    ordenar E por peso creciente

    for v in V:
        MakeSet(v)

    for (u, v, peso) in E:
        if Find(u) != Find(v):
            agregar (u, v) a T
            Union(u, v)

    return T
```

Complejidad: usualmente `O(E log E)` por ordenar las aristas, más el costo casi constante amortizado de Union-Find. Esta página debe mencionarlo solo como conexión; la explicación completa quedará para `kruskal-union-find.qmd`.

## Fórmulas y criterios mínimos

En problemas de optimización se suele tener una función objetivo:

$$
\max \sum_{i \in S} p_i
$$

o bien:

$$
\min \sum_{i \in S} c_i
$$

Una solución greedy intenta construir `S` con decisiones locales. Para justificarla, no basta decir “parece mejor”. Hay que explicar por qué elegir ese candidato no impide alcanzar una solución óptima.

## Complejidad

La complejidad depende de cómo se implemente `Select`.

| Implementación de Select | Complejidad típica | Comentario |
|---|---|---|
| Buscar el mejor candidato recorriendo todo en cada paso | `O(n^2)` | Simple, pero puede ser lento. |
| Ordenar una vez y recorrer | `O(n log n)` | Muy común en greedy con intervalos u objetos. |
| Usar heap | `O(n log n)` | Útil si se extrae repetidamente el mínimo o máximo. |
| Usar Union-Find después de ordenar | `O(E log E)` | Patrón de Kruskal. |

## Ejemplo guiado 1: charlas compatibles

Charlas:

| Charla | Inicio | Fin |
|---|---:|---:|
| A | 1 | 4 |
| B | 3 | 5 |
| C | 0 | 6 |
| D | 5 | 7 |
| E | 8 | 9 |
| F | 5 | 9 |

Paso 1: ordenar por término.

| Orden | Charla | Inicio | Fin |
|---:|---|---:|---:|
| 1 | A | 1 | 4 |
| 2 | B | 3 | 5 |
| 3 | C | 0 | 6 |
| 4 | D | 5 | 7 |
| 5 | E | 8 | 9 |
| 6 | F | 5 | 9 |

Paso 2: recorrer.

| Charla | ¿Compatible? | Acción | Último fin |
|---|---|---|---:|
| A | Sí | Se agrega | 4 |
| B | No, empieza 3 < 4 | Se descarta | 4 |
| C | No, empieza 0 < 4 | Se descarta | 4 |
| D | Sí, empieza 5 >= 4 | Se agrega | 7 |
| E | Sí, empieza 8 >= 7 | Se agrega | 9 |
| F | No, empieza 5 < 9 | Se descarta | 9 |

Resultado: `{A, D, E}`.

La intuición es que elegir la charla que termina antes deja el mayor espacio posible para las charlas futuras.

## Ejemplo guiado 2: una estrategia greedy falsa para LIS

Problema: encontrar la subsecuencia creciente más larga de una secuencia.

Estrategia propuesta:

> Revisar de izquierda a derecha. Si el elemento actual es mayor que el último incluido, incluirlo; si no, descartarlo.

Contraejemplo:

$$
A = \langle 10, 1, 2, 3, 4 \rangle
$$

La estrategia toma `10` primero. Luego descarta `1`, `2`, `3` y `4`, porque todos son menores que `10`. Obtiene una subsecuencia de largo 1: `<10>`.

Pero la solución óptima es:

$$
\langle 1, 2, 3, 4 \rangle
$$

que tiene largo 4. Por lo tanto, esa estrategia codiciosa no es correcta.

## Cómo justificar que un greedy funciona

Hay tres estilos frecuentes:

| Método | Idea |
|---|---|
| Intercambio | Tomar una solución óptima cualquiera y modificarla para que incluya la elección greedy sin empeorarla. |
| Contradicción | Suponer que la elección greedy no puede estar en un óptimo y llegar a una contradicción. |
| Elección segura | Mostrar que existe al menos una solución óptima que contiene la decisión local tomada. |

En una evaluación, si no se puede demostrar formalmente, al menos se debe escribir una justificación clara de por qué la decisión local no bloquea una solución óptima. Si la estrategia falla, hay que dar un contraejemplo pequeño y explicar el error.

## Errores típicos

| Error | Corrección |
|---|---|
| Decir “es greedy porque ordena”. | Ordenar no basta. Hay que indicar decisión local, factibilidad y objetivo. |
| Usar greedy sin justificar optimalidad. | Agregar argumento de intercambio, contradicción o elección segura. |
| Confundir mochila fraccionable con mochila 0/1. | La razón ganancia/peso funciona para fraccionable, no necesariamente para 0/1. |
| Usar Kruskal para caminos más cortos. | Kruskal resuelve MST; no resuelve rutas más cortas. |
| Usar Dijkstra para MST. | Dijkstra resuelve caminos mínimos desde un origen. |
| Confundir heurística con garantía. | Una heurística puede ser buena en la práctica, pero necesita prueba para asegurar óptimo. |
| No definir Feasible. | Siempre explicar qué condición mantiene válida la solución parcial. |

## Problema representativo de interrogación

<details>
<summary>Ver enunciado</summary>

Se tienen `n` charlas, cada una con hora de inicio `s_i` y hora de término `f_i`. Se quiere seleccionar la mayor cantidad posible de charlas sin traslapes.

1. Propón un algoritmo codicioso.
2. Identifica `Select`, `Feasible` y `Union`.
3. Explica por qué la estrategia tiene sentido.
4. Calcula la complejidad.
5. Indica qué cambiaría si cada charla tuviera una ganancia distinta y se quisiera maximizar la ganancia total.

</details>

<details>
<summary>Ver solución</summary>

La estrategia es ordenar las charlas por hora de término creciente y elegir una charla si comienza después o justo cuando termina la última charla seleccionada.

`Select`: tomar la charla disponible que termina antes.

`Feasible`: verificar que `s_i >= ultimo_fin`.

`Union`: agregar la charla al conjunto solución y actualizar `ultimo_fin = f_i`.

**Input:** arreglo de charlas `A`, cada una con inicio y fin.  
**Output:** subconjunto máximo de charlas compatibles.

```text
SeleccionCharlas(A):
    ordenar A por fin creciente
    S <- empty
    ultimo_fin <- -infinito

    for charla in A:
        if charla.inicio >= ultimo_fin:
            agregar charla a S
            ultimo_fin <- charla.fin

    return S
```

La idea es que terminar lo antes posible deja más espacio disponible para las charlas futuras. Una justificación por intercambio dice que, si una solución óptima eligiera primero una charla que termina más tarde, se puede reemplazar por la charla compatible que termina antes sin reducir la cantidad total de charlas posibles.

La complejidad es:

$$
O(n \log n) + O(n) = O(n \log n)
$$

Si cada charla tiene una ganancia distinta, elegir la que termina antes ya no necesariamente maximiza la ganancia total. Esa variante suele resolverse con programación dinámica: para cada charla se compara tomarla más la mejor solución compatible anterior contra no tomarla.

</details>

## Preguntas de repaso

1. ¿Qué significa que una decisión greedy sea definitiva?
2. ¿Por qué un algoritmo greedy no es lo mismo que backtracking?
3. ¿Cuáles son las tres funciones clave del pseudocódigo greedy abstracto?
4. ¿Por qué ordenar por ganancia/peso funciona en mochila fraccionable, pero no necesariamente en mochila 0/1?
5. ¿Qué es un contraejemplo y por qué es suficiente para refutar una estrategia codiciosa?
6. ¿Por qué Kruskal es un algoritmo codicioso?
7. ¿Qué diferencia hay entre MST y caminos mínimos?
8. ¿Qué debe decir una respuesta cuando propone una estrategia greedy?

## Ejercicios breves

1. Dada la secuencia `A = <8, 1, 2, 9, 3, 4>`, aplica la estrategia codiciosa falsa para LIS que toma el primer elemento posible. Luego compara con una mejor subsecuencia creciente.
2. Da un contraejemplo donde elegir siempre la charla más corta no maximice la cantidad de charlas compatibles.
3. Resuelve una instancia de mochila fraccionable con tres objetos y capacidad `M = 20` usando razón ganancia/peso.
4. Para un conjunto de intervalos que cubren `[0, 10]`, aplica la estrategia de elegir el intervalo que llega más lejos desde el punto actual.
5. Explica por qué una estrategia que “parece razonable” no basta como prueba de correctitud.
6. Indica si cada frase sugiere greedy, DP o backtracking: “todas las configuraciones”, “subproblemas repetidos”, “decisiones locales definitivas”.

## Checklist de estudio

- [ ] Puedo definir qué es un algoritmo codicioso.
- [ ] Puedo identificar candidatos, `Select`, `Feasible`, `Union` y función objetivo.
- [ ] Sé que una decisión greedy no se deshace.
- [ ] Sé dar un contraejemplo para una estrategia codiciosa falsa.
- [ ] Sé que mochila fraccionable y mochila 0/1 no son lo mismo.
- [ ] Sé que charlas sin ganancia admiten selección por término temprano.
- [ ] Sé que charlas con ganancia suelen requerir programación dinámica.
- [ ] Sé que Kruskal es greedy y usa Union-Find para evitar ciclos.
- [ ] No confundo MST con caminos mínimos.
- [ ] Puedo explicar la complejidad según ordenamiento, heap o búsqueda repetida.


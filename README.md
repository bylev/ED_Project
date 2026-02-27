# ED_Proyecto: BFS usando una cola propia
## Equipo: Michelle Cámara, Carlos Llanes, Marcelo Medina. 
## Docente: Ing. Didier Gamboa Angulo. 
## 2°C

---

# Índice

---

# Descripción del proyecto

En este proyecto se implementó el algoritmo BFS utilizando una estructura de datos Queue propia. 

El objetivo fue recorrer un grafo utilizando el algoritmo BFS, que es un algoritmo de búsqueda en anchura. Este algoritmo se utiliza para explorar todos los nodos de un grafo de manera sistemática, visitando primero los nodos más cercanos al nodo inicial antes de avanzar a los nodos más lejanos.

El proyecto se desarrolló en el lenguaje de programación Python, y se implementó una clase `Queue` para manejar la estructura de datos necesaria para el algoritmo BFS. La clase `Queue` incluye métodos para agregar elementos, eliminar elementos y verificar si la cola está vacía.

--- 

## Estructuras implementadas

### Clase Queue

```python
class Queue:
  def __init__(self):
    self.data = []
    self.front = 0

  def enqueue(self, item):
    self.data.append(item)

  def dequeue(self):
    if self.is_empty():
      return None
    item = self.data[self.front]
    self.front +=1
    return item

  def first(self):
    if self.is_empty():
      return None
    return self.data[self.front]

  def is_empty(self):
    return self.front == len(self.data)

  def size(self):
    return len(self.data) - self.front
```

Se implementó una cola tipo FIFO utilizando una lista para almacenar los elementos y un índice front para indicar el primer elemento en cola. Los métodos `enqueue` y `dequeue` permiten agregar y eliminar elementos de la cola respectivamente, mientras que el método `is_empty` verifica si la cola está vacía. El método `size` devuelve el número de elementos en la cola.

La cola se utiliza dentro del algoritmo BFS para procesar los nodos en orden.

---

### Clase Graph

```python
class Graph:
    def __init__(self, directed: bool = False):
        self.directed = directed
        self.adj = {}  # dict: vertex -> set(neighbors)

    def add_vertex(self, v):
        if v not in self.adj:
            self.adj[v] = set()

    def add_edge(self, u, v):
        self.add_vertex(u)
        self.add_vertex(v)

        self.adj[u].add(v)
        if not self.directed:
            self.adj[v].add(u)

    def neighbors(self, v):
        return self.adj.get(v, set())

    def __str__(self):
        lines = []
        for v in self.adj:
            nbrs = ", ".join(map(str, sorted(self.adj[v])))
            lines.append(f"{v}: [{nbrs}]")
        return "\n".join(lines)

    def has_edge(self, u, v):
      if u not in self.adj or v not in self.adj:
        raise ValueError("No existe uno o los dos vértices.") # Si mi vértice no existe, lanzo un error.
      return v in self.adj[u]

    def remove_edge(self,u,v):
      if not self.has_edge(u,v):
        raise ValueError("No existe la arista.") # Si la arista no existe, lanza un err
      self.adj[u].discard(v)
      if not self.directed:
        self.adj[v].discard(u) #Se remueve la arista inversa.

    def remove_vertex(self, v):
      if v not in self.adj:
        raise ValueError("No existe el vértice.")

      del self.adj[v] #Elimina el vértice.
      for u in self.adj: #Elimina referencias al vértice desde los demás.
        self.adj[u].discard(v)

    def degree(self, v):
      if v not in self.adj:
        raise ValueError("No existe el vértice.")

      if  self.directed:
        raise ValueError("No se puede calcular el grado de un vértice en un grafo dirigido.")

      return len(self.adj[v])


    def out_degree(self, v):
      if v not in self.adj:
        raise ValueError("No existe el vértice.")
      return len(self.adj[v])

    def in_degree(self, v):
      if v not in self.adj:
        raise ValueError("No existe el vértice.")

      in_degree = 0
      for u in self.adj:
        if v in self.adj[u]:
          in_degree += 1

      return in_degree


    def bfs(self, start):
      if start not in self.adj:
        raise ValueError("No existe el vértice.")

      q = Queue()
      visited = set()
      order = []

      visited.add(start)
      q.enqueue(start)


      while not q.is_empty():
        v = q.dequeue()
        order.append(v)

        for u in sorted(self.adj[v]):
          if u not in visited:
            visited.add(u)
            q.enqueue(u)

      return order

    def bfs_parents(self, start):
      if start not in self.adj:
        return{}

      q = Queue()
      visited  = set()
      parents = {start: None}

      visited.add(start)
      q.enqueue(start)

      while not q.is_empty():
        v = q.dequeue()

        for u in sorted(self.adj[v]):
          if u not in visited:
            visited.add(u)
            parents[u] = v
            q.enqueue(u)

      return parents

    def path_unweighted(self, start, end):
        parents = self.bfs_parents(start)

        if end not in parents:
            return None

        path = []
        v = end
        while v is not None:
            path.append(v)
            v = parents[v]

        path.reverse()
        return path
```

La clase `Graph` representa un grafo utilizando un diccionario de adyacencia. Permite agregar vértices y aristas, obtener los vecinos de un vértice, verificar la existencia de una arista, eliminar aristas y vértices, calcular el grado de un vértice, y realizar el recorrido BFS. 

El método `bfs` devuelve el orden de los vértices visitados durante el recorrido BFS, mientras que el método `bfs_parents` devuelve un diccionario que mapea cada vértice a su padre en el recorrido BFS. El método `path_unweighted` utiliza el resultado de `bfs_parents` para reconstruir el camino más corto entre dos vértices en un grafo no ponderado.

---

## Pruebas realizadas

Se realizaron pruebas para verificar el funcionamiento correcto de la clase `Queue`. Además se realizaron las siguientes pruebas para verificar el funcionamiento: 

1. BFS en un grafo conexo: Se creó un grafo conexo y se ejecutó el algoritmo BFS desde un vértice específico. Se verificó que el orden de los vértices visitados correspondiera al esperado.

2. BFS en un grafo con dos componentes: Se creó un grafo con dos componentes desconectados y se ejecutó el algoritmo BFS desde un vértice en uno de los componentes. Se verificó que solo se visitaran los vértices del componente correspondiente.

3. Búsqueda de camino existente: Se creó un grafo con un camino entre dos vértices y se utilizó el método `path_unweighted` para encontrar el camino. Se verificó que el camino devuelto fuera correcto.

4. Búsqueda de camino inexistente: Se creó un grafo sin un camino entre dos vértices y se utilizó el método `path_unweighted` para intentar encontrar el camino. Se verificó que el resultado fuera `None`, indicando que no existe un camino entre los vértices.

---
 ## Conclusión

En conclusión, se implementó con éxito el algoritmo BFS utilizando una estructura de datos Queue propia. La implementación de la clase `Graph` permitió manejar grafos de manera eficiente y realizar el recorrido BFS correctamente. Las pruebas realizadas confirmaron que el algoritmo funciona como se esperaba en diferentes escenarios, incluyendo grafos conexos y desconectados, así como la búsqueda de caminos entre vértices. Este proyecto proporcionó una comprensión sólida del algoritmo BFS y su aplicación en grafos.



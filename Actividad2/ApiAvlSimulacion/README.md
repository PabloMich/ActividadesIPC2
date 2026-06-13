# Parte 1: Investigación Teórica

## 1. El Problema de las Rotaciones Simples

### ¿Por qué fallan las rotaciones simples?

Las rotaciones simples funcionan cuando el árbol está desbalanceado en una sola dirección. Pero cuando insertas valores en "zig-zag" (cruzados), una rotación simple no es suficiente.

**Ejemplo:**

```
Insertas: 30 → 10 → 20

Resultado:
    30
   /
  10
    \
     20   ← Zig-Zag (problema cruzado)
```

Si usas una rotación simple en este caso, el árbol sigue mal balanceado.

### ¿Cuándo usar Rotación Doble Izquierda-Derecha (RID)?

La rotación doble se usa cuando:

- El nodo padre está desbalanceado a un lado
- Pero el hijo está inclinado al lado opuesto (zig-zag)

**Resultado correcto con RID:**

```
    20     ← Ahora 20 es la raíz
   /  \
  10   30  ← Perfecto balance
```

---

## 2. Principio DRY (No te Repitas)

### ¿Qué es DRY?

Es un principio de programación que dice: **no copies el mismo código en varios lugares**.

### ¿Por qué es importante en rotaciones?

En lugar de escribir código completamente nuevo para cada tipo de rotación, **reutilizas las rotaciones simples**.

**Sin DRY (malo):**

```csharp
// Rotación doble 1 - 50 líneas de código
public void RotacionDoble1() { ... }

// Rotación doble 2 - 50 líneas de código (casi igual)
public void RotacionDoble2() { ... }
```

**Con DRY (bueno):**

```csharp
// Rotación simple (reutilizable)
public void RotacionIzquierda(Nodo nodo) { ... }
public void RotacionDerecha(Nodo nodo) { ... }

// Rotación doble = combinar dos simples
public void RotacionDoble(Nodo nodo) {
    RotacionDerecha(nodo.Hijo);
    RotacionIzquierda(nodo);
}
```

**Ventaja:** Menos código, más fácil de arreglar si hay errores.

---

## 2. Fundamentos de Web y HTTP

### El Modelo Cliente-Servidor

**Cliente:** Tu navegador o aplicación que pide información
**Servidor:** La computadora que tiene los datos y responde

```
Cliente (tú)  →  pide datos  →  Servidor (API)
Cliente (tú)  ←  responde    ←  Servidor (API)
```

### ¿Cómo viajan los datos?

**REQUEST (petición del cliente):**

```
GET /api/arbol
Servidor: localhost:5000
```

"Servidor, quiero ver el árbol actual"

**RESPONSE (respuesta del servidor):**

```
HTTP 200 OK
[{"id": 30}, {"id": 10}]
```

"Aquí están los nodos del árbol"

---

### GET vs POST: ¿Cuándo usar cada uno?

**GET = LEER datos**

- Para obtener información
- No modifica nada
- Los datos no se envían en el cuerpo

```
GET /api/arbol
→ Me devuelve: los nodos actuales
```

**POST = CREAR o MODIFICAR datos**

- Para insertar nuevos datos
- Modifica el servidor
- Los datos van en el cuerpo

```
POST /api/arbol/insertar
Body: {"id": 20, "etiqueta": "Nuevo nodo"}
→ El servidor crea el nodo y devuelve: 201 Created
```

### Ejemplo en nuestra API:

```csharp
// GET: Solo lectura, devuelve los nodos
app.MapGet("/api/arbol", () => estadoArbol);

// POST: Inserta un nodo, modifica el servidor
app.MapPost("/api/arbol/insertar", (NodoAVL nodo) => {
    estadoArbol.Add(nodo);
    return "Nodo insertado";
});
```

---

## Resumen

**Rotación Doble** Se usa cuando el árbol está en zig-zag  
**DRY** Reutiliza código simple en lugar de repetir
**GET** Para leer datos (sin modificar)  
**POST** Para crear o modificar datos

# Parte 1: Investigación Teórica

## 1. Estructuras de Datos Eficientes

* **Árboles Binarios de Búsqueda (ABB):** Un ABB organiza sus datos siguiendo una regla de ordenamiento estricta: para cualquier nodo raíz, todos los valores ubicados en su subárbol izquierdo deben ser menores, y todos los valores en su subárbol derecho deben ser mayores. Su principal desventaja se manifiesta cuando los datos se insertan de forma secuencial (ya ordenados); al no reequilibrarse, el árbol crece hacia un solo lado y degenera en una lista vinculada lineal. Esto destruye la eficiencia de búsqueda, pasando de un tiempo logarítmico a un tiempo $O(n)$.

* **Árboles AVL:** Un árbol AVL es una estructura auto-balanceada que previene la degeneración ajustando su propia forma dinámicamente tras cada inserción o eliminación. Utiliza el cálculo de un factor de balanceo ($Factor = Altura_{Izquierda} - Altura_{Derecha}$) para medir la diferencia de niveles entre sus subárboles. Si el desequilibrio supera los límites permitidos, el árbol aplica rotaciones mecánicas para nivelarse. Al garantizar matemáticamente que las ramas se mantengan lo más cortas y simétricas posibles, asegura que las operaciones de búsqueda, inserción y eliminación mantengan siempre una complejidad óptima de $O(\log n)$.

## 2. Fundamentos de Web APIs

* **¿Qué es una API y cómo funciona el modelo Cliente-Servidor?**
  Una API (Interfaz de Programación de Aplicaciones) actúa como un puente estandarizado que permite que dos sistemas de software se comuniquen entre sí. En el modelo Cliente-Servidor, el ciclo de vida de la información ocurre mediante la red: el Cliente (como un navegador o Postman) ensambla y envía una petición (Request) al servidor a través del protocolo HTTP. El Servidor procesa esta solicitud, ejecuta la lógica pertinente en la memoria o base de datos, y emite una respuesta (Response) que viaja de vuelta al cliente, usualmente empaquetada en formato JSON y acompañada de un código de estado (como 200 OK o 404 Not Found).

* **Verbos HTTP e Idempotencia:**
  * **GET (Recuperación de recursos):** Es el verbo diseñado exclusivamente para solicitar y leer información del servidor sin alterarla. Cumple con la propiedad de idempotencia, lo que significa que ejecutar una petición GET una sola vez o repetirla mil veces seguidas producirá exactamente el mismo impacto en el estado del servidor (ninguno).
  * **POST (Creación de nuevos recursos):** Se utiliza para enviar un bloque de datos (payload) al servidor con la directiva de insertar o crear un nuevo registro en la colección. A diferencia de GET, POST no es idempotente: si envías la misma petición POST de creación múltiples veces de manera idéntica, el servidor creará múltiples registros duplicados en su sistema.

# Reporte de Laboratorio: Arquitectura Multi-Nivel y Patrón MVC

## Parte 1: Fundamentación Teórica y Análisis Crítico

### 1. El Tránsito hacia los Sistemas Distribuidos y Multi-Capa

**La Limitación del Monolito Local:**
Cuando la interfaz, la lógica de negocio y el almacenamiento residen de forma exclusiva en una máquina física aislada, el sistema sufre de un cuello de botella crítico. Si la demanda aumenta, la única forma de escalar es mejorando el hardware de esa máquina específica (escalabilidad vertical). Si se intenta replicar la máquina para balancear la carga (escalabilidad horizontal), los datos quedan aislados, provocando graves problemas de sincronización e inconsistencia de la información.

**Distinción Crítica (Layers vs. Tiers):**

- **Capas Lógicas (Layers):** Es una división conceptual del código dentro de un mismo proyecto (organización en carpetas o paquetes). Todo se ejecuta en el mismo espacio de memoria física.
- **Niveles Físicos (Tiers):** Es la separación física del sistema. Cada nivel representa una infraestructura distinta (servidores independientes) que se comunican a través de la red.

**Responsabilidades en la Arquitectura de 3 Niveles:**

- **Nivel 1: Capa de Presentación (Presentation Tier):** Su misión exclusiva es interactuar con el usuario y mostrar la información. Utiliza tecnologías como HTML, CSS, JavaScript o ReactJS.
- **Nivel 2: Capa de Aplicación o Negocio (Application Tier):** Es el cerebro del sistema. Procesa reglas matemáticas, valida datos y orquesta la comunicación entre la vista y los datos. Emplea lenguajes como C# o Java.
- **Nivel 3: Capa de Datos (Data Tier):** Su misión es el almacenamiento persistente y la garantía de la integridad de los datos, empleando motores como SQL Server o MongoDB.

**Seguridad Perimetral:**
Exponer públicamente el puerto de una base de datos a internet es un error crítico porque facilita ataques directos y explotación de vulnerabilidades. La buena práctica recomendada es aislar la base de datos en una red privada estricta, permitiendo que únicamente el nivel de aplicación tenga los permisos necesarios para consultarla.

### 2. Desacoplamiento Lógico con el Patrón MVC

**La Crisis del Código Espagueti:**
Mezclar sentencias SQL, lógica matemática y etiquetas visuales dentro de un mismo archivo físico arruina el mantenimiento, ya que un cambio en la interfaz puede corromper la lógica de datos. Además, imposibilita el diseño de pruebas unitarias aisladas, ya que probar una función matemática obligaría a renderizar elementos gráficos e inicializar conexiones a la base de datos.

**Separación de Preocupaciones (SoC):**

- **Modelo:** Representa los datos y reglas de negocio. No debe conocer cómo se muestran los datos para permitir su reutilización en distintas interfaces.
- **Vista:** Es una entidad pasiva e inteligente encargada exclusivamente de la presentación. Tiene estrictamente prohibido contener lógica de negocio o cálculos complejos.
- **Controlador:** Actúa como intermediario táctico y director de orquesta. Recibe las peticiones, solicita información al Modelo y le inyecta los datos resultantes a la Vista.

**Métricas de Ingeniería de Software:**
El patrón MVC ayuda a alcanzar una Alta Cohesión al asignarle una única responsabilidad específica a cada componente. Simultáneamente, logra un Bajo Acoplamiento porque los componentes interactúan mediante contratos claros, permitiendo modificar un área sin afectar directamente al resto del sistema.

---

## Parte 2: Modelado del Ciclo de Vida y Enrutamiento Semántico

### 1. Mapeo Analítico de URLs

| URL Entrante del Cliente                                       | Clase Controladora Buscada por el Framework | Método (Acción) Ejecutado | Parámetro Inyectado `id` |
| :------------------------------------------------------------- | :------------------------------------------ | :------------------------ | :----------------------- |
| `https://ingenieria.usac.edu.gt/ControlAcademico/Login`        | `ControlAcademicoController`                | `Login`                   | (Ninguno / Opcional)     |
| `https://ingenieria.usac.edu.gt/Estudiante/Historial/20260123` | `EstudianteController`                      | `Historial`               | `20260123`               |
| `https://ingenieria.usac.edu.gt/Asignacion/Detalle/10`         | `AsignacionController`                      | `Detalle`                 | `10`                     |
| `https://ingenieria.usac.edu.gt/Home`                          | `HomeController`                            | `Index`                   | (Ninguno / Opcional)     |

### 2. Diagramación del Flujo Interactivo

1. **Intercepción y Enrutamiento:** El usuario hace clic en un botón en su navegador web, generando una petición HTTP que el motor de enrutamiento evalúa para determinar a qué Controlador delegar la tarea.
2. **Dirección (Controlador):** El Controlador intercepta la petición, analiza los parámetros y decide qué acciones lógicas se requieren.
3. **Procesamiento de Datos (Modelo):** El Controlador solicita la información al Modelo. El Modelo extrae y estructura los datos desde la base de datos sin preocuparse por la interfaz.
4. **Renderizado (Vista):** El Controlador inyecta los datos limpios en la Vista. La Vista utiliza estos datos para generar dinámicamente el código HTML.
5. **Entrega de Respuesta:** El código HTML renderizado se devuelve al Controlador, quien lo envía como una respuesta HTTP final hacia el navegador del usuario.

---

## Parte 5: Referencias Bibliográficas

- Referencia Central 1: > Facultad de Ingeniería, USAC. (2026). [cite_start]Sesión 11: Modelado Base y Arquitecturas de Despliegue. [cite: 101] [cite_start]Evolución de Sistemas Distribuidos, Fundamentos del Modelo Cliente-Servidor y Diseño Físico Multi-Capas (N-Tier). [cite: 102] [cite_start]Laboratorio del curso Introducción a la Programación y Computación 2. Guatemala. [cite: 103]
- Referencia Central 2: > Facultad de Ingenieria, USAC. (2026). [cite_start]Sesión 12: Arquitectura y Componentes del Patrón MVC. [cite: 104] [cite_start]Desacoplamiento Lógico de Software, Ciclo de Vida de las Peticiones y Enrutamiento en Aplicaciones Interactivas Modernas. [cite: 105] [cite_start]Laboratorio del curso Introducción a la Programación y Computación 2. Guatemala. [cite: 106]

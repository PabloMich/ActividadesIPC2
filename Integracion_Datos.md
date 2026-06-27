### Parte 1: Evaluación Conceptual y Buenas Prácticas

#### Formatos de Intercambio

| Formato | Ventajas                                                                                                                                                                                                                    | Desventajas                                                                                                                                                                                                                                    |
| :------ | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **CSV** | • Muy ligero y con bajo consumo de almacenamiento por no tener etiquetas.<br>• Rápido de leer y procesar (parsing).<br>• Ideal para la ingesta de grandes volúmenes de datos planos o tabulares.                            | • No soporta estructuras de datos jerárquicas o anidadas de forma nativa.<br>• Carece de un estándar estricto para definir los tipos de datos (todo es texto).<br>• Difícil de validar sin un esquema externo.                                 |
| **XML** | • Soporta estructuras jerárquicas y objetos anidados complejos.<br>• Altamente descriptivo gracias a sus etiquetas de apertura y cierre.<br>• Permite la validación estricta de estructura y tipos mediante esquemas (XSD). | • Muy pesado (verboso) debido a la repetición constante de etiquetas, lo que aumenta el tamaño del archivo.<br>• Consumo intensivo de CPU al momento de procesar o parsear la información.<br>• Mayor consumo de ancho de banda al transferir. |

#### 1. Diferenciación de Procesos

Utilizando la librería nativa `System.Text.Json` en C#, la diferencia técnica radica en la dirección de la transformación de los datos en la memoria:

- **Serialización:** Es el proceso de tomar un objeto instanciado en la memoria de C# (con todas sus propiedades y estado actual) y convertirlo en una cadena de texto en formato JSON. Este proceso se utiliza comúnmente para preparar los datos antes de enviarlos a través de la red en una respuesta HTTP o guardarlos en un archivo (`JsonSerializer.Serialize()`).
- **Deserialización:** Es el proceso exactamente inverso. Consiste en tomar una cadena de texto en formato JSON (como el _payload_ recibido al consumir una API externa) y transformarlo de vuelta a una instancia de un objeto fuertemente tipado en la memoria de C# (`JsonSerializer.Deserialize<T>()`).

#### 2. El Antipatrón del Rendimiento

- **Error "N+1" en lectura de archivos:** En el contexto de carga de datos, este error ocurre cuando el sistema lee un archivo masivo y realiza una transacción de inserción a la base de datos por _cada_ línea leída. Si el archivo tiene N registros, el sistema hará N viajes hacia la base de datos (más 1 consulta inicial o de control). Esto satura la red, agota el pool de conexiones y ralentiza severamente el rendimiento debido a la latencia de entrada/salida (I/O).
- **Estrategia de Optimización por lotes (Batching):** Para solucionar esto, la estrategia consiste en procesar el archivo y agrupar los registros mapeados temporalmente en la memoria RAM (por ejemplo, en una `List<T>`). Una vez que termina la lectura o se alcanza un límite seguro (ej. 1,000 registros), se envía todo el bloque a la base de datos en una sola transacción masiva. Esto reduce drásticamente las peticiones a la base de datos de "N" a solo "1".

### Parte 2: Implementación Práctica en C#

#### Desafío 1: Consumo de Endpoints y Deserialización

```csharp
using System;
using System.Net.Http;
using System.Text.Json;
using System.Threading.Tasks;

public class ApiUSACService
{
    private readonly HttpClient _httpClient;

    public ApiUSACService(HttpClient httpClient)
    {
        _httpClient = httpClient;
    }

    public async Task<Alumno> ObtenerAlumnoAsync()
    {
        try
        {
            // Petición segura GET
            var response = await _httpClient.GetAsync("[https://api.usac.edu/v1/alumnos](https://api.usac.edu/v1/alumnos)");

            // Validación del código de estado (lanza excepción si no es 2xx)
            response.EnsureSuccessStatusCode();

            var jsonString = await response.Content.ReadAsStringAsync();

            // Configuración para insensibilidad a mayúsculas/minúsculas
            var options = new JsonSerializerOptions
            {
                PropertyNameCaseInsensitive = true
            };

            // Deserialización del payload JSON
            var alumno = JsonSerializer.Deserialize<Alumno>(jsonString, options);
            return alumno;
        }
        catch (HttpRequestException ex)
        {
            Console.WriteLine($"Error de red al consultar la API: {ex.Message}");
            throw;
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Error inesperado: {ex.Message}");
            throw;
        }
    }
}
```

#### Desafío 2: Endpoint para Carga Masiva CSV

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;

[ApiController]
[Route("api/[controller]")]
public class CargaDatosController : ControllerBase
{
private readonly ControlAcademicoContext \_context;

    public CargaDatosController(ControlAcademicoContext context)
    {
        _context = context;
    }

    [HttpPost("cargar-csv")]
    public async Task<IActionResult> CargarCsvMasivo(IFormFile archivo)
    {
        if (archivo == null || archivo.Length == 0)
        {
            return BadRequest("El archivo no es válido o está vacío.");
        }

        var listaRegistros = new List<RegistroAcademico>();

        using (var stream = archivo.OpenReadStream())
        using (var reader = new StreamReader(stream))
        {
            string linea;

            // Lectura asíncrona línea por línea con StreamReader para no saturar la RAM
            while ((linea = await reader.ReadLineAsync()) != null)
            {
                // Mapeo ficticio de la línea a un objeto
                var registro = MapearLineaCsv(linea);
                if (registro != null)
                {
                    // Almacenamiento en lista intermedia (Batching)
                    listaRegistros.Add(registro);
                }
            }
        }

        // Inserción en bloque y una única llamada a la base de datos
        _context.Registros.AddRange(listaRegistros);
        await _context.SaveChangesAsync();

        return Ok(new { Mensaje = $"Carga exitosa. Se procesaron {listaRegistros.Count} registros." });
    }

    private RegistroAcademico MapearLineaCsv(string linea)
    {
        // Lógica de mapeo y split omitida por simplicidad
        return new RegistroAcademico();
    }

}
```

### Parte 3: Referencias Bibliográficas

- Facultad de Ingeniería, USAC. (2026). _Sesión 20: Integración de Datos. Consumo de APIs Externas y Carga Masiva (CSV/XML)._ Laboratorio del curso Introducción a la Programación y Computación 2. Guatemala.

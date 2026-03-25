# Diseño de Solución y Arquitectura

## 1. Stack Tecnológico Sugerido (MVP Escalable)
* **Frontend (UI/UX):** *React.js (o Next.js) + TailwindCSS.* Permite construir la interfaz de "clics" mediante componentes reutilizables de forma rápida. Ideal para manejar estados complejos (como el borrador interactivo de la nota).
* **Backend (API):** *Node.js (Express o NestJS).* Excelente para manejar I/O asíncrono y construir las bases de la API REST para futuras integraciones.
* **Base de Datos:** *PostgreSQL.* Al ser un sistema de expedientes clínicos, la integridad referencial y las relaciones estrictas (Paciente -> Consulta -> Notas -> CIE-10) hacen que una base de datos relacional sea obligatoria para cumplir con la NOM-004 de forma segura.

## 2. Lógica del "Motor de Plantillas de Clics"
El motor funcionará mediante una arquitectura de **Árbol de Decisión y Diccionarios de Mapeo**. 

1. **Estructura JSON de la Plantilla:** Cada especialidad o motivo de consulta carga un JSON. Cada nodo del JSON es un síntoma (ej. "Dolor Abdominal").
2. **Selección y Estado:** Cuando el médico hace clic en "Dolor Abdominal", el Frontend actualiza el estado y despliega los hijos de ese nodo ("Fosa ilíaca derecha", "Punzante").
3. **Algoritmo de Concatenación (Parser):** Un parser lee el estado actual del árbol seleccionado y utiliza reglas gramaticales predefinidas. 
   * *Mapeo:* `[Síntoma] + [Localización] + [Tipo] -> "Paciente refiere dolor abdominal localizado en fosa ilíaca derecha de tipo punzante."`
4. **Inmutabilidad:** Una vez generada y firmada, la cadena de texto se guarda en la base de datos junto con un hash para evitar alteraciones, cumpliendo la normativa de trazabilidad.

## 3. Modelo de Datos Relacional (Propuesta Core)

```mermaid
erDiagram
    PACIENTE ||--o{ CONSULTA : tiene
    PACIENTE {
        uuid id PK
        string nombre_completo
        date fecha_nacimiento
        string sexo
        string domicilio
    }
    MEDICO ||--o{ CONSULTA : atiende
    MEDICO {
        uuid id PK
        string nombre
        string cedula
        string firma_digital
    }
    CONSULTA ||--|| NOTA_MEDICA : genera
    CONSULTA {
        uuid id PK
        uuid paciente_id FK
        uuid medico_id FK
        datetime fecha_hora
    }
    NOTA_MEDICA ||--o{ DIAGNOSTICO_CIE10 : incluye
    NOTA_MEDICA {
        uuid id PK
        uuid consulta_id FK
        text signos_vitales
        text interrogatorio_generado
        text exploracion_generada
        boolean firmada
        uuid creado_por FK
    }
    DIAGNOSTICO_CIE10 {
        string codigo PK
        string descripcion
    }
# Documentación de Ingeniería - MVP Deploy Me

## 1. Requisitos Funcionales (RF)
* **RF-01 (Motor de Plantillas):** El sistema debe permitir al médico construir la nota de evolución mediante la selección de botones (clics) clasificados por sistemas corporales y síntomas recurrentes.
* **RF-02 (Traducción a Lenguaje Técnico):** El sistema debe concatenar las selecciones del usuario y generar texto en lenguaje técnico-médico de forma automática, evitando el uso de abreviaturas ambiguas.
* **RF-03 (Autoguardado y Gestión de Interrupciones):** El sistema debe guardar el estado de la nota médica localmente (y sincronizar en segundo plano) cada 15 segundos para mitigar la pérdida de datos ante actividades interrumpidas.
* **RF-04 (Integración CIE-10):** El sistema debe proveer un buscador predictivo o catálogo simulado para asignar diagnósticos utilizando el estándar CIE-10.

## 2. Requisitos No Funcionales (RNF)
* **RNF-01 (Seguridad y Privacidad):** El sistema debe implementar mecanismos de autenticación, cifrado de datos en tránsito (TLS) y reposo, garantizando que los datos personales no sean divulgados.
* **RNF-02 (Trazabilidad):** Toda acción de creación o modificación de un registro médico debe generar un log (quién, cuándo, qué).
* **RNF-03 (Interoperabilidad):** La arquitectura del MVP debe exponer una API RESTful o GraphQL, sentando las bases para una futura integración transparente con el sistema central.
* **RNF-04 (Usabilidad):** La interfaz de usuario debe estar optimizada para operarse con el mínimo uso de teclado, enfocada en la rapidez requerida por la alta carga de pacientes.

## 3. Cumplimiento Normativo (NOM-004-SSA3-2012)
La arquitectura aborda las exigencias de la NOM-004 mediante los siguientes controles en el modelo de datos:
* **Identificación:** Estructuras dedicadas para los datos obligatorios del paciente (nombre, sexo, edad, domicilio) y del establecimiento simulado.
* **Historia Clínica y Notas:** Separación a nivel base de datos del interrogatorio, exploración física (signos vitales, peso, talla) y resultados. El motor de plantillas asegura el cumplimiento normativo al forzar redacciones con lenguaje técnico-médico.
* **Autenticidad:** Inclusión de un módulo de firma electrónica/digital del médico para cerrar la nota, amarrado a la trazabilidad de la sesión activa.

## 4. Historias de Usuario (Foco: Médico de Urgencias)

### HU-01: Captura Ágil por Clics
> **Como** médico de urgencias,
> **Quiero** registrar los síntomas y exploración física seleccionando opciones predefinidas en pantalla,
> **Para** reducir el tiempo de redacción manual de 20 minutos a menos de 5 minutos por paciente.
* **Criterios de Aceptación:**
    * Al hacer clic en "Cefalea", se despliegan modificadores (ej. "Frontal", "Pulsátil", "Intensidad 8/10").
    * El sistema genera automáticamente el párrafo coherente en la vista de previsualización.

### HU-02: Resiliencia ante Interrupciones
> **Como** médico de urgencias,
> **Quiero** que mi nota en progreso se guarde automáticamente,
> **Para** no perder la información si tengo que abandonar la terminal para atender un código rojo.
* **Criterios de Aceptación:**
    * El borrador se almacena en el caché local del navegador (LocalStorage/IndexedDB).
    * Al regresar a la sesión, el sistema recupera el estado exacto de los botones seleccionados.

### HU-03: Codificación Diagnóstica
> **Como** médico de urgencias,
> **Quiero** buscar el diagnóstico final tecleando palabras clave,
> **Para** que el sistema asigne el código CIE-10 correcto de un catálogo simulado.
* **Criterios de Aceptación:**
    * El buscador debe autocompletar resultados a partir del tercer carácter ingresado.
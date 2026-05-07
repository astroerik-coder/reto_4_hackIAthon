# 🚨 HackIAthon - Reto 4: Sistema de Alerta Temprana de Ingresos a Emergencias

Este repositorio contiene la solución completa para el **Reto 4**. El objetivo de este proyecto es automatizar la recepción de ingresos de urgencias médicas, cruzar los datos del paciente (póliza y pre-existencias) de forma automatizada, y usar Inteligencia Artificial (Google Gemini) para emitir un veredicto médico inicial, notificando simultáneamente a las partes correspondientes (Hospital y Aseguradora).

## 🛠️ Arquitectura de la Solución

1.  **Simulador Web (`simulador_emergencias.html`)**: Una interfaz de usuario Premium (Frontend) que dispara el evento de ingreso de un paciente.
2.  **Motor de Automatización (n8n)**: Flujo de trabajo (`reto4_n8n_workflow.json`) que procesa la lógica del negocio.
3.  **Agente Médico IA (Google Gemini)**: Analiza si los síntomas reportados en triaje representan una emergencia cubierta o un riesgo ligado al historial clínico.
4.  **Notificaciones**: Endpoints (simulados vía `webhook.site`) que reciben el JSON estructurado final para el Hospital y el Gestor del Seguro.

---

## ⚙️ Configuración Paso a Paso

Sigue estos pasos para levantar el proyecto en tu máquina o cuenta de la nube en menos de 5 minutos:

### 1. Preparar las Notificaciones (Webhook.site)
1.  El flujo ya está preconfigurado con una URL de prueba. Abre tu navegador y entra a la bandeja de entrada del webhook: [https://webhook.site/#!/4ccffef3-1618-4ca3-ba35-49e30239a201](https://webhook.site/#!/4ccffef3-1618-4ca3-ba35-49e30239a201).
2.  Mantén esa pestaña abierta, ya que allí llegarán las alertas enviadas por la Inteligencia Artificial al Hospital y al Gestor del Seguro.

### 2. Configurar n8n
1.  Abre tu instancia de **n8n** (ya sea n8n Cloud o Localhost).
2.  Ve a *Workflows* y selecciona **Import from File...**
3.  Selecciona el archivo `reto4_n8n_workflow.json` incluido en esta carpeta.
4.  Una vez cargado el flujo:
    *   Los nodos **Notificar Hospital** y **Notificar Gestor Seguro** ya tienen configurada la URL `https://webhook.site/4ccffef3-1618-4ca3-ba35-49e30239a201`. Puedes dejarla así para usar la bandeja del paso 1, o cambiarla por una propia si lo prefieres.
    *   *(Opcional)* Hay un nuevo nodo llamado **Registro en Notion** al final del flujo. Si deseas guardar un historial de los ingresos, conecta tu cuenta de Notion, crea una base de datos y vincúlala a este nodo.
5.  **Configurar la Inteligencia Artificial:**
    *   Haz clic en el nodo llamado **Google Gemini Chat Model** (o el de OpenAI si decidiste cambiarlo).
    *   En "Credential to connect with", añade tu **API Key**. *(Recuerda que la capa gratuita de Gemini permite 15 peticiones por minuto. Si da error 429, solo espera 1 minuto).*
6.  Haz clic en el primer nodo **Webhook (Ingreso)** y copia la **Test URL**.
7.  En la parte inferior de la pantalla de n8n, haz clic en **"Execute Workflow"** (o "Listen for test event"). n8n se quedará esperando la llamada.

### 3. Ejecutar el Simulador
1.  Haz doble clic en el archivo `simulador_emergencias.html` para abrirlo en tu navegador.
2.  En el campo **n8n Webhook URL**, pega la Test URL que copiaste de n8n.
3.  Ya estás listo para realizar las pruebas.

---

## 🧪 Casos de Prueba (Demo)

A continuación, puedes probar los siguientes 4 escenarios seleccionando distintos pacientes en el simulador. Observa cómo la IA reacciona diferente a cada uno:

### Prueba 1: Riesgo Relacionado (Póliza Activa)
*   **Paciente:** P-001 (Juan Pérez - Hipertensión, Arritmia)
*   **Síntomas a escribir:** *"Paciente ingresa sudoroso, con fuerte dolor punzante en el pecho y el brazo izquierdo adormecido."*
*   **Resultado esperado:** Aprobación financiera. Alerta de riesgo médico (`true`) por coincidencia con sus pre-existencias cardíacas. Notificación urgente a cardiología.

### Prueba 2: Filtro Financiero (Póliza Inactiva)
*   **Paciente:** P-002 (Ana Gómez - Póliza Inactiva)
*   **Síntomas a escribir:** *"Fuerte dolor estomacal y vómitos constantes."*
*   **Resultado esperado:** La IA detecta la póliza inactiva. Alerta al hospital de la falta de cobertura y notifica al gestor que una ex-afiliada intentó ingresar.

### Prueba 3: Aprobación sin Alerta Médica (Sin relación)
*   **Paciente:** P-003 (Carlos Ruiz - Asma Crónico)
*   **Síntomas a escribir:** *"Caída desde 2 metros de altura, posible fractura en la pierna derecha."*
*   **Resultado esperado:** Aprobación financiera. Alerta de riesgo médico (`false`), ya que una fractura no tiene impacto en su asma crónico, ahorrando revisión exhaustiva innecesaria al gestor.

### Prueba 4: Emergencia Crítica por Pre-existencia
*   **Paciente:** P-003 (Carlos Ruiz - Asma Crónico)
*   **Síntomas a escribir:** *"Dificultad severa para respirar, cianosis leve en los labios y sibilancias."*
*   **Resultado esperado:** Aprobación inmediata y alerta de riesgo máxima (`true`). El mensaje advierte explícitamente sobre su cuadro asmático para oxigenación inmediata.

---
> **Nota para el jurado/presentador:** La lógica de cruce de pre-existencias es manejada 100% por Inteligencia Artificial estructurada en n8n, demostrando razonamiento clínico sin necesidad de usar complejas bases de datos relacionales en el motor de reglas.

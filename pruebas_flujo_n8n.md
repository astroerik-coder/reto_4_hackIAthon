# Guía de Pruebas y Uso de n8n para el Reto 4

## 1. ¿Cómo importar el flujo a n8n?

Importar el flujo de automatización a tu entorno de n8n es un proceso muy rápido de tres pasos:

1. **Abre tu n8n**: Ya sea que lo tengas instalado de manera local (usualmente en `http://localhost:5678`) o en la nube (n8n Cloud).
2. **Importar desde archivo**: 
   - En el panel principal (Workflows), entra o crea un nuevo Workflow vacío.
   - En la parte superior derecha de la pantalla (o desde el menú principal de tres puntos `...` en la esquina superior derecha del canvas), busca la opción **"Import from File..."**.
   - Selecciona el archivo `reto4_n8n_workflow.json` que está en tu carpeta de proyecto.
3. **Configurar Credenciales**: 
   - Una vez que el flujo aparezca en tu pantalla, verás que el nodo llamado **Google Gemini Chat Model** puede tener un símbolo de advertencia ⚠️. 
   - Haz clic en ese nodo y selecciona o crea unas nuevas credenciales ("Credential to connect with"). Debes ingresar allí tu API Key de Google Gemini.
   - *(Si prefieres usar otro proveedor, puedes eliminar ese nodo y arrastrar el modelo de OpenAI o Groq y conectarlo al nodo principal de IA)*.

---

## 2. Casos de Prueba para el Simulador

A continuación, te propongo 4 escenarios de prueba distintos para que los ejecutes desde el **Simulador Web**. Estos escenarios demostrarán la capacidad de razonamiento de la Inteligencia Artificial.

Para hacer las pruebas, recuerda poner el flujo de n8n en modo **"Listen for test event"** (Escuchar evento de prueba) y pegar la **Test URL** del Webhook en el simulador.

### 🧪 Prueba 1: Póliza Activa y Riesgo Relacionado
Demuestra que la IA detecta que el síntoma está directamente ligado a la condición médica histórica del paciente.
* **Paciente**: P-001 (Juan Pérez - Hipertensión, Arritmia)
* **Hospital**: Hospital Central
* **Síntomas a escribir**: *"Paciente ingresa sudoroso, con fuerte dolor punzante en el pecho y el brazo izquierdo adormecido."*
* **🎯 Resultado esperado (en n8n)**: La IA debe aprobar el ingreso (validez = `true`), levantar la alerta de riesgo (`alerta_riesgo = true`) debido a que los síntomas coinciden con sus problemas cardíacos previos, y notificar urgentemente al médico y al gestor para preparar protocolos cardiológicos.

### 🧪 Prueba 2: Póliza Inactiva
Demuestra el filtro financiero y el rechazo de la IA.
* **Paciente**: P-002 (Ana Gómez - Póliza Inactiva)
* **Hospital**: Clínica San Borja
* **Síntomas a escribir**: *"Fuerte dolor estomacal y vómitos constantes."*
* **🎯 Resultado esperado (en n8n)**: La IA identificará la póliza como `false`. El mensaje para el hospital indicará que no hay cobertura financiera y sugerirá que el paciente pague particular o sea derivado a red pública, mientras el gestor recibe la notificación de la llegada de la ex-afiliada.

### 🧪 Prueba 3: Póliza Activa SIN relación a pre-existencias
Demuestra que la IA tiene criterio médico básico y no alerta por cualquier cosa.
* **Paciente**: P-003 (Carlos Ruiz - Asma Crónico)
* **Hospital**: Hospital del Norte
* **Síntomas a escribir**: *"Caída desde 2 metros de altura, posible fractura en la pierna derecha."*
* **🎯 Resultado esperado (en n8n)**: La IA aprueba el ingreso, pero marca el riesgo de pre-existencia como `false` ya que una fractura no tiene nada que ver con el asma crónico, ahorrándole al gestor una revisión innecesaria de caso complejo.

### 🧪 Prueba 4: Riesgo Respiratorio (Pre-existencia confirmada)
Demuestra el análisis de especialidades.
* **Paciente**: P-003 (Carlos Ruiz - Asma Crónico)
* **Hospital**: Hospital del Norte
* **Síntomas a escribir**: *"Dificultad severa para respirar, cianosis leve en los labios y sibilancias."*
* **🎯 Resultado esperado (en n8n)**: Aprobación inmediata y alerta extrema (`true`). El mensaje al hospital advertirá sobre el cuadro crónico asmático para que se prepare oxígeno o medicación de rescate inmediatamente.

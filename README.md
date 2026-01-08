# � Entrega de Práctica: IA Conversacional Local

## 🎯 Objetivo Alcanzado

Se ha desplegado exitosamente una **Inteligencia Artificial conversacional privada** en local utilizando tecnologías de código abierto, logrando:

- ✅ Control total y soberanía de datos
- ✅ Privacidad por diseño (sin envío a terceros)
- ✅ Independencia de APIs externas
- ✅ Sistema completamente funcional en portátil

---

## 🤖 Modelo Utilizado: SmolLM2:360m

Se ha elegido **SmolLM2:360m** como modelo para esta implementación, en lugar del modelo de mayor tamaño recomendado inicialmente.

### Razón de la Elección

| Aspecto | Detalle |
|---------|---------|
| **Hardware disponible** | Portátil con 16GB RAM + Ryzen 7 5700 (CPU only) |
| **Requisitos del modelo** | Solo 726 MB de descarga, 4GB RAM mínima |
| **Rendimiento** | Velocidad aceptable en CPU sin GPU |
| **Propósito** | Perfecto para demostración y desarrollo local |

**SmolLM2:360m** ofrece un excelente balance entre velocidad y capacidad, permitiendo respuestas rápidas en hardware limitado.

---

## 🛠️ Stack Tecnológico

| Componente | Versión | Propósito |
|------------|---------|----------|
| **Docker Compose** | v2.31.0 | Orquestación de contenedores |
| **Ollama** | Latest | Motor de ejecución de modelos LLM |
| **Open WebUI** | Main | Interfaz conversacional web |
| **SmolLM2** | 360m | Modelo de lenguaje (360M parámetros) |

---

## ✅ Resultados de la Prueba

Se han realizado **4 pruebas de funcionalidad** para verificar el correcto desempeño del modelo en diferentes tipos de tareas.

### 1️⃣ Pregunta General: ¿Qué es la Inteligencia Artificial?

**Objetivo:** Verificar que el modelo entiende conceptos generales y puede explicarlos en español.

![Pregunta General](/images/1.png)

**Resultado:** ✅ El modelo proporciona una explicación clara y precisa sobre qué es la IA y menciona sus aplicaciones actuales de forma correcta.

---

### 2️⃣ Prueba de Código: Criba de Eratóstenes

**Objetivo:** Validar la capacidad del modelo para generar código Python funcional.

![Código Parte 1](/images/2.1.png)
![Código Parte 2](/images/2.2.png)

**Resultado:** ✅ El modelo genera una función Python completa y bien documentada que implementa correctamente el algoritmo de la Criba de Eratóstenes para encontrar números primos.

---

### 3️⃣ Prueba Multilingüe: Soberanía de Datos

**Objetivo:** Demostrar comprensión de conceptos complejos en español y su importancia empresarial.

![Soberanía de Datos](/images/3.png)

**Resultado:** ✅ El modelo explica adecuadamente el concepto de soberanía de datos y su relevancia para las empresas europeas, demostrando comprensión multilingüe robusta.

---

### 4️⃣ Prueba de Razonamiento: Problema de las Cajas

**Objetivo:** Evaluar capacidad de razonamiento lógico y resolución de problemas.

![Razonamiento Lógico](/images/4.png)

**Resultado:** ✅ El modelo resuelve correctamente el acertijo de las cajas mal etiquetadas, demostrando capacidad de razonamiento deductivo.

---

## 📊 Conclusiones

| Aspecto | Evaluación | Detalle |
|---------|-----------|---------|
| **Funcionalidad** | ✅ Excelente | Todas las pruebas completadas exitosamente |
| **Velocidad** | ✅ Buena | Respuestas en 2-5 segundos (CPU) |
| **Calidad de respuestas** | ✅ Buena | Respuestas coherentes y precisas |
| **Privacidad** | ✅ Total | Todos los datos permanecen en local |
| **Reproducibilidad** | ✅ Sí | Sistema escalable y reutilizable |

### Ventajas Observadas vs Servicios de Terceros

- **Privacidad:** Conversaciones 100% locales, sin envío a servidores externos
- **Control:** Poder elegir modelo, parámetros y recursos según necesidades
- **Costo:** Inversión única en hardware, sin suscripciones mensuales
- **Latencia:** Respuestas rápidas sin depender de internet externo
- **Sostenibilidad:** Uso eficiente de recursos con SmolLM2:360m

### Casos de Uso Empresariales

1. **Chatbot interno para empresas:** Soporte técnico sin exponer datos a terceros
2. **Asistente de análisis de documentos:** Procesar información confidencial localmente sin riesgos de privacidad

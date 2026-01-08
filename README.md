# 🚀 Práctica: Construye tu Propia IA sin Dependencia de la Nube

## 🎯 Objetivo de la Práctica

En esta práctica aprenderás a desplegar tu propia **Inteligencia Artificial conversacional privada** utilizando tecnologías de código abierto. Al finalizar, habrás conseguido:

- **Control total y soberanía de datos**: Tus conversaciones y datos permanecen en tu infraestructura
- **Privacidad por diseño**: Sin envío de información a servicios de terceros
- **Independencia tecnológica**: Sin depender de APIs externas, sus costes ni sus limitaciones

Abandonaremos el modelo tradicional de la Nube para construir una solución completamente autogestionada.

---

## 🤖 El Modelo: Mistral Small 3.1

Para esta práctica utilizaremos **Mistral Small 3.1**, un modelo de última generación desarrollado por la empresa francesa Mistral AI.

### ¿Por qué Mistral Small 3.1?

| Característica | Detalle |
|----------------|---------|
| **Parámetros** | 24 mil millones (24B) |
| **Contexto** | Hasta 128.000 tokens (~100 páginas de texto) |
| **Licencia** | Apache 2.0 (totalmente libre y gratuito) |
| **Multimodal** | Comprende texto e imágenes |
| **Multilenguaje** | Excelente rendimiento en español |
| **Velocidad** | ~150 tokens/segundo |

### Rendimiento comparado

Mistral Small 3.1 **supera** a modelos propietarios como GPT-4o Mini y Gemma 3 en benchmarks de texto, comprensión multimodal y tareas multilingües, siendo además completamente de código abierto.

### Requisitos de hardware para Mistral Small 3.1

- **Con GPU**: NVIDIA RTX 4090 o superior
- **Sin GPU (CPU)**: Mínimo 32 GB de RAM (funcionará más lento pero es viable)
- **Almacenamiento**: ~14 GB para descargar el modelo

> **Nota**: Si tu equipo no cumple estos requisitos, puedes usar modelos más ligeros como alternativa (ver sección de alternativas).

---

## 🛠️ Arquitectura del Sistema

El sistema se compone de tres elementos que trabajan juntos:

```
┌─────────────────────────────────────────────────────────────┐
│                     Tu Navegador Web                        │
│                   http://localhost:3000                     │
└─────────────────────────┬───────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────┐
│                     Open WebUI                              │
│            (Interfaz gráfica - Puerto 3000)                 │
│     Tu "ChatGPT personal" con interfaz moderna              │
└─────────────────────────┬───────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────┐
│                       Ollama                                │
│              (Motor de IA - Puerto 11434)                   │
│   Ejecuta los modelos LLM de forma eficiente                │
└─────────────────────────────────────────────────────────────┘
```

### Componentes

- **Ollama**: El motor que ejecuta los modelos de lenguaje. Gestiona la descarga, optimización y ejecución de los LLMs.
- **Open WebUI**: Interfaz web que permite interactuar con Ollama de forma visual, similar a ChatGPT.
- **Docker Compose**: Orquestador que levanta ambos servicios con un solo comando y gestiona su comunicación.

---

## 📋 Guía de Despliegue Paso a Paso

### Prerrequisitos

Antes de comenzar, asegúrate de tener instalado:

1. **Docker Desktop** (Windows/Mac) o **Docker Engine** (Linux)
   - Descarga: https://www.docker.com/products/docker-desktop/
   - Verifica la instalación: `docker --version`

2. **Docker Compose** (incluido en Docker Desktop)
   - Verifica la instalación: `docker compose version`

---

### **Paso 1: Crear el entorno de trabajo**

Crea una carpeta para el proyecto y el archivo de configuración `docker-compose.yml`:

```bash
# Crear y entrar en la carpeta del proyecto
mkdir ollama-ia-local
cd ollama-ia-local
```

Crea el archivo `docker-compose.yml` con el siguiente contenido:

```yaml
services:
  ollama:
    image: ollama/ollama:latest
    container_name: ollama
    ports:
      - "11434:11434"
    volumes:
      - ollama_data:/root/.ollama
    tty: true
    restart: unless-stopped

  open-webui:
    image: ghcr.io/open-webui/open-webui:main
    container_name: open-webui
    ports:
      - "3000:8080"
    volumes:
      - openwebui_data:/app/backend/data
    environment:
      - OLLAMA_BASE_URL=http://ollama:11434
      - WEBUI_SECRET_KEY=
    extra_hosts:
      - "host.docker.internal:host-gateway"
    depends_on:
      - ollama
    restart: unless-stopped

volumes:
  ollama_data:
  openwebui_data:
```

> **Explicación del archivo:**
> - `ollama`: Servicio que ejecuta el motor de IA en el puerto 11434
> - `open-webui`: Interfaz web accesible en el puerto 3000
> - `volumes`: Almacenamiento persistente para modelos y datos
> - `depends_on`: Open WebUI espera a que Ollama esté listo

---

### **Paso 2: Levantar los servicios**

Desde la carpeta donde creaste el `docker-compose.yml`, ejecuta:

```bash
docker compose up -d
```

Este comando:
1. Descarga las imágenes de Ollama (~4.7 GB) y Open WebUI (~3.7 GB)
2. Crea los contenedores
3. Los inicia en segundo plano (`-d` = detached)

**Verifica que los contenedores estén corriendo:**

```bash
docker compose ps
```

Deberías ver algo así:
```
NAME        STATUS         PORTS
ollama      Up 2 minutes   0.0.0.0:11434->11434/tcp
open-webui  Up 2 minutes   0.0.0.0:3000->8080/tcp
```

---

### **Paso 3: Descargar el modelo Mistral Small 3.1**

Ahora descargamos el "cerebro" de nuestra IA. Ejecuta:

```bash
docker compose exec ollama ollama pull mistral-small:24b
```

**Nota**: La descarga es de aproximadamente **14 GB**. Dependiendo de tu conexión, puede tardar entre 10-30 minutos.

Puedes verificar que el modelo se descargó correctamente:

```bash
docker compose exec ollama ollama list
```

Deberías ver:
```
NAME                ID           SIZE     MODIFIED
mistral-small:24b   xxx...       14 GB    Just now
```

---

### **Paso 4: Acceder a la interfaz web**

Abre tu navegador y visita:

```
http://localhost:3000
```

#### Primer acceso - Registro de usuario

1. Al entrar por primera vez, deberás **crear una cuenta de administrador**
2. Este usuario es local (solo existe en tu máquina)
3. Completa el formulario con email y contraseña

#### Seleccionar el modelo

1. Una vez dentro, busca el selector de modelos (parte superior)
2. Selecciona **mistral-small:24b**
3. ¡Comienza a conversar!

---

## 🧪 Probando tu IA

### Conversación de prueba sugerida

Prueba estos prompts para verificar el funcionamiento:

**1. Pregunta general:**
```
¿Qué es la inteligencia artificial y cuáles son sus principales aplicaciones actuales?
```

**2. Prueba de código:**
```
Escribe una función en Python que calcule los números primos hasta N usando la Criba de Eratóstenes.
```

**3. Prueba en español:**
```
Explícame el concepto de "soberanía de datos" y por qué es importante para las empresas europeas.
```

**4. Prueba de razonamiento:**
```
Si tengo 3 cajas: una con manzanas, otra con naranjas y otra con manzanas y naranjas. Todas las etiquetas están mal puestas. Si solo puedo sacar una fruta de una caja, ¿cómo puedo etiquetar correctamente todas las cajas?
```

---

## 🔄 Modelos Alternativos

Si tu equipo no soporta Mistral Small 3.1, puedes usar estos modelos más ligeros:

| Modelo | Tamaño | RAM mínima | Comando de descarga |
|--------|--------|------------|---------------------|
| `llama3.2:3b` | ~2 GB | 8 GB | `docker compose exec ollama ollama pull llama3.2:3b` |
| `mistral:7b` | ~4 GB | 8 GB | `docker compose exec ollama ollama pull mistral:7b` |
| `gemma2:9b` | ~5 GB | 12 GB | `docker compose exec ollama ollama pull gemma2:9b` |
| `llama3.1:8b` | ~4.7 GB | 10 GB | `docker compose exec ollama ollama pull llama3.1:8b` |

---

## � Modelos Ultraligeros: Para Equipos con Recursos Mínimos

Existen modelos **extremadamente pequeños** que pueden ejecutarse con recursos mínimos. Son ideales para aprender, prototipar o entornos con hardware muy limitado.

### SmolLM2: El Modelo Más Pequeño del Mercado

**SmolLM2** es una familia de modelos desarrollada por **Hugging Face** diseñada específicamente para ejecutarse en dispositivos con recursos limitados. Es actualmente **el modelo más pequeño disponible en Ollama**.

| Variante | Parámetros | Tamaño descarga | Contexto | RAM mínima |
|----------|------------|-----------------|----------|------------|
| `smollm2:135m` | 135 millones | **271 MB** | 8K tokens | **2 GB** |
| `smollm2:360m` | 360 millones | 726 MB | 8K tokens | 4 GB |
| `smollm2:1.7b` | 1.7 mil millones | 1.8 GB | 8K tokens | 6 GB |

#### Características de SmolLM2

| Aspecto | Detalle |
|---------|---------|
| **Desarrollador** | Hugging Face |
| **Licencia** | Apache 2.0 (totalmente libre) |
| **Casos de uso** | Tareas estructuradas, chatbots simples, IoT, dispositivos móviles |
| **Ventaja principal** | Puede correr en casi cualquier hardware moderno |
| **Limitación** | Menor capacidad de razonamiento complejo y escritura creativa |

#### Instalación de SmolLM2

```bash
# Versión más pequeña (135M) - Solo 271 MB
docker compose exec ollama ollama pull smollm2:135m

# Versión intermedia (360M) - Mejor equilibrio calidad/tamaño
docker compose exec ollama ollama pull smollm2:360m

# Versión más capaz (1.7B) - Similar a modelos de 3B
docker compose exec ollama ollama pull smollm2:1.7b
```

---

### Qwen2.5-0.5B: Pequeño pero con Gran Contexto

**Qwen2.5** de Alibaba Cloud ofrece una versión de 500 millones de parámetros que destaca por tener una **ventana de contexto de 32K tokens** (4 veces más que SmolLM2), ideal si necesitas procesar textos más largos.

| Característica | Valor |
|----------------|-------|
| **Parámetros** | 500 millones (0.5B) |
| **Tamaño descarga** | 398 MB |
| **Contexto** | **32.000 tokens** (~25 páginas) |
| **RAM mínima** | 4 GB |
| **Idiomas** | 29 idiomas (incluido español) |
| **Licencia** | Apache 2.0 |

#### ¿Cuándo elegir Qwen2.5-0.5B sobre SmolLM2?

- Necesitas procesar documentos o conversaciones largas
- Requieres soporte multilingüe robusto
- Quieres mejor rendimiento en tareas de instrucciones

#### Instalación de Qwen2.5-0.5B

```bash
docker compose exec ollama ollama pull qwen2.5:0.5b
```

---

### Comparativa: SmolLM2 vs Qwen2.5-0.5B

| Criterio | SmolLM2:135m | SmolLM2:360m | Qwen2.5:0.5B |
|----------|--------------|--------------|--------------|
| **Tamaño** | 271 MB | 726 MB | 398 MB |
| **RAM mínima** | 2 GB | 4 GB | 4 GB |
| **Contexto** | 8K | 8K | **32K** |
| **Velocidad** | ⚡⚡⚡ Muy rápido | ⚡⚡ Rápido | ⚡⚡ Rápido |
| **Calidad respuestas** | Básica | Buena | **Mejor** |
| **Ideal para** | Hardware mínimo | Balance | Textos largos |

### Recomendación para Alumnos

Si tu equipo tiene **menos de 8 GB de RAM**, te recomendamos:

1. **Primera opción**: `qwen2.5:0.5b` - Mejor calidad general y mayor contexto
2. **Si tienes muy poca RAM** (< 4 GB): `smollm2:135m` - Funcionará en casi cualquier equipo

> **Nota importante**: Estos modelos ultraligeros tienen capacidades más limitadas que Mistral Small 3.1. Son excelentes para aprender y experimentar, pero no esperes el mismo nivel de razonamiento o creatividad.

---

## �🔧 Comandos Útiles

### Gestión de contenedores

```bash
# Ver estado de los servicios
docker compose ps

# Ver logs en tiempo real
docker compose logs -f

# Detener los servicios
docker compose down

# Reiniciar los servicios
docker compose restart

# Eliminar todo (contenedores + datos)
docker compose down -v
```

### Gestión de modelos

```bash
# Listar modelos descargados
docker compose exec ollama ollama list

# Descargar un modelo
docker compose exec ollama ollama pull <nombre_modelo>

# Eliminar un modelo
docker compose exec ollama ollama rm <nombre_modelo>

# Probar modelo desde terminal
docker compose exec ollama ollama run mistral-small:24b
```

---

## ⚠️ Solución de Problemas Comunes

### "No se puede conectar a localhost:3000"
- Verifica que los contenedores estén corriendo: `docker compose ps`
- Revisa los logs: `docker compose logs open-webui`

### "El modelo no aparece en Open WebUI"
- Asegúrate de que la descarga finalizó: `docker compose exec ollama ollama list`
- Refresca la página del navegador (F5)

### "Respuestas muy lentas"
- Normal si no tienes GPU
- Considera usar un modelo más pequeño
- Verifica el uso de RAM: `docker stats`

### "Error de memoria"
- Reduce el tamaño del modelo
- Cierra otras aplicaciones
- Aumenta la memoria asignada a Docker (en Docker Desktop: Settings > Resources)

---

## 🎓 Entregables de la Práctica

1. **Captura de pantalla** de Open WebUI funcionando en tu navegador mostrando:
   - El modelo seleccionado (mistral-small:24b o alternativo)
   - La interfaz lista para usar

2. **Captura de conversación** con el modelo que incluya mínimo 5 intercambios demostrando:
   - Una pregunta de conocimiento general
   - Una pregunta técnica de programación
   - Una pregunta en español que demuestre comprensión del idioma

3. **Documento reflexivo** (máximo 1 página) que incluya:
   - Especificaciones de tu equipo (CPU, RAM, GPU si aplica)
   - Modelo utilizado y razón de la elección
   - Dificultades encontradas durante el despliegue y cómo las resolviste
   - Comparativa: ¿qué diferencias observas respecto a usar ChatGPT o Claude?
   - Dos casos de uso donde desplegarías esta tecnología en un entorno empresarial

---

## 📚 Recursos Adicionales

### Documentación oficial
- [Ollama - Documentación](https://ollama.ai/docs)
- [Open WebUI - Documentación](https://docs.openwebui.com/)
- [Catálogo de modelos Ollama](https://ollama.ai/library)

### Sobre Mistral Small 3.1
- [Anuncio oficial de Mistral Small 3.1](https://mistral.ai/news/mistral-small-3-1)
- [Mistral AI - Página principal](https://mistral.ai/)

### Docker
- [Docker - Guía de inicio](https://docs.docker.com/get-started/)
- [Docker Compose - Referencia](https://docs.docker.com/compose/)

---

## 💡 Para Saber Más

### ¿Qué es un LLM?
Un **Large Language Model** (Modelo de Lenguaje Grande) es una red neuronal entrenada con cantidades masivas de texto que puede generar, comprender y razonar sobre lenguaje natural.

### ¿Por qué Ollama?
Ollama optimiza la ejecución de LLMs en hardware de consumo mediante técnicas como cuantización y gestión eficiente de memoria, permitiendo ejecutar modelos que normalmente requerirían servidores especializados.

### ¿Es esto legal?
Sí. Mistral Small 3.1 está bajo licencia Apache 2.0, que permite uso comercial y privado sin restricciones. Open WebUI usa licencia MIT y Ollama también es código abierto.

# NANDA_TESTING
# Agente Autónomo con CrewAI y NANDA sobre AWS Bedrock

<p align="center">
  Un proyecto que implementa un agente de IA autónomo utilizando el framework <b>CrewAI</b>, potenciado por el modelo <b>Claude 3.5 Haiku</b> a través de <b>AWS Bedrock</b> y expuesto como un servicio interoperable mediante <b>NANDA Adapter</b>.
</p>

---

## 👥 Integrantes del Equipo

* **Laura Rodriguez**
* **Claudia Cifuentes**
* **Mitzuko Davis Quispe**
* **David Gallo**
* **Axel Aranibar**

---

<details>
  <summary><strong>Tabla de Contenidos</strong></summary>
  <ol>
    <li><a href="#-acerca-del-proyecto">Acerca del Proyecto</a></li>
    <li><a href="#-tecnologías-utilizadas">Tecnologías Utilizadas</a></li>
    <li><a href="#-instalación-y-configuración">Instalación y Configuración</a></li>
    <li><a href="#-uso">Uso</a></li>
    <li><a href="#-estructura-del-proyecto">Estructura del Proyecto</a></li>
  </ol>
</details>

---

## 🧐 Acerca del Proyecto

Este repositorio contiene la implementación de un "Research Helper", un agente de inteligencia artificial diseñado para responder consultas de manera clara y concisa. La arquitectura se basa en tres componentes principales:

1.  **CrewAI:** Un framework de vanguardia para orquestar agentes de IA autónomos, permitiendo definir roles, metas y tareas de forma colaborativa.
2.  **AWS Bedrock:** El servicio de Amazon que provee acceso a modelos de IA de alto rendimiento. En este caso, utilizamos **Claude 3.5 Haiku** de Anthropic como el "cerebro" de nuestro agente.
3.  **NANDA Adapter:** Una herramienta que expone al agente de CrewAI como un servicio, permitiendo que otros sistemas, aplicaciones o agentes puedan interactuar con él a través de una API.

El objetivo es crear un agente especializado y hacerlo accesible para su integración en flujos de trabajo más complejos.

## 🛠️ Tecnologías Utilizadas

* **Framework de Agentes:** [CrewAI](https://www.crewai.com/) & [CrewAI Tools](https://docs.crewai.com/tools/crewai-tools/)
* **Modelo LLM:** Anthropic Claude 3.5 Haiku
* **Plataforma de IA:** [AWS Bedrock](https://aws.amazon.com/bedrock/)
* **Interoperabilidad:** [NANDA Adapter](https://github.com/NANDA-PROTOCOL/nanda-adapter)
* **Lenguaje:** Python 3.12+

## 🚀 Instalación y Configuración

Sigue estos pasos para poner en marcha el proyecto en tu entorno local.

### Prerrequisitos

* Python 3.12 o superior.
* Una cuenta de AWS con acceso a **AWS Bedrock** y al modelo `anthropic.claude-3-haiku-20240307-v1:0`.
* Tus credenciales de AWS (Access Key ID y Secret Access Key).

### Pasos de Instalación

1.  **Clona el repositorio**
    ```sh
    git clone [https://github.com/tu_usuario/tu_repositorio.git](https://github.com/tu_usuario/tu_repositorio.git)
    cd tu_repositorio
    ```

2.  **(Recomendado) Crea y activa un entorno virtual**
    ```sh
    python -m venv .venv
    source .venv/bin/activate  # En Windows: .venv\Scripts\activate
    ```

3.  **Instala las dependencias**
    Crea un archivo `requirements.txt` con el siguiente contenido:
    ```txt
    crewai
    crewai-tools
    boto3
    litellm
    nanda-adapter
    python-dotenv
    ```
    Luego, instálalo con pip:
    ```sh
    pip install -r requirements.txt
    ```

### Configuración de Credenciales

El proyecto utiliza un archivo `.env` para gestionar las credenciales de AWS de forma segura.

1.  **Crea un archivo `.env`** en la raíz del proyecto.
2.  **Añade tus credenciales de AWS** como se muestra a continuación. El proyecto se configuró con la región `us-east-1`.

    ```dotenv
    # Contenido del archivo .env
    AWS_ACCESS_KEY_ID="AKIAW...CO63"
    AWS_SECRET_ACCESS_KEY="m1gib...VLwTi"
    AWS_REGION_NAME="us-east-1"
    ```

## ▶️ Uso

Puedes ejecutar el agente de dos maneras: directamente con CrewAI o como un servicio a través de NANDA.

### 1. Ejecutar el Crew Directamente

El comando `crewai create` generó una estructura de proyecto estándar. Para probar el agente definido en los archivos de configuración:

1.  **Navega al directorio del proyecto:**
    ```sh
    cd ai_agent_claude
    ```

2.  **Ejecuta el crew:**
    ```sh
    crewai run
    ```
    Esto iniciará los agentes y tareas definidos en `src/ai_agent_claude/config/`.

### 2. Ejecutar como un Servicio NANDA

Para exponer tu agente y permitir que otras aplicaciones se comuniquen con él:

1.  **Crea un archivo `server_nanda.py`** en la raíz del proyecto con este código:
    ```python
    import os
    from crewai import Agent, Task, Crew, LLM
    from nanda_adapter import NANDA

    # El .env carga automáticamente las credenciales
    os.environ.setdefault("AWS_REGION", "us-east-1")

    # --- LLM vía Bedrock ---
    llm = LLM(
        model="bedrock/anthropic.claude-3-haiku-20240307-v1:0",
        temperature=0.1,
        max_tokens=800
    )

    helper = Agent(
        role="Research Helper",
        goal="Responder consultas breves con claridad.",
        backstory="Asistente que puede colaborar con otros agentes vía NANDA.",
        llm=llm,
        verbose=False
    )

    def crewai_improvement(message_text: str) -> str:
        task = Task(
            description=f"Responde de forma clara a: {message_text}",
            expected_output="Una respuesta breve y útil.",
            agent=helper,
        )
        out = Crew(agents=[helper], tasks=[task]).kickoff()
        return str(out)

    def main():
        print("🚀 Iniciando el servidor NANDA para el agente de CrewAI...")
        nanda = NANDA(crewai_improvement)
        nanda.start_server_api("", "localhost")

    if __name__ == "__main__":
        main()
    ```

2.  **Ejecuta el servidor:**
    ```sh
    python server_nanda.py
    ```
    El servidor se iniciará y registrará el agente, haciéndolo disponible en la red NANDA.

## 📂 Estructura del Proyecto

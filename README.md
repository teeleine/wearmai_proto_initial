# WearM.ai: Your AI Running Coach & Physio Pal! 🏃‍♀️🤖✨

Welcome to the WearM.ai codebase! This project aims to be a sophisticated AI assistant designed to help runners and athletes understand their performance, prevent injuries, and achieve their goals. By analyzing detailed biomechanical data, leveraging expert knowledge from sports science literature, and providing personalized insights through an interactive AI coach, WearM.ai strives to be your virtual physio and performance companion.

Built on a robust Django backend, WearM.ai integrates powerful Language Models (like Gemini, Claude, OpenAI via `infrastructure/llm_clients`), utilizes Weaviate for intelligent knowledge retrieval, incorporates grounding with academic sources via Linkup, and features a user-friendly Streamlit app for direct interaction with the AI coach.

## 🧠 Project Context – Initial Back-End Prototype
This repository contains the first iteration of the back-end system designed for a coaching interface that translates running sensor data into actionable feedback using Large Language Models (LLMs). Developed in Python, this prototype primarily integrates the Gemini API but remains configurable to support other LLMs as well.

The purpose of this initial prototype was to create a minimum viable product (MVP) that allowed for live testing and interaction with the system. It enabled early-stage exploration of how LLMs interpret and respond to sensor data in real-time scenarios. This version served as a foundational step in understanding how effectively an LLM could bridge the gap between raw sensor input and user-friendly coaching feedback, with a simplified UI included for testing purposes.

Its creation was driven by the need to evaluate LLM responsiveness and determine the baseline performance, capabilities, and potential optimization areas for future versions of the system.

## 🎉 Features Galore! 🎉

*   **Deep Biomechanical Analysis:** Ingests and models highly detailed time-series data for various exercises (running, walking, jumping, squats, lunges, landing), including joint angles (Pelvis, Hip, Knee, Ankle), muscle forces (Soleus, Tibialis, Gastrocnemius), and gait phases (see the intricate `core/models.py`).
*   **AI-Powered Coaching (`services/llm_coach`):** Interact with an LLM-based coach (`CoachService`) that understands your profile, performance data, goals, and conversation history. It can:
    *   Generate insightful run summaries (`services/exercise_summarisation`).
    *   Answer specific questions about performance and technique.
    *   Provide personalized training advice and plans.
    *   Explain complex biomechanical concepts.
*   **Knowledge Base Integration (`infrastructure/vectorstore`):** Uses a Weaviate vector store populated with knowledge from sports medicine texts (processed by `core/management/commands/index_knowledge_base.py`) to provide contextually relevant information.
*   **Evidence-Based Grounding (`services/grounding`):** Employs Linkup (`linkup_retriever.py`) to search academic literature and fact-check AI-generated advice, ensuring recommendations are backed by scientific evidence (as guided by `services/prompts/llm_prompts.py`).
*   **Intelligent Context Retrieval (`services/llm_coach/coach_service.py`):** The AI dynamically determines (using `FUNCTION_DETERMINANT_PROMPT`) whether to fetch raw run data (`GetRawRunData`), generate summaries (`GenerateRunSummary`), query the knowledge base (`QueryKnowledgeBase`), or retrieve grounding data (`GetGroundingAndFactCheckingData`) based on your query and conversation context.
*   **Multiple LLM Support (`infrastructure/llm_clients`):** Easily switch between different Large Language Models (Gemini, Claude, OpenAI) thanks to a factory pattern.
*   **Advanced Text Segmentation (`services/segmentation`):** Uses various strategies (SDPM, Semantic, Late) via the `chonkie` library to intelligently chunk knowledge base text for effective vectorization.
*   **Interactive Streamlit UI (`coach_ui.py`):** A clean web interface for chatting directly with your WearM.ai coach, complete with streaming responses and status updates.
*   **Robust Django Backend (`core/`, `wearmai/`):** Manages user profiles, exercise data, conversations, and provides a structured foundation. Includes a highly detailed admin interface (`core/admin.py`) powered by `nested-admin` for data exploration.
*   **Utility Functions (`common/utils`):** Provides reusable tools for tasks like statistical calculations (`stats.py`) and text cleaning (`text_cleaning.py`).

## Key Directory Deep Dive 🧭

*   **`core/` (The Heart ❤️)**
    *   **What it is:** The primary Django *app* housing the data models, data access logic, and core Django features.
    *   **What's inside:**
        *   `models.py`: Defines the detailed database schema for users, exercises (Run, Walk, etc.), intricate biomechanical data points (GaitPhase, joints, muscles), and conversation history.
        *   `serializers.py`: Translates complex Django models into formats (like JSON) suitable for the LLM or APIs (`UserProfileForLLM`, `RunDetailSerializer`).
        *   `admin.py`: Configures the Django admin interface for easy data browsing, leveraging `nested-admin` for related data.
        *   `management/commands/`: Custom management commands (`python manage.py <command_name>`) like `index_knowledge_base`, `st_coach` (demo), and `wearm_ai` (alternative demo).

*   **`services/` (The Brains & Hands 🧠🖐️)**
    *   **What it is:** Contains the core application logic, broken down into specific, modular services.
    *   **What's inside:**
        *   `llm_coach/`: The central AI orchestration hub. `coach_service.py` manages LLM interactions, context retrieval, history, and prompt generation.
        *   `prompts/`: Defines sophisticated prompt templates (`llm_prompts.py`) and expected structured outputs (`structured_outputs.py`) for the LLMs.
        *   `grounding/`: Logic for retrieving external grounding information (currently using `LinkupGroundingRetriever`).
        *   `segmentation/`: Handles breaking down text documents into chunks using various strategies (`spdm.py`, `semantic.py`, `late.py`).
        *   `exercise_summarisation/`: Contains the `ExerciseSummaryService` for calculating statistical summaries from raw exercise data.

*   **`infrastructure/` (The External Connections 🔌)**
    *   **What it is:** Manages interactions with external systems like LLMs and vector databases.
    *   **What's inside:**
        *   `llm_clients/`: Provides a standardized way (`base.py`, `factory.py`) to interact with different LLM providers (`openai_client.py`, `gemini_client.py`, `claude_client.py`).
        *   `vectorstore/`: Defines the interface (`base.py`) and implementation (`weaviate_vectorstore.py`) for communicating with the Weaviate vector database.

*   **`user_profile/` (The User Focus 👤)**
    *   **What it is:** Groups logic specifically related to handling user profile data.
    *   **What's inside:**
        *   `loader.py`: Responsible for loading and preparing user profile data, likely for use by the coaching service or serializers.

*   **`common/` (The Utility Belt 🛠️)**
    *   **What it is:** A collection of general-purpose helper functions used across the application.
    *   **What's inside:**
        *   `utils/`: Modules for statistics (`stats.py`) and text cleaning (`text_cleaning.py`).

*   **`wearmai/` (The Project Control Room ⚙️)**
    *   **What it is:** The *inner* `wearmai/` directory containing the main configuration for the *entire Django project*.
    *   **What's inside:**
        *   `settings.py`: Master configuration file defining databases, installed apps, middleware, static files, and crucially, loading API keys from environment variables.
        *   `urls.py`: Main URL routing configuration.
        *   `wsgi.py` & `asgi.py`: Configuration for web server integration.

## ✨ Key Components Spotlight ✨

*   **`core/models.py`**: The foundation defining the incredibly detailed structure of biomechanical and user data stored in the database. Understanding this reveals the depth of analysis possible.
*   **`services/llm_coach/coach_service.py`**: The central orchestrator for the AI coaching experience. It manages conversation flow, determines necessary data retrieval actions (summary vs. raw vs. KB vs. grounding), constructs complex prompts, interacts with LLMs via the factory, and maintains conversation history.
*   **`services/prompts/llm_prompts.py`**: Contains the carefully crafted instructions (prompts) guiding the LLM's behavior, analysis, grounding requirements, and output structure. The `COACH_PROMPT` is particularly vital.
*   **`infrastructure/vectorstore/weaviate_vectorstore.py` & `core/management/commands/index_knowledge_base.py`**: This pair manages the project's external knowledge. The command processes, cleans, segments, and ingests text into Weaviate, while the service allows the `CoachService` to retrieve relevant information during conversations.
*   **`infrastructure/llm_clients/factory.py` & Specific Clients**: Enables flexibility by abstracting LLM interactions, allowing easy use of different models like Gemini, Claude, or OpenAI for various tasks (e.g., function determination vs. main response generation).
*   **`core/serializers.py`**: Essential translators converting complex database models into structured JSON (`UserProfileForLLM`, `RunDetailSerializer`) needed for the `CoachService` and LLM prompts.
*   **`coach_ui.py`**: Provides the user-friendly chat interface, demonstrating how to use the `CoachService` in a web application context.
*   **`services/grounding/linkup_retriever.py`**: Implements the crucial step of fetching evidence from academic sources to validate AI recommendations, enhancing trustworthiness.


## API keys
Below is a description of each API key listed in your configuration, along with the rationale for its use within the project.

**OPENAI_API_KEY**

- **Purpose:** Grants access to OpenAI's suite of powerful language models (such as o4-mini, gpt4o-mini, etc) through their API.
- **Rationale:** Used by WearM.ai to generate natural language responses, analyse text, and provide AI-driven coaching and biomechanical insights. The flexibility to use different LLMs (including OpenAI) ensures robust, state-of-the-art language understanding and generation capabilities.

---

**WEAVIATE_URL** & **WEAVIATE_API_KEY**

- **Purpose:**  
  - `WEAVIATE_URL`: Specifies the endpoint (URL) for your Weaviate vector database instance.
  - `WEAVIATE_API_KEY`: Authenticates requests to your Weaviate instance, which is a cloud-native vector database for storing and retrieving embeddings and structured data.
- **Rationale:** Weaviate is used as the knowledge base backend, enabling fast semantic search and retrieval of contextually relevant information (e.g., sports medicine facts, biomechanics literature) to ground AI responses. The API key and URL are required for secure and correct access to your knowledge base.

---

**VOYAGEAI_API_KEY** (For Weaviate vectorization if using Voyage)

- **Purpose:** Provides access to VoyageAI's embedding models, which can generate vector representations of text for semantic search.
- **Rationale:** If configured, WearM.ai can use VoyageAI's models to vectorize (embed) knowledge base documents before storing them in Weaviate. This can improve search quality or offer an alternative to other embedding providers.

---

**GEMINI_API_KEY**

- **Purpose:** Grants access to Google's Gemini LLM API.
- **Rationale:** Enables WearM.ai to use Gemini as an alternative or complement to other LLMs (like OpenAI or Anthropic). This supports model diversity, allowing you to leverage strengths of different providers for specific tasks or fallback scenarios.

---

**ANTHROPIC_API_KEY**

- **Purpose:** Provides access to Anthropic's Claude language models via their API.
- **Rationale:** Allows WearM.ai to utilize Anthropic's models (Claude) for generating responses, supporting a multi-LLM strategy for reliability, flexibility, and potentially improved output quality in certain contexts.

---

**LINKUP_API_KEY**

- **Purpose:** Authenticates access to the Linkup service, which retrieves and grounds information from academic literature and scientific sources.
- **Rationale:** Used to fact-check and support AI-generated advice with evidence from peer-reviewed research, enhancing the trustworthiness and scientific validity of the coaching recommendations provided by WearM.ai.

---

## Summary Table

| API Key              | Service/Provider | Purpose & Rationale                                                                                      |
|----------------------|------------------|----------------------------------------------------------------------------------------------------------|
| OPENAI_API_KEY       | OpenAI           | Access to OpenAI's LLMs for text generation, analysis, and coaching logic                                |
| WEAVIATE_URL         | Weaviate         | Endpoint for the vector database storing knowledge base embeddings                                       |
| WEAVIATE_API_KEY     | Weaviate         | Authentication for secure access to Weaviate instance                                                    |
| VOYAGEAI_API_KEY     | VoyageAI         | Enables use of VoyageAI's embedding models for vectorization in Weaviate                                 |
| GEMINI_API_KEY       | Google           | Access to Gemini LLMs for alternative/complementary AI responses                                         |
| ANTHROPIC_API_KEY    | Anthropic        | Access to Claude LLMs, supporting multi-LLM flexibility                                                  |
| LINKUP_API_KEY       | Linkup           | Retrieves academic evidence to ground and fact-check AI advice                                           |

---

## Rationale for Multi-API Approach

- **Flexibility:** Multiple LLM providers (OpenAI, Gemini, Anthropic) allow you to select the best model for each task and provide redundancy if one service is unavailable.
- **Best-in-Class Retrieval:** Weaviate, combined with advanced embedding models (OpenAI, VoyageAI), ensures fast, accurate, and context-aware knowledge retrieval.
- **Trustworthiness:** Linkup integration grounds AI advice in real scientific evidence, increasing user trust and safety.
- **Scalability & Modularity:** Each API serves a distinct role, making the system modular, scalable, and adaptable to future improvements or provider changes


## 🚀 Getting Started: Let's Get Running! 🚀

Ready to bring your AI coach to life? Follow these steps:

1.  **Prerequisites:** Ensure **Python 3.11** (as mentioned in the old README) is installed.
2.  **Clone the Repository:**
    ```bash
    git clone <your-repo-url>
    cd wearmai
    ```
3.  **Create and Activate a Virtual Environment:** (Highly Recommended!)
    ```bash
    python3 -m venv venv
    source venv/bin/activate  # On Windows use `venv\Scripts\activate`
    ```
4.  **Install Dependencies:**
    ```bash
    pip install -r requirements.txt
    ```
5.  **Configure API Keys:**
    This project relies heavily on external APIs. Set the following as **environment variables**. Using a `.env` file is recommended (and ensure `.env` is in your `.gitignore`). **DO NOT COMMIT YOUR KEYS!**
    *   `OPENAI_API_KEY`
    *   `WEAVIATE_URL`
    *   `WEAVIATE_API_KEY`
    *   `VOYAGEAI_API_KEY` (For Weaviate vectorization if using Voyage)
    *   `GEMINI_API_KEY`
    *   `ANTHROPIC_API_KEY`
    *   `LINKUP_API_KEY`
    *(Verify exact names required in `wearmai/settings.py`)*

6.  **Set Up the Database:**
    ```bash
    python3 wearmai/manage.py migrate
    ```

7.  **Create a Superuser (for Admin Access):**
    ```bash
    python3 wearmai/manage.py createsuperuser
    ```
    Follow the prompts to set up your admin login.

8.  **🧠 Vectorize the Knowledge Base: 🧠**
    This command processes the knowledge base file (e.g., `wearmai/development/books/Sports Rehab Injury Prevention_clean.md`), cleans it, chunks it, and loads it into your Weaviate instance.
    ```bash
    python manage.py index_knowledge_base --debug
    ```
    *(Run this if you need to populate or update the knowledge base. The `--debug` flag provides verbose output.)*

You're all set up! Time to interact with WearM.ai.

## 🎮 How to Use WearM.ai 🎮

1.  **Streamlit Web App (Recommended):**
    For the primary interactive chat experience:
    ```bash
    python3 -m streamlit run wearmai/coach_ui.py
    ```
    Or to avoid opening a browser tab automatically:
    ```bash
    python3 -m streamlit run wearmai/coach_ui.py --server.headless true
    ```
    Access the provided URL in your browser to chat with the coach.

2.  **Coach Service Demo (Command Line):**
    Run a quick test interaction using the `CoachService` directly:
    ```bash
    python3 wearmai/manage.py start_coach_cli --debug
    ```
    *(This script executes a predefined query using the main coaching logic.)*

3.  **Django Admin Interface:**
    Explore the raw data models and database contents:
    ```bash
    python3 wearmai/manage.py runserver
    ```
    Navigate to `http://127.0.0.1:8000/admin/` in your browser and log in with your superuser credentials.

## 📊 The Data 📊

*   **Knowledge Base:** The source text for the AI's general knowledge is intended to be placed (e.g., in `wearmai/development/books/`) and processed by the `index_knowledge_base` command. The script currently references `wearmai/development/books/Sports Rehab Injury Prevention_clean.md`.
*   **User/Exercise Data:** Managed via the Django models (`core/models.py`) and stored in the configured database (`db.sqlite3` by default). The Django admin provides a way to view and manage this data.

---

Have fun exploring and developing WearM.ai! Contributions and feedback are welcome. Let's build the future of personalized athletic coaching! 🎉

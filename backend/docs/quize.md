# Onyx Codebase Onboarding Quiz

Welcome to the team! To get you up to speed and ensure you develop a deep, comprehensive understanding of the Onyx platform, I've prepared a list of questions.

Your task is to find the answers to these questions by exploring the codebase. Don't just look for a single line of code; try to understand the context, the "why" behind the implementation, and how different components interact. This will be the foundation of your work here.

Let's begin.

### Section 1: The Big Picture & Core Architecture

1.  What is the primary web framework used in this project, and where is the main application instance created?
2.  How are application-level configurations (e.g., database URLs, auth settings) managed and loaded?
3.  Explain the purpose of the `pyproject.toml` file versus the `requirements/` directory.
4.  What are the roles of the two main `Dockerfile`s (`Dockerfile` and `Dockerfile.model_server`)? What does this imply about the system's architecture?
5.  What is `Alembic` used for in this project? Where would you find the history of database schema changes?
6.  What is the role of `supervisord.conf`? What processes does it manage?
7.  How are background tasks handled in the application? Is there a specific task queue system in use?
8.  What is the purpose of the `shared_configs` directory? How does it differ from `onyx/configs`?
9.  Based on the file structure, what would you say are the 5-7 main functional pillars of the Onyx application?
10. How does the application handle multi-tenancy? Find the relevant context variables and middleware.

### Section 2: User Authentication & Authorization (`onyx/auth/`)

11. Trace the complete flow for a new user registration using BASIC auth. Start from the API endpoint and go to the database record creation.
12. What is the `UserManager` class in `onyx/auth/users.py` responsible for?
13. How does the application handle different authentication backends (e.g., `postgres` vs. `redis`)? Where is this configured?
14. Explain the lifecycle of an API key. How is it created, stored, and validated?
15. What is the difference between `current_user`, `current_admin_user`, and `optional_user` dependencies? Why have different ones?
16. How is a user's password validated for complexity? Where are these rules defined?
17. Walk through the process of a user resetting their password. What role does email play in this?
18. What is an `OAuthAccount` and how does it relate to the `User` model?
19. How does the system prevent an inactive user (`is_active=False`) from accessing protected endpoints?
20. What is the purpose of the `UserRole` enum? How is it used to enforce permissions?
21. How are session cookies configured (e.g., name, expiration)?
22. Explain the purpose of the `double_check_user` function. When is it called?
23. How does the application handle invited users? Where is the list of invited emails stored or checked?
24. What happens in the `on_after_register` callback?

### Section 3: Database and Data Models (`onyx/db/`, `alembic/`)

25. What ORM is used? How does it interact with the database?
26. In `onyx/db/models.py`, what is the relationship between a `User` and a `ChatSession`?
27. What is the purpose of the `ConnectorCredentialPair` table? Why not just link `Connector` and `Credential` directly?
28. Explain the `EncryptedString` and `EncryptedJson` custom types. How do they work?
29. What does the `delete-orphan` cascade option on a relationship (e.g., `User.oauth_accounts`) signify?
30. How are database sessions managed for API requests? Find the dependency that provides a `Session` object.
31. What is the difference between `IndexAttempt` and `IndexingStatus`?
32. What information does the `Document` model store? What do `doc_updated_at` and `last_synced` represent?
33. Explain the relationship between `Persona`, `Prompt`, and `Tool`.
34. What is the purpose of the `ChatMessage__SearchDoc` association table?
35. How does Alembic generate a new migration script? What command would you run?
36. What is the `PydanticType` custom column type used for?
37. How does the application handle database connections in an asynchronous context?
38. What is the purpose of the `Base` class in `onyx/db/models.py`?

### Section 4: Connectors & Data Ingestion (`onyx/connectors/`, `onyx/indexing/`)

39. What is the fundamental role of a "Connector" in Onyx?
40. Describe the data flow when a new file is uploaded by a user for indexing.
41. What is the difference between a `Connector` and a `Credential`?
42. How does the system handle recurring indexing (e.g., checking a source every hour)? Where is this frequency configured?
43. What happens if an indexing job for a specific `ConnectorCredentialPair` fails? How is this failure tracked?
44. What is the purpose of the `from_beginning` flag in the `IndexAttempt` model?
45. How are documents uniquely identified within the system? (Hint: look at the `Document` model's primary key).
46. What is the "pruning" process mentioned in the `Connector` model?
47. How are permissions from an external source (like Google Drive) synced and applied to documents in Onyx?
48. What is the role of the `FileStore` module?
49. Trace the code path for a "run once" indexing job initiated from the UI.

### Section 5: Search, Retrieval, and NLP (`onyx/document_index/`, `onyx/natural_language_processing/`)

50. Outline the high-level steps of how a user's search query is converted into a set of documents.
51. What is the role of the `SearchSettings` model? What key parameters does it control?
52. How does the system handle different embedding models? Can they be swapped?
53. What is "reranking"? Find where it's implemented in the search flow.
54. What is the purpose of query rephrasing? How is it triggered?
55. What information is contained within a `SearchDoc` object? How does it differ from a `Document` object?
56. How does the system implement recency bias for search results?
57. What is "multipass indexing" as suggested by the `SearchSettings` model?
58. How are access controls (permissions) enforced during a search query to ensure a user only sees documents they have access to?
59. What is the purpose of the `model_server`? What specific NLP tasks does it handle?

### Section 6: Chat, LLMs, and Prompts (`onyx/chat/`, `onyx/llm/`, `onyx/prompts/`)

60. Follow the lifecycle of a user's message in a chat. Start from the API endpoint to the point where a response is streamed back.
61. What is a `Persona`? What attributes define it?
62. How does the system select which `Prompt` to use for a given user query?
63. How are citations generated and linked to the `SearchDoc` results?
64. Explain how the `ChatMessage` model is structured to form a conversation chain. (Hint: `parent_message`).
65. How does the application interact with different LLM providers? Find the abstraction layer.
66. What is a `Tool` in the context of an LLM? How are custom tools created and used?
67. How is the chat history (`ChatMessage` records) used in generating a new response?
68. What is the purpose of the `llm_override` field in the `ChatSession` model?
69. How does the system handle streaming responses from the LLM back to the client?
70. What is the difference between `message` and `rephrased_query` in the `ChatMessage` model?
71. How are files (e.g., images) associated with a specific chat message?

### Section 7: Agents and OnyxBot (`onyx/agents/`, `onyx/onyxbot/`)

72. What distinguishes an "agentic" chat flow from a standard question-answering flow?
73. What is the primary role of the `OnyxBot`?
74. How do agents decide which tool to use for a given task?
75. Find the code that executes a `ToolCall` and stores its result.
76. What kind of tasks is the `OnyxBot` designed to handle?

### Section 8: API, Server, and Administration (`onyx/server/`)

77. How are API routes organized? (Hint: `APIRouter`).
78. What is the purpose of the Pydantic models found in `onyx/server/manage/models.py`?
79. How would an administrator deactivate a user's account? Trace the API call.
80. How are new users invited to the platform via email? Find the relevant endpoint.
81. What is the `/me` endpoint used for? What information does it return?
82. How does the backend handle requests when authentication is completely disabled (`AUTH_TYPE = "disabled"`)?
83. What is the purpose of the `PaginatedReturn` model in `onyx/server/documents/models.py`?

### Section 9: Model Server (`model_server/`)

84. What is the main entry point for the model server? (Hint: `main.py`).
85. How does the model server load embedding models?
86. What are `OnyxTorchModel` and `encoders.py` used for?
87. How would you add a new, custom embedding model to the model server?
88. What endpoints does the model server expose?
89. How does the main backend application know the address of the model server?

### Section 10: Testing and Scripts (`tests/`, `scripts/`)

90. What testing framework is used? How can you tell?
91. What is the purpose of `load_env_vars.py` in the `tests/` directory?
92. Differentiate between the `unit`, `integration`, and `regression` test suites. What would you expect to find in each?
93. How would you run the entire test suite?
94. Pick one script from the `scripts/` directory (e.g., `reset_indexes.py`) and explain what it does.
95. What is the purpose of the `chat_loadtest.py` script?
96. How are secrets and API keys handled during testing to avoid exposing real credentials?
97. What is the purpose of the `onyx_openapi_schema.py` script?
98. How would you write a new unit test for a function in `onyx/chat/chat_utils.py`?
99. What is the role of `pytest.ini`?
100. After making a code change, what steps would you take to verify it doesn't break existing functionality?

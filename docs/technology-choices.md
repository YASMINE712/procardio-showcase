# Technology choices

The explanations below describe the role and tradeoffs of the technologies found in ProCardio. A dependency's presence alone is not evidence of deployment, performance, or clinical validation.

| Technology | Role in the project | Design rationale and tradeoff |
| --- | --- | --- |
| React and TypeScript | Interactive forms, workflow navigation, graphical editors, and review panels | Components and typed data contracts help organize a large interface. Complex screens still need careful state ownership. |
| Vite | Frontend development and bundling | A focused build tool for the client. The type-check and production build remain separate quality concerns. |
| TanStack Query | Server-state fetching, caching, and mutation updates | Avoids rebuilding a query cache by hand. Configuration changes require deliberate invalidation. |
| Django and Django REST Framework | API endpoints, validation, persistence, and administration | Keeps related business workflows in a modular backend. Heavy inference in the request process can affect latency. |
| PostgreSQL | Relational application storage | Fits linked records, hospital scopes, configuration overrides, and transactional changes. |
| JWT authentication | API authentication and session checks | Supports separate frontend and backend services. Authorization still depends on current server-side account and role checks. |
| PyTorch and Ultralytics YOLO | Vision training and inference | Reuses pretrained detection and segmentation models. Model quality depends on data, split design, and evaluation. |
| OpenCV | Image preprocessing, frame handling, masks, and overlays | Gives explicit control over image transformations. Training and inference preprocessing must stay aligned. |
| Segmentation refinement integrations | Optional refinement and fallback stages around detected regions | Allows experiments beyond a single detector. Each additional stage needs evaluation rather than an assumed quality gain. |
| faster-whisper and Transformers | Speech transcription paths and local checkpoint support | Converts spoken input into reviewable text. Quality varies with audio conditions and vocabulary. |
| Ollama | Optional local language-model responses | Keeps inference locally configurable. Response latency and hardware requirements remain deployment concerns. |
| PDF extraction and OCR | Recovering document text before retrieval | Handles text PDFs and scanned pages through different paths. OCR errors can propagate into retrieval. |
| PyMuPDF and python-docx | PDF and Word export | Produces usable documents from structured report content. Layout needs visual checking in both formats. |
| SVG | Interactive diagrams and report graphics | Stores geometry explicitly and supports overlays and pointer interaction. Geometry must stay consistent across editing and export. |
| Docker and Compose | Packaging and service configuration | Defines how services fit together. Containers alone do not constitute CI/CD or prove a live deployment. |
| Django tests and Vitest | Backend and frontend automated checks | Make regressions observable. This case study does not claim a completely passing application suite. |

## Skills that deserve their own explanation

**ETL** means extracting source data, transforming it into a usable representation, and loading or exporting it to the next stage. ProCardio contains both image-dataset preparation and document-ingestion flows. These are Python application pipelines; this case study does not claim Airflow, Spark, or a data warehouse.

**Data augmentation** belongs to the training methodology, not just the dependency list. It changes training examples while keeping their annotations aligned. Its effect should be measured against a baseline on untouched evaluation data.

**RAG** combines retrieval with response generation. The current implementation retrieves using text and metadata relevance, then produces an extractive response or calls a local model. It does not require a vector database to be described accurately as retrieval-augmented functionality.

**CI/CD** needs observable automation: checks on changes, repeatable builds, an identifiable artifact, and a verified release process. The proposed extension is described separately from the existing container setup.

<h1 align="center">Zia ur Rahman</h1>

<h3 align="center">AI / ML Engineer &nbsp;•&nbsp; Infrastructure &amp; DevOps &nbsp;•&nbsp; MLOps &amp; Cloud &nbsp;•&nbsp; Computer Vision</h3>

<p align="center">
  <a href="https://github.com/ziaur390"><img src="https://img.shields.io/github/followers/ziaur390?label=Followers&style=social" alt="Followers"></a>
  <a href="https://linkedin.com/in/ziaur390"><img src="https://img.shields.io/badge/LinkedIn-Connect-blue" alt="LinkedIn"></a>
  <a href="https://scholar.google.com/citations?user=aH_kKXwAAAAJ"><img src="https://img.shields.io/badge/Google%20Scholar-Publications-4285F4" alt="Google Scholar"></a>
  <a href="https://ziarahman.netlify.app"><img src="https://img.shields.io/badge/Portfolio-ziarahman.netlify.app-teal" alt="Portfolio"></a>
</p>

---

I build AI, infrastructure and full-stack systems that reach production. My work sits across **message-driven pipelines and DevOps**, **deep learning and computer vision**, and **full-stack software engineering**, with a peer-reviewed publication and business software running in daily commercial use.

- ⚙️ **Reliable infrastructure** — an event pipeline with at-least-once delivery, dead-letter queues, Redis deduplication and Kubernetes deployment
- 🔗 **LLM & RAG systems** — citation-grounded legal retrieval and bilingual voice intake, on GCP
- 🏥 **Medical imaging** — deployed spinal X-ray diagnostic platform, awarded Best Final Year Project
- 💼 **Production business software** — a C#/.NET system pharmacy distributors run daily operations on
- 📄 **Published researcher** — peer-reviewed IoT safety architecture with measured results

> I care about systems that ship and hold up in use, not notebooks that stop at a metric.

---

## 📄 Publication

**A Decoupled Multi-Component IoT Architecture for Enhanced Safety Monitoring and Alerting in Subterranean Coal Mines**
M. Salman, I. U. Haq, **Z. U. Rahman** — *The Sciencetech* (Qurtuba University), vol. 6, no. 4, pp. 187–205, 2025
[Read the paper](https://journals.qurtuba.edu.pk/ojs/index.php/tst/article/view/86186) · [Google Scholar](https://scholar.google.com/citations?user=aH_kKXwAAAAJ)

Modular IoT safety architecture for coal mines with no dependency on pre-built underground networking: sensor units, miner smart jackets with SOS alerting, and a cloud-synchronised control unit. Published results: end-to-end alert latency under **350 ms**, **98%** of alerts delivered in under **250 ms**, **112 ± 5 m** line-of-sight range and **38 ± 4 m** through two structural walls on NRF24L01.

---

## 🚀 Featured Projects

### ⚙️ Infrastructure, Streaming & DevOps

#### 🔀 [event-pipeline](https://github.com/ziaur390/event-pipeline) — Event-Driven Data Pipeline
Reliable message processing: no lost events, no duplicate writes, no poison message blocking the queue.
**`RabbitMQ` `Apache Kafka` `Redis` `PostgreSQL` `Docker Compose` `Kubernetes` `Prometheus` `Grafana` `Nginx` `GitHub Actions`**

- **At-least-once delivery.** Events are acknowledged only after successful processing, so a consumer crash causes redelivery rather than data loss.
- **Bounded retries through a TTL delay queue.** A failed message is republished with an attempt counter to a consumer-less queue whose expiry dead-letters it back to the work queue after a delay. A malformed message therefore cannot block the queue.
- **Dead-letter queue.** After the retry budget is exhausted the message is quarantined and the failure recorded in a database table, so failed events are inspectable rather than lost.
- **Two-layer idempotency.** Atomic Redis `SET NX` on the fast path, with a PostgreSQL `UNIQUE` constraint as the authoritative backstop that survives a cache flush.
- **A real bug found and fixed:** marking events processed *before* handling them made every retry short-circuit as a duplicate, rendering the dead-letter path unreachable. Moved the dedup release into the failure path and added a regression test.
- **Kubernetes** deployment with liveness and readiness probes, resource limits, and multiple consumer replicas sharing one queue. **Prometheus** queue-depth and dead-letter-depth metrics with a provisioned Grafana dashboard.
- **Two brokers behind one interface** to compare models: RabbitMQ is a smart broker with dumb consumers (native nack, native dead-lettering); Kafka is an append-only log with consumer-managed offsets and retention.

#### 🐳 [Dockerized-API-with-CI-CD](https://github.com/ziaur390/Dockerized-API-with-CI-CD) — Containerized API with Conditional CI/CD
**`FastAPI` `SQLAlchemy` `PostgreSQL` `Docker` `Docker Compose` `GitHub Actions` `pytest`**

- PostgreSQL **integration tests** against a real database, not SQLite, with a GitHub Actions pipeline that runs the suite against a PostgreSQL service container **before** building the image
- Separate **liveness and readiness** endpoints, so a database blip cannot make orchestration restart a healthy container
- Docker Compose test profile using a disposable database service, keeping test data isolated from development data

#### 🖥️ [server-operations-lab](https://github.com/ziaur390/server-operations-lab) — Linux Server Operations
Deploy and operate a real service on Ubuntu, with the boring parts done properly.
**`Ubuntu` `Ansible` `Docker Compose` `Nginx` `PostgreSQL` `Bash` `GitHub Actions`**

- Deployed a FastAPI and PostgreSQL service behind an **Nginx reverse proxy**, with **Ansible** automating server configuration
- Bash **health-check, database backup and database restore** scripts, including a **restore into a fresh database to verify data integrity**
- **[Troubleshooting record](https://github.com/ziaur390/server-operations-lab/blob/main/docs/troubleshooting.md)** documenting twelve real failures hit during the build and how each was fixed
- Every module built on its own branch and merged through a pull request, so the review trail is visible in the history

#### 🛠️ [fregee](https://github.com/ziaur390/fregee) — Host Provisioning and Model Serving Operations
A service that provisions its own host, then measures three serving runtimes with confidence intervals instead of asserting they are fast.
**`Ansible` `Terraform` `systemd` `ufw` `Docker` `Prometheus` `Alertmanager` `Grafana` `ONNX`**

- **Host provisioning and hardening** via Ansible roles: user and SSH key-only policy, **ufw deny-by-default firewall**, Docker with log caps, logrotate, and systemd units with a dedicated service user and `ProtectSystem=strict`
- **Backup with verified restore:** a systemd timer archives, restores into a scratch database, and asserts row-count and checksum parity, **failing loudly if the restore disagrees** rather than reporting an unrecoverable backup
- **Drift detection** on a timer (PSI per feature, KS corroboration) publishing to Prometheus textfiles, with alert rules, Alertmanager and a provisioned Grafana dashboard
- **A benchmark harness with stated methodology:** three runtimes sharing one set of weights, paired bootstrap confidence intervals, and tail-power reporting. The honest finding is that int8 is **not uniformly faster** — it wins at small batches and loses at batch 64 concurrency 4
- Also documented a real export trap: quantising `Conv` produced `ConvInteger` nodes that onnxruntime's CPU provider cannot load, so the export now **validates each candidate by running it** and falls back

---

### 🧠 AI, Medical Imaging & Computer Vision

#### 🦴 [SPINEVISION-AI](https://github.com/ziaur390/SPINEVISION-AI) — Automated Spinal X-Ray Diagnosis
Full-stack medical imaging platform, deployed and in active clinical use.
**`YOLOv9c` `DenseNet-121` `U-Net` `FastAPI` `React` `PostgreSQL` `Docker` `JWT` `Gemini`**

- DenseNet-121 classification across four findings plus YOLOv9c localisation of abnormal regions; U-Net segmentation to isolate the spinal region before classification
- Grad-CAM heatmaps so clinicians see *where* the model looked; RAG-grounded report generation instead of free-form LLM text
- JWT + BCrypt auth, automated database migrations on startup, PDF report generation, zero-downtime deploy across Vercel, Render, and HuggingFace Spaces
- **Awarded Best Final Year Project, batch 2022–2026**
- 🔗 [Live demo](https://spinevision-ai.vercel.app)

#### 🏃 [Live-Pose-Detection](https://github.com/ziaur390/Live-Pose-Detection) — Real-Time Pose Estimation
**`MediaPipe BlazePose` `PySide6` `OpenCV` `NumPy` `SciPy`**
33 full-body keypoints at 30+ FPS on CPU with rep counting, form scoring, posture anomaly detection (forward head, uneven shoulders, rounded back), multi-person tracking, and annotated session export. 15 unit tests. *Collaborative project — I contributed computer-vision guidance and debugging.*

---

### 🔗 LLM, RAG & NLP

#### ⚖️ [legal-rag-assistant](https://github.com/ziaur390/legal-rag-assistant) — Citation-Grounded Legal Q&A
**`FastAPI` `Weaviate v4` `Gemini` `Vertex AI` `Google Drive API` `Pub/Sub` `GCP Cloud Run` `Docker`**
Retrieval-augmented generation over Pakistani case law and CPC sections. Google Drive ingestion with MD5 change tracking so only modified documents are re-embedded; hybrid search with cosine reranking; a retrieval-weakness heuristic that suppresses unsupported answers; structured Issue / Rule / Application / Next Step output with source citations.

#### 🎙️ [voice-intake-agent](https://github.com/ziaur390/voice-intake-agent) — Bilingual Voice Intake
**`FastAPI` `WebSockets` `GCP Speech-to-Text` `Gemini 2.5 Flash` `GCS` `Docker`**
Bilingual (Urdu/English) legal intake interviews over WebSocket audio, replacing long forms. Gemini-driven conversation manager, session state machine, structured legal-domain and urgency classification, and a six-suite pytest layer covering REST, WebSocket, and integration flows.

---

### 🏢 Full-Stack & Business Software

#### 💊 [pharmacy-management-system](https://github.com/ziaur390/pharmacy-management-system) — Production Business Software
**`C#` `.NET` `SQL Server` `Desktop UI`**
C#/.NET management system covering daily workflow, inventory, transactions, and accounts for pharmacy distribution businesses. Delivered as working software that distributors use as their day-to-day operating system, replacing manual record keeping in live businesses.

#### 📐 [Construction-Scaler](https://github.com/ziaur390/Construction-Scaler) — Blueprint Measurement Platform
**`FastAPI` `PyMuPDF` `PostgreSQL` `SQLAlchemy` `Canvas API` `Docker`**
Measures real-world distances and areas directly on construction blueprint PDFs, with automatic scale-text parsing from PDF layers (`1/8" = 1'-0"`, `1:100`). Server-side rendering, Canvas-based measurement, per-user persistence.
🔗 [Live demo](https://construction-scaler.vercel.app)

#### 🧩 [NexusCare](https://github.com/ziaur390/NexusCare) — Role-Based Community Platform
**`React 18` `Flask` `MySQL` `REST API`**
Full-stack platform with role-based access control across four roles, complaint CRUD workflows, admin statistics, and activity audit logging. BCrypt hashing, session auth, CORS, and SQL-injection prevention.

---

### 🔬 Research & Applied ML

#### ☁️ [deploy-ml-loan-predictor](https://github.com/ziaur390/deploy-ml-loan-predictor) — Fintech ML on Azure
**`Azure ML SDK` `Scikit-learn` `SQLAlchemy` `pyodbc`**
End-to-end credit-scoring deployment to Azure Cloud with database-sourced data preparation and a monitoring module tracking metrics across run IDs to detect **model and data drift**.

<details>
<summary><b>More projects</b></summary>

<br>

- [**MLOps CI/CD Pipeline**](https://github.com/ziaur390/mlops-ci-cd-pipeline) — `Jenkins` `Docker` `GitHub Actions` `FastAPI` — automated train-to-deploy lifecycle with containerised serving and automated retraining triggers
- [**fastapi-ml-docker**](https://github.com/ziaur390/fastapi-ml-docker) — `FastAPI` `Docker Compose` `Nginx` `AWS EC2` — containerised inference API with reverse proxy, health checks, and load-balancing-ready configuration
- [**Flower Classification & Detection**](https://github.com/ziaur390/Flower-Classification-and-Detection-using-AlexNet-and-YOLOv9) — `AlexNet` `YOLOv9` — comparative study of classification versus detection on one annotated dataset
- [**chatSync**](https://github.com/ziaur390/chatSync) — `Node.js` `WebSocket` — custom application-layer messaging protocol with a documented 10-message-type specification and heartbeat
- [**AI_Real_Estate_Assistant**](https://github.com/ziaur390/AI_Real_Estate_Assistant) — `RAG` `Vector DB` `FastAPI` — semantic search over property listings; architecture pattern behind my later legal retrieval system
- [**ChatBot_Tensorflow_NLP**](https://github.com/ziaur390/ChatBot_Tensorflow_NLP) — `TensorFlow` `NLP` — context- and intent-aware conversational agent

</details>

---

## 🧠 What I Work With

**Deep Learning & Computer Vision**
`PyTorch` `TensorFlow` `Keras` `Scikit-learn` `OpenCV` `YOLOv9` `DenseNet` `U-Net` `MediaPipe` `Transfer Learning` `Grad-CAM` `Custom Dataset Pipelines`

**LLMs, RAG & Agentic Tooling**
`RAG Pipelines` `Weaviate` `Document Embeddings` `Hybrid Search & Reranking` `Prompt Engineering` `Claude API` `Gemini / Vertex AI` `Model Context Protocol (MCP)` `Claude Code` `Agent Harness & Loop Design`

**DevOps, Infrastructure & Cloud**
`Docker` `Docker Compose` `Kubernetes` `Ansible` `Terraform` `systemd` `ufw` `logrotate` `Jenkins` `GitHub Actions` `Nginx` `Linux` `Bash` `RabbitMQ` `Apache Kafka` `Prometheus` `Alertmanager` `Grafana` `CloudWatch` `DNS` `SSL/TLS` `Firewalls & Security Groups` `TCP/IP` `Subnetting` `NAT` `VPNs` `AWS (EC2, S3, IAM, VPC, CloudWatch)` `Azure` `GCP (Cloud Run, GCS, Pub/Sub, Vertex AI, Cloud Scheduler)`

**MLOps**
`MLflow` `Model Versioning` `Automated Retraining` `Experiment Tracking` `Dockerised Model Serving`

**Backend & Frontend**
`Python` `FastAPI` `Flask` `REST APIs` `WebSockets` `C#` `.NET WinForms` `React` `JavaScript` `Vite` `Tailwind CSS`

**Data & Databases**
`PostgreSQL` `SQL Server` `MySQL` `SQLite` `MongoDB` `Oracle` `Redis` `SQLAlchemy` `Schema Design & Migrations` `JSONB` `Pandas` `NumPy`

---

## 💼 Experience

**AI & RAG Engineering Intern** — Advanced Telecom Services (ATS) AI Lab `Aug 2025 – Oct 2025`
Built end-to-end RAG pipelines and LLM integrations, with cloud-native CI/CD on Microsoft Azure and iterative optimisation of retrieval accuracy and latency.

**Software Engineering Intern** — Ufone (PTCL Group) `Jun 2024 – Aug 2024`
Developed .NET WinForms applications with SQL Server backends for telecom operations; redesigned operator dashboards and implemented validated CRUD systems, reducing error rates across enterprise data.

**DevOps & Cloud Engineering Intern** — Sino-Pak Center for AI (SPCAI) `Jul 2023 – Sep 2023`
Containerised deployment pipelines on AWS with Docker, CI/CD automation via GitHub Actions, Terraform infrastructure-as-code, and Linux server plus IAM administration.

---

## 🏆 Recognition

- **Best Final Year Project, BS Computer Science, batch 2022–2026** — PAF-IAST, awarded for a deployed medical AI platform in active use by doctors
- **Team Lead** on the three-person SPINEVISION-AI team — architecture, ML training, backend, and deployment ownership

---

## 🎓 Education

**BS Computer Science** — Pak-Austria Fachhochschule Institute of Applied Sciences and Technology (PAF-IAST), Haripur, Pakistan `2022 – June 2026`

Coursework: Artificial Intelligence, Machine Learning, Computer Vision, Cloud Computing, Database Systems, Distributed Systems, Software Engineering.

---

## 📚 Certifications

- Deep Learning Specialization — Coursera / deeplearning.ai
- Computer Vision & Object Detection — Kaggle / Roboflow
- Claude API & Prompt Engineering · Model Context Protocol (MCP) — Anthropic
- Docker & Kubernetes Fundamentals — Udemy
- AWS Cloud Practitioner Essentials — AWS
- Python for Data Science & AI — IBM / Coursera
- AI Fluency & Responsible AI — Anthropic / Google

---

## 🎯 Currently

- Taking **medical imaging research** toward publication — spinal pathology detection with clinical validation
- Studying **edge ML** — quantisation, pruning, and distillation for constrained devices
- Going deeper on **systems fundamentals** in Go: memory management, scheduling, execution efficiency
- Building with **AI coding agents** and learning harness internals and agent loop design from the inside

---

## 📫 Connect

<p>
  <a href="mailto:ziaurrahman.26261@gmail.com"><img src="https://img.shields.io/badge/Email-ziaurrahman.26261@gmail.com-D14836?style=flat&logo=gmail&logoColor=white" alt="Email"></a>
  <a href="https://linkedin.com/in/ziaur390"><img src="https://img.shields.io/badge/LinkedIn-ziaur390-0A66C2?style=flat&logo=linkedin&logoColor=white" alt="LinkedIn"></a>
  <a href="https://scholar.google.com/citations?user=aH_kKXwAAAAJ"><img src="https://img.shields.io/badge/Scholar-Publications-4285F4?style=flat" alt="Scholar"></a>
  <a href="https://ziarahman.netlify.app"><img src="https://img.shields.io/badge/Portfolio-Website-teal?style=flat" alt="Portfolio"></a>
</p>

<p align="center"><i>Open to AI/ML Engineer, Computer Vision, MLOps, and Full-Stack (.NET / React) roles — remote or relocation.</i></p>

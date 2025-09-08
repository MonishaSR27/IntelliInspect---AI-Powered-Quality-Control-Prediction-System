# IntelliInspect - System Architecture Diagrams

## 1. High-Level System Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        User Interface Layer                     │
├─────────────────────────────────────────────────────────────────┤
│  Angular Frontend (Port 4200)                                  │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │  Step 1: Upload Dataset                                     │ │
│  │  Step 2: Date Ranges                                        │ │
│  │  Step 3: Model Training                                     │ │
│  │  Step 4: Real-time Simulation                               │ │
│  └─────────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────┘
                                │
                                ▼ HTTP/REST API
┌─────────────────────────────────────────────────────────────────┐
│                        API Gateway Layer                        │
├─────────────────────────────────────────────────────────────────┤
│  .NET Core Backend (Port 5000)                                 │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │  Controllers:                                               │ │
│  │  - DatasetController                                        │ │
│  │  - ModelTrainingController                                  │ │
│  │  - SimulationController                                     │ │
│  │  - DataController                                           │ │
│  └─────────────────────────────────────────────────────────────┘ │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │  Services:                                                  │ │
│  │  - MLService                                                │ │
│  │  - DataIngestionService                                     │ │
│  │  - PredictionService                                        │ │
│  │  - AlertService                                             │ │
│  └─────────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────┘
                                │
                    ┌───────────┴───────────┐
                    ▼                       ▼
┌─────────────────────────┐    ┌─────────────────────────┐
│    Data Layer           │    │    ML Processing Layer  │
├─────────────────────────┤    ├─────────────────────────┤
│  SQL Server (Port 1433) │    │  Python ML Service      │
│  ┌─────────────────────┐ │    │  (Port 8000)            │
│  │  ProductionRecords  │ │    │  ┌─────────────────────┐ │
│  │  NumericFeatures    │ │    │  │  FastAPI App        │ │
│  │  CategoricalFeatures│ │    │  │  - Dataset Upload   │ │
│  │  PredictionResults  │ │    │  │  - Model Training   │ │
│  │  Alerts             │ │    │  │  - Real-time Pred   │ │
│  └─────────────────────┘ │    │  └─────────────────────┘ │
└─────────────────────────┘    │  ┌─────────────────────┐ │
                               │  │  ML Libraries        │ │
                               │  │  - XGBoost           │ │
                               │  │  - LightGBM          │ │
                               │  │  - scikit-learn      │ │
                               │  │  - pandas/numpy      │ │
                               │  └─────────────────────┘ │
                               └─────────────────────────┘
```

## 2. Data Flow Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        Data Processing Pipeline                 │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  CSV Upload → Validation → Timestamp Augmentation → Storage    │
│       │              │              │              │           │
│       ▼              ▼              ▼              ▼           │
│  File Check → Schema Check → Time Series → Database            │
│                                                                 │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐            │
│  │   Upload    │  │  Validation │  │  Processing │            │
│  │   Layer     │  │    Layer    │  │    Layer    │            │
│  └─────────────┘  └─────────────┘  └─────────────┘            │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────┐
│                        Model Training Pipeline                  │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  Data Split → Feature Engineering → Model Training → Storage   │
│       │              │                    │              │     │
│       ▼              ▼                    ▼              ▼     │
│  Train/Test → Preprocessing → Algorithm → Model Artifacts      │
│                                                                 │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐            │
│  │   Data      │  │  Feature    │  │   Model     │            │
│  │  Splitting  │  │ Engineering │  │  Training   │            │
│  └─────────────┘  └─────────────┘  └─────────────┘            │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────┐
│                        Real-Time Simulation Pipeline            │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  Historical Data → Feature Extraction → Inference → Streaming  │
│       │                  │                    │          │     │
│       ▼                  ▼                    ▼          ▼     │
│  Simulation → Preprocessing → Prediction → Live Updates        │
│                                                                 │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐            │
│  │ Historical  │  │  Feature    │  │  Real-time  │            │
│  │    Data     │  │ Extraction  │  │ Inference   │            │
│  └─────────────┘  └─────────────┘  └─────────────┘            │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

## 3. Component Interaction Sequence

```
User → Frontend → Backend → ML Service → Database
 │        │         │          │           │
 │        │         │          │           │
 ▼        ▼         ▼          ▼           ▼
1. Upload CSV File
   │        │         │          │           │
   │        │         │          │           │
   ▼        ▼         ▼          ▼           ▼
2. Process Dataset & Add Timestamps
   │        │         │          │           │
   │        │         │          │           │
   ▼        ▼         ▼          ▼           ▼
3. Validate Date Ranges
   │        │         │          │           │
   │        │         │          │           │
   ▼        ▼         ▼          ▼           ▼
4. Train ML Model
   │        │         │          │           │
   │        │         │          │           │
   ▼        ▼         ▼          ▼           ▼
5. Run Real-time Simulation
   │        │         │          │           │
   │        │         │          │           │
   ▼        ▼         ▼          ▼           ▼
6. Stream Predictions & Visualize Results
```

## 4. Docker Container Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        Docker Compose Stack                    │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐ │
│  │   Frontend      │  │    Backend      │  │   ML Service    │ │
│  │   Container     │  │   Container     │  │   Container     │ │
│  │                 │  │                 │  │                 │ │
│  │  Angular App    │  │  .NET Core API  │  │  Python FastAPI │ │
│  │  Nginx Server   │  │  HTTP Server    │  │  ML Libraries   │ │
│  │  Port: 4200     │  │  Port: 5000     │  │  Port: 8000     │ │
│  └─────────────────┘  └─────────────────┘  └─────────────────┘ │
│           │                     │                     │         │
│           │                     │                     │         │
│           └─────────────────────┼─────────────────────┘         │
│                                 │                               │
│                                 ▼                               │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │                Database Container                           │ │
│  │                                                             │ │
│  │  SQL Server 2022                                           │ │
│  │  Port: 1433                                                │ │
│  │  Persistent Volume: database-data                          │ │
│  └─────────────────────────────────────────────────────────────┘ │
│                                                                 │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │                Shared Network                               │ │
│  │                                                             │ │
│  │  intelliinspect-network (bridge)                           │ │
│  │  Internal DNS resolution                                   │ │
│  └─────────────────────────────────────────────────────────────┘ │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

## 5. API Endpoint Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        API Endpoint Structure                   │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  Frontend (Angular)                                             │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │  HTTP Client → Backend API (Port 5000)                     │ │
│  └─────────────────────────────────────────────────────────────┘ │
│                                 │                               │
│                                 ▼                               │
│  Backend (.NET Core)                                            │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │  /api/dataset/upload                                        │ │
│  │  /api/dataset/validate-date-ranges                          │ │
│  │  /api/dataset/model-status                                  │ │
│  │  /api/modeltraining/train                                   │ │
│  │  /api/simulation/run                                        │ │
│  │  /api/data/ingest                                           │ │
│  │  /api/alerts                                                │ │
│  └─────────────────────────────────────────────────────────────┘ │
│                                 │                               │
│                                 ▼                               │
│  ML Service (Python FastAPI)                                   │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │  /dataset/upload                                            │ │
│  │  /train-model                                               │ │
│  │  /simulate                                                  │ │
│  │  /predict                                                   │ │
│  │  /model/status                                              │ │
│  │  /health                                                    │ │
│  └─────────────────────────────────────────────────────────────┘ │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

## 6. Security Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        Security Layers                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │  Layer 1: Input Validation                                  │ │
│  │  - File type validation (CSV only)                          │ │
│  │  - File size limits                                         │ │
│  │  - SQL injection prevention                                 │ │
│  │  - XSS protection                                           │ │
│  └─────────────────────────────────────────────────────────────┘ │
│                                 │                               │
│                                 ▼                               │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │  Layer 2: API Security                                      │ │
│  │  - CORS configuration                                       │ │
│  │  - Request validation                                       │ │
│  │  - Rate limiting                                            │ │
│  │  - Error handling                                           │ │
│  └─────────────────────────────────────────────────────────────┘ │
│                                 │                               │
│                                 ▼                               │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │  Layer 3: Container Security                                │ │
│  │  - Non-root user execution                                  │ │
│  │  - Minimal base images                                      │ │
│  │  - Network isolation                                        │ │
│  │  - Volume security                                          │ │
│  └─────────────────────────────────────────────────────────────┘ │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

## 7. Performance Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        Performance Optimization                 │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐ │
│  │   Frontend      │  │    Backend      │  │   ML Service    │ │
│  │   Optimization  │  │  Optimization   │  │  Optimization   │ │
│  │                 │  │                 │  │                 │ │
│  │  - Lazy Loading │  │  - Async/Await  │  │  - Vectorized   │ │
│  │  - OnPush CD    │  │  - Connection   │  │    Operations   │ │
│  │  - Tree Shaking │  │    Pooling      │  │  - Memory       │ │
│  │  - Code Splitting│  │  - Caching      │  │    Management   │ │
│  └─────────────────┘  └─────────────────┘  └─────────────────┘ │
│                                                                 │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │                Database Optimization                        │ │
│  │                                                             │ │
│  │  - Indexed Queries                                          │ │
│  │  - Connection Pooling                                       │ │
│  │  - Query Optimization                                       │ │
│  │  - Partitioning                                             │ │
│  └─────────────────────────────────────────────────────────────┘ │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

This architecture provides a comprehensive view of the IntelliInspect system, showing how all components interact to deliver a scalable, maintainable, and high-performance AI-powered quality control prediction system.

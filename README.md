# IntelliInspect---AI-Powered-Quality-Control-Prediction-System
<br>
# IntelliInspect - AI-Powered Quality Control Prediction System

A full-stack AI-powered application that enables real-time quality control prediction using Kaggle Production Line sensor data. Built with Angular frontend, .NET Core backend, and Python ML service deployed in a Dockerized environment.

## System Architecture

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Angular       │    │   .NET Core     │    │   Python        │
│   Frontend      │◄──►│   Backend       │◄──►│   ML Service    │
│   (Port 4200)   │    │   (Port 5000)   │    │   (Port 8000)   │
└─────────────────┘    └─────────────────┘    └─────────────────┘
         │                       │                       │
         │                       │                       │
         ▼                       ▼                       ▼
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Nginx         │    │   SQL Server    │    │   Model Storage │
│   (Port 80)     │    │   (Port 1433)   │    │   (Volume)      │
└─────────────────┘    └─────────────────┘    └─────────────────┘
```

## Quick Start

### Prerequisites

- Docker and Docker Compose
- Git

### Installation & Deployment

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd bosch-production-line-performance
   ```

2. **Start all services**
   ```bash
   docker-compose up --build
   ```

3. **Access the application**
   - Frontend: http://localhost:4200
   - Backend API: http://localhost:5000
   - ML Service: http://localhost:8000
   - Swagger UI: http://localhost:5000

## Features

### 1. Dataset Upload & Processing
- Drag-and-drop CSV file upload
- Automatic synthetic timestamp generation
- Dataset metadata extraction
- File validation and error handling

### 2. Date Range Configuration
- Interactive date picker interface
- Training, testing, and simulation period definition
- Real-time validation and record counting
- Timeline visualization

### 3. Model Training & Evaluation
- Multiple ML algorithms (XGBoost, LightGBM, Random Forest)
- Real-time training metrics visualization
- Performance evaluation (Accuracy, Precision, Recall, F1-Score)
- Confusion matrix and feature importance

### 4. Real-Time Simulation
- Second-level granularity streaming
- Live quality prediction updates
- Real-time statistics and charts
- Interactive prediction stream table

## Technology Stack

| Layer | Technology | Version |
|-------|------------|---------|
| Frontend | Angular | 18+ |
| Backend API | ASP.NET Core | 8.0 |
| ML Service | Python + FastAPI | 3.11+ |
| ML Framework | XGBoost / LightGBM / scikit-learn | Latest |
| Database | SQL Server | 2022 |
| Deployment | Docker + Docker Compose | Latest |
| Charts | Chart.js + ng2-charts | Latest |

## Project Structure

```
bosch-production-line-performance/
├── frontend-skeleton/           # Angular Frontend
│   ├── src/app/wizard/         # 4-step wizard components
│   ├── Dockerfile              # Frontend container config
│   └── nginx.conf              # Nginx configuration
├── Backend/                    # .NET Core Backend
│   └── IntelliInspect.Backend/
│       ├── Controllers/        # API controllers
│       ├── Services/           # Business logic services
│       ├── Models/             # Data models and DTOs
│       └── Dockerfile          # Backend container config
├── ml-service/                 # Python ML Service
│   ├── main.py                 # FastAPI application
│   ├── requirements.txt        # Python dependencies
│   └── Dockerfile              # ML service container config
├── docker-compose.yml          # Multi-container orchestration
└── README.md                   # This file
```

## API Endpoints

### Dataset Management
- `POST /api/dataset/upload` - Upload and process CSV dataset
- `POST /api/dataset/validate-date-ranges` - Validate date ranges
- `GET /api/dataset/model-status` - Get model status

### Model Training
- `POST /api/modeltraining/train` - Train ML model
- `GET /api/modeltraining/status` - Get training status

### Simulation
- `POST /api/simulation/run` - Run real-time simulation
- `GET /api/simulation/status` - Get simulation status

### ML Service (Python)
- `POST /dataset/upload` - Process dataset
- `POST /train-model` - Train model
- `POST /simulate` - Run simulation
- `GET /health` - Health check

## Dataset Requirements

The application expects CSV files from the [Kaggle Bosch Production Line Performance](https://www.kaggle.com/c/bosch-production-line-performance/data) dataset with the following characteristics:

- **Required Column**: `Response` (binary classification target)
- **Optional Columns**: Various sensor readings and categorical features
- **Format**: CSV with headers
- **Size**: Up to several GB supported

### Synthetic Timestamp Generation

The system automatically adds synthetic timestamps to datasets:
- **Granularity**: 1-second intervals
- **Start Date**: 2021-01-01 00:00:00 (configurable)
- **Purpose**: Enable time-based train/test/simulation splits

## Usage Guide

### Step 1: Upload Dataset
1. Navigate to the upload screen
2. Drag and drop your CSV file or click "Choose File"
3. Wait for processing and metadata extraction
4. Review the dataset summary
5. Click "Next" to proceed

### Step 2: Configure Date Ranges
1. Set training period (e.g., Jan-Mar 2021)
2. Set testing period (e.g., Apr-Jun 2021)
3. Set simulation period (e.g., Jul-Sep 2021)
4. Click "Validate Ranges" to verify
5. Review the timeline visualization
6. Click "Next" to proceed

### Step 3: Train Model
1. Select ML algorithm (XGBoost recommended)
2. Review training configuration
3. Click "Train Model" and wait for completion
4. Analyze performance metrics and charts
5. Click "Next" to proceed

### Step 4: Run Simulation
1. Review simulation configuration
2. Click "Start Simulation"
3. Watch real-time predictions stream
4. Monitor live statistics and charts
5. Review final results

## Development

### Local Development Setup

1. **Backend Development**
   ```bash
   cd Backend/IntelliInspect.Backend
   dotnet restore
   dotnet run
   ```

2. **Frontend Development**
   ```bash
   cd frontend-skeleton
   npm install
   npm start
   ```

3. **ML Service Development**
   ```bash
   cd ml-service
   pip install -r requirements.txt
   python main.py
   ```

### Environment Variables

| Service | Variable | Default | Description |
|---------|----------|---------|-------------|
| Backend | `MLService__BaseUrl` | `http://localhost:8000` | ML service URL |
| Backend | `ConnectionStrings__DefaultConnection` | Local SQL Server | Database connection |
| ML Service | `PYTHONUNBUFFERED` | `1` | Python output buffering |

## Docker Commands

```bash
# Build and start all services
docker-compose up --build

# Start in background
docker-compose up -d

# View logs
docker-compose logs -f

# Stop all services
docker-compose down

# Rebuild specific service
docker-compose up --build frontend

# Remove volumes (clean database)
docker-compose down -v
```

## Performance Considerations

- **Dataset Size**: Optimized for datasets up to 10GB
- **Memory Usage**: ~2GB RAM per service
- **Training Time**: 2-5 minutes for typical datasets
- **Simulation Speed**: 1 prediction per second
- **Concurrent Users**: Supports multiple simultaneous sessions

## Security Features

- CORS configuration for cross-origin requests
- Input validation and sanitization
- SQL injection prevention
- File upload restrictions (CSV only)
- Non-root container execution

## Troubleshooting

### Common Issues

1. **Port Conflicts**
   ```bash
   # Check port usage
   netstat -tulpn | grep :4200
   # Change ports in docker-compose.yml
   ```

2. **Database Connection Issues**
   ```bash
   # Check database container
   docker-compose logs database
   # Restart database
   docker-compose restart database
   ```

3. **ML Service Not Responding**
   ```bash
   # Check ML service logs
   docker-compose logs ml-service
   # Verify Python dependencies
   docker-compose exec ml-service pip list
   ```

4. **Frontend Build Issues**
   ```bash
   # Clear npm cache
   docker-compose exec frontend npm cache clean --force
   # Rebuild frontend
   docker-compose up --build frontend
   ```

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests if applicable
5. Submit a pull request

## Support

For support and questions:
- Create an issue in the repository
- Check the troubleshooting section
- Review the API documentation at `/swagger`

## Demo Video

A 3-minute demo video showing the complete workflow is available in the repository, demonstrating:
- Dataset upload and processing
- Date range configuration
- Model training and evaluation
- Real-time prediction simulation

---


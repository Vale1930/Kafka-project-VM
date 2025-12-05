¡Claro\! Aquí tienes la traducción al inglés del documento:

# 🛒 E-commerce Analytics Pipeline - Usage Guide

## 📋 Description

This project implements a complete e-commerce event analysis pipeline using:

  - **Kafka**: For real-time event streaming
  - **MongoDB**: For event persistence
  - **Streamlit**: For interactive visualization and analysis

## 🏗️ Architecture

```
Producer (Python) → Kafka (ecommerce.events) → Consumer (Python) → MongoDB
                                                                        ↓
                                                            Dashboard (Streamlit)
```

## 🚀 Quick Start

### 1\. Bring Up the Infrastructure

```bash
# Start all services (Kafka, MongoDB, Consumer, Dashboard)
docker compose up -d

# Verify that all services are running
docker compose ps
```

### 2\. Generate E-commerce Events

You have two options to generate events:

#### Option A: Simple Producer (single process)

```bash
# Navigate to the src folder
cd src

# Install dependencies
pip install -r requirements.txt

# Run the producer to generate e-commerce events
# Duration: 5 minutes, Rate: 5 events/second
python producer.py --stream ecommerce --duration 5 --rate 5
```

#### Option B: Multi-Producer (multiple simultaneous processes)

```bash
# Navigate to the src folder
cd src

# Run multiple producers simultaneously
# 3 producers, each for 10 minutes at 5 events/sec
python multi_producer.py --producers 3 --duration 10 --rate 5
```

**Multi-Producer Parameters:**

  - `--producers N`: Number of producer instances to run in parallel
  - `--duration M`: Duration in minutes for each producer
  - `--rate R`: Events per second for each producer

### 3\. Verify Data Flow

#### Kafka UI

  - URL: http://localhost:8080
  - Verify that the `ecommerce.events` topic exists
  - You should see messages arriving in real time

#### Mongo Express

  - URL: http://localhost:8081
  - Username: `admin`
  - Password: `mongopass`
  - Database: `kafka_events_db`
  - Collection: `ecommerce`

### 4\. View the Dashboard

  - URL: http://localhost:8501
  - The dashboard updates automatically
  - Includes:
      - **Basic Metrics**: Count of events by type, unique users, revenue
      - **User Segmentation**:
          - By event frequency
          - By product categories
          - By device/browser type
          - By activity hours

## 📊 Generated Event Types

The producer generates the following types of e-commerce events:

1.  **page\_view**: Page view
2.  **product\_view**: Product view
3.  **add\_to\_cart**: Add to cart
4.  **remove\_from\_cart**: Remove from cart
5.  **purchase**: Completed purchase
6.  **search**: Product search
7.  **login**: Login
8.  **logout**: Logout

## 🛠️ Useful Commands

### Docker Compose

```bash
# View logs of a specific service
docker compose logs -f ecommerce-consumer
docker compose logs -f ecommerce-dashboard

# Restart a service
docker compose restart ecommerce-consumer

# Stop all services
docker compose down

# Stop and remove volumes (careful! deletes all data)
docker compose down -v
```

### MongoDB

```bash
# Connect to MongoDB from the terminal
docker exec -it kafka-lab-mongodb mongosh -u admin -p mongopass

# Inside mongosh:
use kafka_events_db
db.ecommerce.countDocuments()
db.ecommerce.find().limit(5)
```

### Kafka

```bash
# View available topics
docker exec -it kafka-lab-broker kafka-topics --list --bootstrap-server localhost:9092

# View messages from the topic (from the beginning)
docker exec -it kafka-lab-broker kafka-console-consumer \
  --bootstrap-server localhost:9092 \
  --topic ecommerce.events \
  --from-beginning \
  --max-messages 10
```

## 📈 Available Analysis on the Dashboard

### 1\. Basic Metrics

  - Total processed events
  - Unique users
  - Unique sessions
  - Total revenue (from purchases)
  - Distribution of events by type

### 2\. User Segmentation

#### By Event Frequency

  - **High**: 20+ events
  - **Medium**: 10-19 events
  - **Low**: 1-9 events

#### By Product Category

  - Electronics
  - Appliances
  - Sports
  - Accessories
  - Education

#### By Device

  - iOS (iPhone/iPad)
  - Android
  - Windows
  - macOS

#### By Activity Hour

  - **Early Morning**: 0-6 hours
  - **Morning**: 6-12 hours
  - **Afternoon**: 12-18 hours
  - **Night**: 18-24 hours

## 🎯 Ports and Services

| Port | Service | Description |
|--------|----------|-------------|
| 2181 | Zookeeper | Kafka coordination |
| 9092 | Kafka | Message broker |
| 8080 | Kafka UI | Kafka web interface |
| 8501 | Dashboard | Streamlit Dashboard |
| 27017 | MongoDB | Database |
| 8081 | Mongo Express | MongoDB web interface |

## 🐛 Troubleshooting

### The consumer is not processing events

```bash
# Check consumer logs
docker compose logs -f ecommerce-consumer

# Restart the consumer
docker compose restart ecommerce-consumer
```

### The dashboard is not showing data

1.  Verify that the consumer is running and processing events
2.  Verify in Mongo Express that the `ecommerce` collection has documents
3.  Manually refresh the dashboard from the sidebar

### Kafka is not available

```bash
# Check Kafka health
docker compose ps

# View Kafka logs
docker compose logs -f kafka

# Restart Kafka services
docker compose restart zookeeper kafka
```

## 📝 Additional Notes

  - Data is persisted in Docker volumes, so it survives restarts
  - To start with fresh data, use `docker compose down -v`
  - The dashboard has configurable auto-refresh from the sidebar
  - Producers can be run outside of Docker (on your local machine)
  - Make sure you have Python 3.11+ and dependencies installed if you run producers locally

## 🔗 Project Structure

```
project_airflow/
├── src/
│   ├── producer.py              # Main event producer
│   ├── multi_producer.py        # Multi-producer launcher
│   ├── consumers/
│   │   └── ecommerce_consumer.py   # E-commerce event consumer
│   └── dashboard/
│       ├── app.py               # Streamlit application
│       ├── Dockerfile           # Dashboard Dockerfile
│       └── requirements.txt     # Dashboard dependencies
├── docker-compose.yml           # Service orchestration
└── ECOMMERCE_SETUP.md          # This guide
```

## ✅ Verification Checklist

  - [ ] All docker-compose services are running
  - [ ] Kafka UI shows the `ecommerce.events` topic
  - [ ] The producer is generating events
  - [ ] The consumer is processing events (check logs)
  - [ ] Mongo Express shows documents in the `ecommerce` collection
  - [ ] The dashboard at http://localhost:8501 shows data

-----

### Authors:

  - Valeria Ramirez Hernandez
  - Esther Apaza
  - Daniel Valdes
  - Carlos Helguera
  - Rogelio Novelo


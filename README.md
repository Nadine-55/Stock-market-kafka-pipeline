# Real-Time Stock Market Data Pipeline
A real-time data pipeline that streams live stock market data through Apache Kafka, stores it in Amazon S3, and makes it queryable using Amazon Athena.

## Overview
This project ingests live stock price data (AAPL, MSFT, GOOGL, AMZN, TSLA) using the `yfinance` API, streams it through a Kafka producer/consumer architecture, and lands it in an S3 data lake as structured JSON. The data is then queried using Amazon Athena with a schema defined via AWS Glue.

## Architecture
yfinance API -> Kafka Producer -> Kafka Topic -> Kafka Consumer -> Amazon S3 -> AWS Glue (schema) -> Amazon Athena (SQL queries)

## Tech Stack

- **Apache Kafka**: real-time message streaming (producer/consumer)
- **Docker / Docker Compose**: containerized Kafka & ZooKeeper for local development
- **Python**: `kafka-python`, `yfinance`, `pandas`
- **Amazon S3**: data lake storage (JSON)
- **AWS Glue**: schema/table definition
- **Amazon Athena**: serverless SQL querying

## How It Works

1. **Producer** (`KafkaProducer.ipynb`): fetches live stock data via `yfinance` and publishes it to a Kafka topic
2. **Consumer** (`KafkaConsumer.ipynb`): subscribes to the topic and writes each record as a JSON file to an S3 bucket
3. **Athena** — queries the JSON files in S3 directly using a table schema defined in Glue, no data movement required

## Setup

1. Start Kafka and ZooKeeper locally:
```bash
   docker-compose up -d
```
2. Create the Kafka topic:
```bash
   docker exec -it <kafka-container-name> kafka-topics --create --topic demo_test2 --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1
```
3. Run 'KafkaProducer.ipynb' to start sending live stock data
4. Run `KafkaConsumer.ipynb` to consume and write data to S3
5. Query the data in Amazon Athena

## Key Challenges Solved

- Resolved Kafka broker networking between Docker containers and host-level Python clients ( 'advertised.listeners' configuration)
- Debugged SerDe (serialization/deserialization) compatibility issues to get Athena reading JSON-formatted data correctly
- Designed a daily-batch producer loop suitable for ongoing data collection

## Future Improvements

- Automate daily ingestion via a scheduled job instead of manual notebook runs
- Add data validation/error handling for API failures
- Visualize trends with a dashboard (e.g., QuickSight or Power BI)

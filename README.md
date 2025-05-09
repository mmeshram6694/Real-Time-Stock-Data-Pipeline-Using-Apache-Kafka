# Real-Time-Stock-Data-Pipeline-Using-Apache-Kafka and AWS
Apache Kafka · AWS EC2 · Python · Real-Time Streaming · Amazon S3 · Glue Crawler · Athena SQL · Data Ingestion · Kafka Producer · Kafka Consumer · Distributed Systems · Cloud Analytics · JSON · s3fs · kafka-python

## 🌟 Project Overview
This project simulates a **real-time stock market data pipeline** using **Apache Kafka** and **AWS cloud services**, replicating how real-time systems process financial market data. It covers ingestion, streaming, storage, and analytics using services such as EC2, S3, Glue, and Athena.

---

## 🎯 Objective
To demonstrate an end-to-end real-time data pipeline using Apache Kafka and AWS that simulates live stock market data ingestion, persistence to AWS S3, and querying via Athena. This setup mirrors real-world streaming architectures used in trading platforms.

---

## 🔧 Tech Stack
- **Apache Kafka** (Producer, Broker, Consumer)
- **ZooKeeper** (Kafka cluster coordination)
- **AWS EC2** (Kafka, ZooKeeper, Python apps)
- **Python** (data simulation, ingestion, storage)
- **Amazon S3** (data lake for JSON storage)
- **AWS Glue** (schema cataloging)
- **Amazon Athena** (serverless analytics)

---

## 📐 Architecture Overview
```
[CSV Dataset → Python Producer → Kafka (EC2) → Consumer → S3 → Glue Crawler → Athena]
```

---

## 📷 Architecture Diagram 

### 📊 Kafka 
![Kafka_Architecture](Kafka_Architecture.png)

---

## 🔄 Step-by-Step Implementation

### 1. Kafka Setup on EC2
Provisioned an Ubuntu 20.04 LTS **t2.micro** EC2 instance and installed:
- Java 1.8 (OpenJDK)
- Apache Kafka and ZooKeeper (Kafka 2.12-3.3.1)

**Optimization:**
```bash
export KAFKA_HEAP_OPTS="-Xmx256M -Xms128M"
```
Updated `server.properties`:
```bash
advertised.listeners=PLAINTEXT://<your-ec2-public-ip>:9092
```

Created Topic:
```bash
bin/kafka-topics.sh --create --topic demo_testing2 --bootstrap-server <your-ec2-public-ip>:9092 --replication-factor 1 --partitions 1
```

---

### 2. Python Kafka Producer – Real-Time Simulation
Used **pandas** and **kafka-python** to stream real-time stock data from CSV.
```python
import pandas as pd
from kafka import KafkaProducer
from time import sleep
from json import dumps

producer = KafkaProducer(bootstrap_servers=['<your-ec2-public-ip>:9092'],
                         value_serializer=lambda x: dumps(x).encode('utf-8'))

df = pd.read_csv("data/indexProcessed.csv")

while True:
    data = df.sample(1).to_dict(orient="records")[0]
    producer.send('demo_testing2', value=data)
    sleep(1)
```

---

### 3. Kafka Consumer – Store JSON in S3
```python
from kafka import KafkaConsumer
from json import loads, dump
from s3fs import S3FileSystem

consumer = KafkaConsumer('demo_testing2',
                         bootstrap_servers=['<your-ec2-public-ip>:9092'],
                         value_deserializer=lambda x: loads(x.decode('utf-8')))

s3 = S3FileSystem()
for count, msg in enumerate(consumer):
    with s3.open(f"s3://kafka-stock-market/stock_{count}.json", 'w') as file:
        dump(msg.value, file)
```

---

### 4. AWS Glue Crawler
- Pointed to S3 bucket
- Created Glue database and catalog table
- Auto-detected schema (fields like symbol, price, timestamp)

---

### 5. Athena SQL Queries
Example:
```sql
SELECT symbol, AVG(price) as avg_price
FROM stock_data_table
GROUP BY symbol
ORDER BY avg_price DESC
LIMIT 5;
```

---

## 🎓 Learnings & Takeaways
- How to deploy and configure Kafka on a cloud VM
- Deep understanding of Kafka-ZooKeeper coordination and EC2 networking
- Importance of resource optimization on EC2 (heap, threading)
- Streaming to S3 using Boto3 and JSON
- Creating Athena-ready schemas with Glue

---

---

## 📊 Business Recommendations
- For high-volume data ingestion systems, consider Kafka + AWS Glue + Athena as a minimal-maintenance alternative to traditional ETL pipelines
- For MVPs in FinTech or IoT, this modular pipeline enables quick prototyping of event-driven analytics

---

---

## 💡 Key Insights & Optimization Results
- Insight: Message queueing with Kafka decouples ingestion and analytics
- Optimization: Lowered EC2 memory usage via KAFKA_HEAP_OPTS tuning
- Result: Improved consistency and throughput in live streaming simulation
- Result: Improved consistency and throughput in live streaming simulation

---  

## 📊 Outcomes
- Working real-time stock market pipeline
- Cloud-native, scalable, analytics-ready design
- Foundation for integration with dashboards, ML, or alerting tools

---

## 🚀 Future Work
- Add data visualization (Amazon QuickSight / Power BI)
- Add anomaly detection with SageMaker
- Deploy Kafka in Docker with multi-broker clustering

---

## 🧑‍💻 
**Mayur Meshram**  
UMass Dartmouth | Spring 2025 | MIS 696-01 / MIS 681

---

## 📚 References
- [Kafka Documentation](https://kafka.apache.org/documentation/)
- [AWS Glue](https://aws.amazon.com/glue/)
- [AWS Athena](https://aws.amazon.com/athena/)
- [Kafka: The Definitive Guide – O’Reilly](https://www.oreilly.com/library/view/kafka-the-definitive/9781491936153/)
- [AWS S3](https://aws.amazon.com/s3/)

---

## 📬 Connect
Interested in real-time data, Kafka, or cloud analytics? 
Connect with me on 📧 mmeshram@umassd.edu  
🔗https://www.linkedin.com/in/mayur-meshram9/ 
Or explore the repo!

# 💳 Payment Service (Sistema de Pagamentos Simulado)

## 🧩 Descrição Curta

Microserviço em **Java (Spring Boot)** responsável por processar pagamentos de pedidos, consumindo mensagens da fila **AWS SQS** e registrando o resultado no **PostgreSQL**. Inclui integração com **AWS Lambda** e **API Gateway** para endpoints serverless.

**Custo estimado:** 💰 R$ 0,00 (dentro do AWS Free Tier)

---

## 🚀 Visão Geral do Projeto

O Payment Service simula o fluxo de autorização e confirmação de pagamentos:

* Consome mensagens do SQS enviadas pelo Order Service.
* Realiza a “simulação” de aprovação ou recusa.
* Armazena o histórico de transações.
* Publica a resposta de volta na fila (`payment.completed` ou `payment.failed`).

### Tecnologias Principais

* **Java 17 / Spring Boot**
* **AWS SQS / Lambda / API Gateway**
* **PostgreSQL / DynamoDB (logs)**
* **CloudWatch (métricas e logs)**

---

## ⚙️ Arquitetura

```
Order Service → AWS SQS → Payment Service → PostgreSQL
                                   ↓
                            CloudWatch Logs
                                   ↓
                           AWS Lambda (notificação)
```

---

## 🧰 Setup Local

### 1️⃣ Clonar o projeto

```bash
git clone https://github.com/seu-usuario/payment-service.git
cd payment-service
```

### 2️⃣ Subir LocalStack e PostgreSQL

```bash
docker-compose up -d
```

### 3️⃣ Criar fila no LocalStack

```bash
awslocal sqs create-queue --queue-name payment-queue
```

### 4️⃣ Configurar `.env`

```bash
DB_URL=jdbc:postgresql://localhost:5432/payments
DB_USER=postgres
DB_PASS=postgres
AWS_REGION=us-east-1
AWS_SQS_URL=http://localhost:4566/000000000000/payment-queue
```

### 5️⃣ Rodar aplicação

```bash
./mvnw spring-boot:run
```

---

## 🧪 Teste Manual

Enviar uma mensagem simulada na fila:

```bash
awslocal sqs send-message \
  --queue-url http://localhost:4566/000000000000/payment-queue \
  --message-body '{"orderId": 10, "amount": 250.00, "method": "PIX"}'
```

Ver log da aplicação e resposta `payment.completed`.

---

## ☁️ Deploy AWS (Lambda + API Gateway)

1. Empacotar com Maven:

```bash
./mvnw clean package -DskipTests
```

2. Criar função Lambda no painel AWS.
3. Upload do JAR (handler configurado no `application.yml`).
4. Integrar com **API Gateway** para expor endpoint `/payment`.

---

## 📈 Monitoramento e Logs

* Logs de processamento no **CloudWatch**.
* Métricas de sucesso/falha de pagamentos.
* DLQ configurada para mensagens não processadas.

---

## 🧾 Custos Estimados

| Serviço              | Custo (Free Tier)    |
| -------------------- | -------------------- |
| AWS Lambda           | ✅ 1M exec/mês grátis |
| AWS SQS              | ✅ 1M req/mês grátis  |
| AWS API Gateway      | ✅ 1M req/mês grátis  |
| AWS RDS (PostgreSQL) | ✅ 750h/mês grátis    |
| CloudWatch           | ✅ 5GB/mês grátis     |
| **Total estimado**   | 💵 **R$ 0,00/mês**   |

---

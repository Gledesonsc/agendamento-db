# 📋 Sistema de Agendamento de Atendimento com PostgreSQL  

Este guia descreve como configurar um banco de dados PostgreSQL para um sistema de agendamento de atendimento, permitindo verificar horários disponíveis e realizar agendamentos sem vínculo com clientes.  

---

## 🛠️ Requisitos  

- PostgreSQL instalado (v13 ou superior recomendado)  
- Ferramentas de acesso ao banco de dados (como `psql`, DBeaver ou PgAdmin)  
- Ambiente de desenvolvimento configurado  

---

## 📁 Estrutura do Banco de Dados  

O banco de dados terá duas tabelas principais:  

### 1. **Tabela `appointments`**  
Registra os horários agendados.  

| Campo          | Tipo         | Restrição                     |
|----------------|--------------|-------------------------------|
| `id`           | SERIAL       | PRIMARY KEY                   |
| `date`         | DATE         | NOT NULL                     |
| `time`         | TIME         | NOT NULL                     |
| `status`       | VARCHAR(20)  | DEFAULT 'scheduled'          |
| `created_at`   | TIMESTAMP    | DEFAULT CURRENT_TIMESTAMP    |

---

### 2. **Tabela `availability`**  
Armazena os horários disponíveis para agendamentos.  

| Campo          | Tipo         | Restrição              |
|----------------|--------------|------------------------|
| `id`           | SERIAL       | PRIMARY KEY            |
| `date`         | DATE         | NOT NULL               |
| `time`         | TIME         | NOT NULL               |
| `is_available` | BOOLEAN      | DEFAULT TRUE           |

---

## 🧑‍💻 Configuração do Banco de Dados  

### 📌 Criando o Banco de Dados  

No terminal, execute:  
```sql
CREATE DATABASE scheduling_system;
```  

Acesse o banco de dados criado:  
```sql
\c scheduling_system;
```  

---

### 📌 Criando as Tabelas  

Crie as tabelas executando os comandos SQL:  

**Tabela `appointments`:**  
```sql
CREATE TABLE appointments (
    id SERIAL PRIMARY KEY,
    date DATE NOT NULL,
    time TIME NOT NULL,
    status VARCHAR(20) DEFAULT 'scheduled',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```  

**Tabela `availability`:**  
```sql
CREATE TABLE availability (
    id SERIAL PRIMARY KEY,
    date DATE NOT NULL,
    time TIME NOT NULL,
    is_available BOOLEAN DEFAULT TRUE
);
```  

---

## 🔄 Lógica para Verificação e Agendamento  

### 1. **Inserir Horários Disponíveis**  
Adicione horários disponíveis para agendamento:  
```sql
INSERT INTO availability (date, time) 
VALUES 
('2024-11-25', '09:00'),
('2024-11-25', '10:00'),
('2024-11-25', '11:00');
```  

### 2. **Verificar Disponibilidade**  
Para verificar horários disponíveis em uma data específica:  
```sql
SELECT time 
FROM availability 
WHERE date = '2024-11-25' AND is_available = TRUE;
```  

### 3. **Agendar Atendimento**  
Para realizar o agendamento:  

1. Verifique se o horário está disponível:  
   ```sql
   SELECT is_available 
   FROM availability 
   WHERE date = '2024-11-25' AND time = '10:00';
   ```  

2. Realize o agendamento:  
   ```sql
   BEGIN;

   -- Insira o agendamento
   INSERT INTO appointments (date, time) 
   VALUES ('2024-11-25', '10:00');

   -- Atualize a disponibilidade
   UPDATE availability 
   SET is_available = FALSE 
   WHERE date = '2024-11-25' AND time = '10:00';

   COMMIT;
   ```  

---

## 🗑️ Cancelamento de Atendimento  

Caso um atendimento seja cancelado, atualize o horário para disponível:  
```sql
UPDATE availability 
SET is_available = TRUE 
WHERE date = '2024-11-25' AND time = '10:00';

DELETE FROM appointments 
WHERE date = '2024-11-25' AND time = '10:00';
```  

---

## 📊 Diagrama ER  

### Estrutura Relacional do Banco de Dados  

```plaintext
+----------------+     +------------------+
|  appointments  |     |   availability   |
+----------------+     +------------------+
| id (PK)        |     | id (PK)          |
| date           |     | date             |
| time           |     | time             |
| status         |     | is_available     |
| created_at     |     |                  |
+----------------+     +------------------+
```  

---

## 🔍 Índices e Otimização  

- **Índice para Tabela `availability`:**  
  ```sql
  CREATE INDEX idx_availability_date_time 
  ON availability (date, time);
  ```  

- **Índice para Tabela `appointments`:**  
  ```sql
  CREATE INDEX idx_appointments_date_time 
  ON appointments (date, time);
  ```  

---

## 🎉 Finalização  

Com essa estrutura, o sistema de agendamento está pronto para gerenciar atendimentos com eficiência. Para dúvidas ou melhorias, contribua com este projeto no GitHub! 

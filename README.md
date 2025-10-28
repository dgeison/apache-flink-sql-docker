# Apache Flink SQL Client - Ambiente Docker

Este projeto configura um ambiente completo do Apache Flink com SQL Client usando Docker Compose, desenvolvido para fins educacionais na disciplina **Camadas e Serviços de Consumo de Dados** do curso de **Arquitetura e Engenharia de Dados** da PUC Minas.

## 📋 Sobre o Projeto

O Apache Flink é uma plataforma de código aberto para processamento de dados em tempo real (streaming) e em lote (batch). Este ambiente permite:

- **Processamento de Stream e Batch**: Analise dados em tempo real ou processos em lote
- **SQL Nativo**: Use SQL puro para criar pipelines de dados complexos
- **Conectores Múltiplos**: Suporte a Kafka, JDBC, Elasticsearch e outros
- **Interface Web**: Dashboard completo para monitoramento
- **Ambiente Isolado**: Configuração completa via Docker

### 🎯 Objetivos de Aprendizado

- Compreender os conceitos de processamento de dados distribuído
- Praticar SQL para pipelines de dados em tempo real
- Trabalhar com tecnologias de Big Data e streaming
- Entender arquiteturas de dados modernas

## 🏗️ Arquitetura do Sistema

O ambiente é composto por 3 contêineres Docker:

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   SQL Client    │    │   Job Manager   │    │  Task Manager   │
│                 │    │                 │    │                 │
│ Interface SQL   │◄──►│ Coordenador     │◄──►│ Executor de     │
│ para Flink      │    │ do Cluster      │    │ Tarefas         │
│                 │    │                 │    │                 │
└─────────────────┘    └─────────────────┘    └─────────────────┘
        │                       │                       │
        └───────────────────────┼───────────────────────┘
                                │
                    ┌─────────────────┐
                    │   Dados CSV     │
                    │   /data/        │
                    └─────────────────┘
```

### Componentes:

- **Job Manager**: Coordena o cluster Flink e gerencia os jobs
- **Task Manager**: Executa as tarefas de processamento de dados
- **SQL Client**: Interface interativa para executar comandos SQL
- **Web UI**: Dashboard web disponível em `http://localhost:8081`

## 🚀 Como Usar

### Pré-requisitos

- Docker Desktop instalado
- Docker Compose disponível
- Pelo menos 4GB de RAM livres

### 1. Iniciando o Ambiente

```bash
# Navegue até o diretório do projeto
cd flink-sql-cli-docker

# Inicie todos os serviços em background
docker-compose up -d

# Verifique se os contêineres estão rodando
docker-compose ps
```

**Saída esperada:**
```
    Name                 Command            State                    Ports                  
------------------------------------------------------------------------------------------
jobmanager      /docker-entrypoint.sh   Up      6123/tcp, 0.0.0.0:8081->8081/tcp        
sql-client      /docker-entrypoint.sh   Up                                               
taskmanager     /docker-entrypoint.sh   Up      6121-6125/tcp                            
```

### 2. Acessando o SQL Client

```bash
# Entre no contêiner SQL Client
docker exec -it sql-client /bin/bash

# Inicie o cliente SQL do Flink
./sql-client.sh
```

### 3. Experimentando com SQL

#### Criando uma Tabela de Exemplo

```sql
CREATE TABLE people_job (
    id INT,
    name STRING,
    job STRING,
    salary BIGINT
) WITH (
    'connector' = 'filesystem',
    'path' = 'file:///data/test.csv',
    'format' = 'csv',
    'csv.ignore-parse-errors' = 'true'
);
```

#### Consultas de Exemplo

```sql
-- Listar todos os registros
SELECT * FROM people_job;

-- Buscar por ID específico
SELECT * FROM people_job WHERE id = 1;

-- Filtrar por profissão
SELECT name, salary FROM people_job WHERE job = 'Software Engineer';

-- Calcular estatísticas
SELECT 
    job,
    COUNT(*) as total_pessoas,
    AVG(salary) as salario_medio,
    MAX(salary) as maior_salario
FROM people_job 
GROUP BY job;
```

### 4. Navegação no Terminal

#### Comandos Úteis no SQL Client:

```bash
# Listar todas as tabelas
SHOW TABLES;

# Descrever uma tabela
DESCRIBE people_job;

# Sair da visualização de resultados de uma query
# Pressione 'Q'

# Sair do SQL Client (várias opções)
EXIT;
# ou
QUIT;
# ou pressione Ctrl + C

# Sair do contêiner Docker
exit
```

#### Sequência para Sair Completamente:

```bash
# 1. Se estiver visualizando resultados, pressione:
Q

# 2. Sair do Flink SQL Client:
Flink SQL> EXIT;

# 3. Sair do container Docker:
bash-4.4$ exit

# Agora você está de volta ao seu terminal do Windows
```

#### Comandos Docker Úteis:

```bash
# Ver logs dos serviços
docker-compose logs

# Ver logs de um serviço específico
docker-compose logs jobmanager

# Parar todos os serviços
docker-compose down

# Reiniciar um serviço específico
docker-compose restart taskmanager

# Ver uso de recursos
docker stats
```

## 🌐 Interface Web

Acesse o Dashboard do Flink em: **http://localhost:8081**

### Funcionalidades do Dashboard:

- **Overview**: Status geral do cluster
- **Jobs**: Jobs em execução e histórico
- **Task Managers**: Status dos executores
- **Job Manager**: Informações do coordenador
- **Submit New Job**: Upload de jobs JAR

## 📁 Estrutura do Projeto

```
flink-sql-cli-docker/
├── docker-compose.yml          # Orquestração dos contêineres
├── README.md                   # Este arquivo
├── .gitignore                  # Arquivos ignorados pelo Git
├── texto explicativo.md        # Documentação original da disciplina
├── data/
│   └── test.csv               # Dados de exemplo para testes
├── settings/                  # Configurações compartilhadas
└── sql-client/
    ├── Dockerfile             # Imagem customizada do SQL Client
    ├── docker-entrypoint.sh   # Script de inicialização
    ├── bin/
    │   └── sql-client.sh      # Script do cliente SQL
    └── conf/
        └── flink-conf.yaml    # Configurações do Flink
```

## 🔧 Conectores Incluídos

O ambiente vem pré-configurado com conectores para:

- **Kafka**: Processamento de streams
- **JDBC**: Bancos de dados relacionais
- **Elasticsearch**: Busca e analytics
- **PostgreSQL**: Banco de dados específico
- **JSON/Avro**: Formatos de dados
- **Filesystem**: Arquivos locais (CSV, JSON, etc.)

## 💡 Casos de Uso Práticos

### 1. Processamento de Logs em Tempo Real
```sql
-- Exemplo conceitual para logs de web server
CREATE TABLE web_logs (
    timestamp_log TIMESTAMP,
    ip_address STRING,
    method STRING,
    url STRING,
    status_code INT,
    response_size BIGINT
) WITH (
    'connector' = 'kafka',
    'topic' = 'web-logs',
    'properties.bootstrap.servers' = 'localhost:9092',
    'format' = 'json'
);
```

### 2. ETL de Dados
```sql
-- Transformação e agregação de dados
CREATE TABLE sales_summary AS
SELECT 
    DATE_FORMAT(order_date, 'yyyy-MM') as month,
    product_category,
    SUM(amount) as total_sales,
    COUNT(*) as total_orders
FROM sales_raw
GROUP BY DATE_FORMAT(order_date, 'yyyy-MM'), product_category;
```

## 🐛 Troubleshooting

### Problemas Comuns:

1. **Porta 8081 já em uso**
   ```bash
   # Verificar processo usando a porta
   netstat -tulpn | grep 8081
   # Ou mudar a porta no docker-compose.yml
   ```

2. **Contêineres não iniciam**
   ```bash
   # Verificar logs
   docker-compose logs
   # Limpar volumes
   docker-compose down -v
   ```

3. **Sem memória suficiente**
   ```bash
   # Verificar uso de recursos
   docker stats
   # Aumentar memória do Docker Desktop
   ```

4. **SQL Client não conecta**
   ```bash
   # Verificar se o Job Manager está ativo
   docker-compose ps
   # Reiniciar o SQL Client
   docker-compose restart sql-client
   ```

## 📚 Próximos Passos

1. **Integração com Kafka**: Configure um broker Kafka para streams reais
2. **Conectores de Banco**: Conecte com PostgreSQL ou MySQL
3. **Jobs Complexos**: Desenvolva jobs Java/Scala para o Flink
4. **Monitoramento**: Configure métricas e alertas
5. **Produção**: Estude clusters Flink em produção

## 🎓 Material de Estudo

- [Documentação Oficial Apache Flink](https://flink.apache.org/docs/)
- [Flink SQL Cookbook](https://flink.apache.org/2020/07/28/flink-sql-cookbook.html)
- [Tutoriais de Streaming](https://flink.apache.org/tutorials/)

## 📝 Licença

Este projeto é baseado no Apache Flink e mantém a licença Apache 2.0.

---

**Desenvolvido para fins educacionais - PUC Minas**  
**Disciplina**: Camadas e Serviços de Consumo de Dados  
**Curso**: Arquitetura e Engenharia de Dados
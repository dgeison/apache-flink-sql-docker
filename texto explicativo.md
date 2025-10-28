# Trabalho Prático: Apache Flink - Instalação e Prática

**DIRETORIA DE EDUCAÇÃO CONTINUADA - IEC**

---

## 📋 Informações da Disciplina

| Campo | Informação |
|-------|------------|
| **Disciplina** | Camadas e Serviços de Consumo de Dados |
| **Curso** | Arquitetura e Engenharia de Dados |
| **Professor** | Ricardo Brito Alves |
| **Instituição** | PUC Minas |

### 👥 Equipe
- **Aluno**: [Dgeison Serrão Peixoto]

---

## 🎯 Apache Flink e SQL

### Objetivos do Trabalho

O **Apache Flink** é uma estrutura de código aberto para processamento de dados nos modos:
- **Stream** (fluxo de dados em tempo real)
- **Batch** (processamento em lote)

#### 🔧 Tecnologias Suportadas
- **Plataformas**: Apache Kafka, bancos JDBC
- **Linguagens**: Java, Scala, Python (PyFlink)
- **Integração ML**: Bibliotecas de Machine Learning via PyFlink

#### 💡 Por que SQL?
O **SQL** é uma das linguagens mais universais no mundo dos dados:
- **Analistas de dados** → Consultas e relatórios
- **Cientistas de dados** → Análise exploratória  
- **Engenheiros de dados** → Pipelines de ETL
- **Abstração universal** → Independente da tecnologia subjacente

### 🚀 Proposta do Projeto
Com o Apache Flink, você pode definir **pipelines de dados inteiros** em SQL puro usando o **SQL Client**. Este trabalho configura uma plataforma local baseada em Docker para Apache Flink, incluindo um cliente SQL interativo.

> **Fonte**: Trabalho adaptado de [Apache Flink on Docker](https://dev.to/ftisiot/apache-flink-on-docker-4kij)

---

## 🛠️ Atividades Práticas

### 1. Configuração do Apache Flink com Docker Compose

O Apache Flink é uma tecnologia poderosa que merece ser explorada. Para minimizar o esforço de configuração, utilizamos **Docker** como solução containerizada portável.

#### 📋 Pré-requisitos
Certifique-se de que estão instalados:
- ✅ Docker Desktop
- ✅ Docker Compose

#### 🚀 Iniciando o Ambiente

```bash
# Navegue até o diretório do projeto
cd flink-sql-cli-docker

# Inicie os serviços em background
docker-compose up -d
```

Este comando iniciará **3 componentes** do Apache Flink:
- **Job Manager** → Coordenador do cluster
- **Task Manager** → Executor de tarefas
- **SQL Client** → Interface SQL interativa

#### ✅ Verificação do Status

```bash
# Verifique se os contêineres estão rodando
docker-compose ps
```

**Resultado esperado:**
```
    Name                 Command            State                    Ports                  
------------------------------------------------------------------------------------------
jobmanager      /docker-entrypoint.sh   Up      6123/tcp, 0.0.0.0:8081->8081/tcp        
sql-client      /docker-entrypoint.sh   Up                                               
taskmanager     /docker-entrypoint.sh   Up      6121-6125/tcp                            
```

#### 🌐 Interface Web
A **interface web do Flink** está disponível em: **http://localhost:8081**

Esta ferramenta permite:
- Monitorar status do cluster
- Visualizar jobs em execução
- Acompanhar métricas de performance
- Gerenciar pipelines de dados

---

### 2. Configurações do Docker Compose

#### 📁 Mapeamento de Volumes

No `docker-compose.yml`, são mapeadas duas pastas importantes:

**`/settings`**
- Compartilha configurações entre host e containers
- Útil para arquivos de autenticação (keystores, certificados)
- Permite customização sem rebuild da imagem

**`/data`**  
- Disponibiliza dados para processamento
- Contém o arquivo `test.csv` para demonstrações
- Facilita testes com arquivos do sistema local

---

### 3. Utilizando o SQL Client

#### 🔑 Acessando o Cliente SQL

```bash
# Entre no container SQL Client
docker exec -it sql-client /bin/bash

# Inicie o cliente SQL do Flink
./sql-client.sh
```

Agora você terá um **cliente SQL totalmente funcional** para criar pipelines de dados com várias fontes e destinos.

#### 📊 Exemplo Prático: Criando uma Tabela

Execute o comando para criar uma tabela baseada no arquivo CSV:

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

#### 🔍 Consultando os Dados

```sql
-- Listar todos os registros
SELECT * FROM people_job;
```

**Resultado esperado:**
```
+------+----------+------------------------+--------+
|   id |     name |                    job | salary |
+------+----------+------------------------+--------+
|    1 |      Ugo |        Football Player | 200000 |
|    2 |    Carlo | Crocodile domesticator |  30000 |
|    3 |    Maria |      Software Engineer | 210000 |
|    4 |   Sandro |            UX Designer |  70000 |
|    5 |  Melissa |      Software Engineer |  95000 |
+------+----------+------------------------+--------+
```

#### 📈 Consultas Avançadas

```sql
-- Buscar por ID específico
SELECT * FROM people_job WHERE id = 1;

-- Filtrar por profissão
SELECT job FROM people_job WHERE id = 2;

-- Análise estatística por profissão
SELECT 
    job,
    COUNT(*) as total_pessoas,
    AVG(salary) as salario_medio,
    MAX(salary) as maior_salario
FROM people_job 
GROUP BY job;
```

#### 🚪 Saindo do Cliente SQL

Para sair da visualização de resultados:
```
Pressione: Q
```

Para sair do SQL Client:
```sql
EXIT;
```

Para sair do container:
```bash
exit
```

---

## 📋 Entrega do Trabalho

### 🎯 Requisitos de Entrega

1. **Screenshot do Dashboard**
   - Acesse: http://localhost:8081/
   - Capture a interface web do Apache Flink Dashboard
   - Certifique-se de que mostra o cluster ativo

2. **Evidências de Execução**
   - Comandos executados no SQL Client
   - Resultados das consultas SQL
   - Status dos containers Docker

3. **Documentação**
   - Relatório das atividades realizadas
   - Análise dos resultados obtidos
   - Reflexões sobre o uso do Apache Flink

### ✅ Critérios de Avaliação

- **Configuração correta** do ambiente Docker
- **Execução bem-sucedida** dos comandos SQL
- **Captura adequada** do dashboard web
- **Documentação clara** do processo
- **Compreensão demonstrada** dos conceitos

---

## 🎓 Aprendizados Esperados

Ao concluir este trabalho, você terá:

- ✅ **Configurado um cluster Apache Flink** completo
- ✅ **Executado consultas SQL** em dados distribuídos  
- ✅ **Compreendido arquiteturas** de processamento de streams
- ✅ **Trabalhado com containerização** via Docker
- ✅ **Explorado ferramentas** de Big Data modernas

### 🚀 Próximos Passos

- Integração com Apache Kafka para streams reais
- Conectores para bancos de dados relacionais
- Desenvolvimento de jobs Java/Scala personalizados
- Implementação de pipelines de Machine Learning
- Deploy em clusters de produção

---

**📚 Material Complementar**: [Documentação Oficial Apache Flink](https://flink.apache.org/docs/)

 


DIRETORIA DE EDUCAÇÃO CONTINUADA - IEC




Disciplina: CAMADAS E SERVIÇOS DE CONSUMO DE DADOS

Nome do curso: ARQUITETURA E ENGENHARIA DE DADOS
Professor: Ricardo Brito Alves


Trabalho Prático – Apache Flink – Instalação e Prática
Aluno:
-
-

 


APACHE FLINK E SQL


Objetivos 
O Apache Flink é uma estrutura de código aberto para processamento de dados no modo de stream (fluxo) e batch (lote). Ele suporta uma variedade de plataformas de dados diferentes, incluindo Apache Kafka e qualquer banco de dados JDBC. Os poderes do Flink vêm em uma variedade de linguagens: desde o Java e Scala mais tradicionais, até o Python. E para Python há pyFlink que você pode usar com as melhores bibliotecas de aprendizado de máquina disponíveis.
No entanto, no mundo dos dados, uma das linguagens mais conhecidas é o SQL. De analistas de dados a cientistas e engenheiros, o SQL geralmente faz parte do kit de ferramentas de qualquer profissional de dados. O SQL fornece uma abstração para acesso e manipulação de dados que vai além das tecnologias e tendências. Você pode usá-lo para consultar seus dados, independentemente da tecnologia subjacente (com pequenas diferenças de dialeto).
Com o Apache Flink, você pode definir pipelines de dados inteiros em SQL puro usando seu SQL Client. Este trabalho ajudará você a configurar uma plataforma local baseada em docker para o Apache Flink, incluindo um cliente SQL.

Fonte: trabalho adaptado de https://dev.to/ftisiot/apache-flink-on-docker-4kij

Atividades
Configurar o Apache Flink localmente com o Docker Compose
O Apache Flink é uma tecnologia muito interessante e vale a pena experimentar. 
Para minimizar o trabalho de configuração, vamos usar o Docker. Ele oferece ferramentas como soluções pré-empacotadas portáteis em qualquer sistema operacional convidado com o mínimo de esforço.
Copie a pasta do drive:
https://drive.google.com/drive/folders/1G9c87P0UhfJiY2pyeABZbDtUqgC_6kP8?
Antes de começar, certifique-se de que o docker e o docker-compose estejam instalados.

Agora vamos abrir a pasta flink-sql-cli-docker e iniciar a composição do docker:
cd ...\flink-sql-cli-docker

Rodar o arquivo docker-compose.yml
docker-compose up -d
 

Isso iniciará 3 nós do Apache Flink em segundo plano: um gerenciador de tarefas, um gerenciador de tarefas e o cliente sql. Podemos revisar os detalhes do cluster assim:
docker-compose ps
Isso deve mostrar que os três contêineres estão no estado Up:
 

A interface do usuário da Web do Flink agora está disponível no endereço localhost:8081. Esta é uma ferramenta útil para navegar por informações sobre o status do Flink e os pipelines de dados que vamos criar.
 



Observações sobre como configurar o Docker-Compose
Em docker-compose.yml mapeamos a subpasta settings para a pasta jobmanager docker container /settings. Dessa forma, os arquivos de configurações podem ser passados entre o host e o convidado, o que é útil se arquivos específicos gerados pelo host (por exemplo, armazenamentos de chaves) forem necessários para autenticação.
A subpasta de dados também é mapeada para os contêineres taskmanager e jobmanager. Isso ocorre puramente porque eu precisava fornecer o exemplo SQL abaixo, mas pode ser útil nos casos em que queremos testar o comportamento do Apache Flink em arquivos em um sistema de arquivos local. A subpasta de dados contém um arquivo test.csv com dados inventados que usaremos para testar o Apache Flink.

Liberando o SQL no Flink
Se quisermos brincar com o SQL do Flink, precisamos entrar no contêiner sql-client. Podemos fazer isso executando o seguinte comando no terminal:
docker exec -it sql-client /bin/bash

 

Já estamos dentro do container, e podemos iniciar o cliente SQL do Flink com:
./sql-client.sh

 

Agora temos um cliente SQL totalmente funcional que podemos usar para criar pipelines de dados anexados a uma variedade de fontes de dados e destinos. Como um pequeno exemplo de demonstração, podemos consultar o arquivo test.csv dentro da pasta flink-sql-cli-docker/data definindo a tabela Flink associada no SQL Client. Execute o comando abaixo:
create table
  people_job (
    id INT,
    name STRING,
    job STRING,
    salary BIGINT
  )
  WITH (
    'connector' = 'filesystem',
    'path' = 'file:///data/test.csv',
    'format' = 'csv',
    'csv.ignore-parse-errors' = 'true'
    );

 

Podemos consultar a tabela:
select * from people_job;
Com os seguintes resultados:
+/-                        id                      name                       job                    salary
  +                         1                       Ugo           Football Player              200000
  +                         2                     Carlo    Crocodile domesticator        30000
  +                         3                     Maria         Software Engineer          210000
  +                         4                    Sandro               UX Designer           70000
  +                         5                   Melissa         Software Engineer        95000

Consultar a tabela com outros comandos SQL, como:
select * from people_job where id = 1;
select job from people_job where id = 2;

Saia da visualização de tabela do Flink pressionando Q.
 

Entrega:
Ao final da prática, entre no browser (http://localhost:8081/) e tire print da interface web do Apache Flink Dashboard. Entregue este print.

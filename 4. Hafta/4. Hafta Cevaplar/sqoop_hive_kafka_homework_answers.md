# SORULAR
# Soru-1 
Aşağıdaki işleri yapan bir script yazınız.
- `~/datasets/retail_db` klasörü içindeki csv dosyalarını postgresql retail veritabanına kopyalasın.

- Postgresql retail şemasındaki tabloları hive retail veri tabanına aktarsın. Aktarılan tablolar orc formatında snappy sıkıştırmasına sahip olsun.

# Soru-2
- Docker-compose kullanarak 3 node bir kafka cluster oluşturunuz.
- Kafka cluster üzerinde topic1 adında bir topic oluşturunuz. Bu topic 5 parçaya sahip olsun ve bu parçalar 3 kopya halinde tutulsun.
- data-generator ile [ChurnModeling.csv] https://raw.githubusercontent.com/erkansirin78/datasets/master/Churn_Modelling.csv veri setini topic1'e gönderiniz. Bu esnada 3 adet console-consumer ile topic1'i aynı consumer group altında consume ediniz ve sonuçları gözlemleyiniz.

# CEVAPLAR

# Cevap-1
Soru:

Aşağıdaki işleri yapan bir script yazınız.
- `~/datasets/retail_db` klasörü içindeki csv dosyalarını postgresql retail veritabanına kopyalasın.

- Postgresql retail şemasındaki tabloları hive retail veri tabanına aktarsın. Aktarılan tablolar orc formatında snappy sıkıştırmasına sahip olsun.

Cevap:

- Eğer datasets içinde retail_db yoksa indiriniz.
```
[train@localhost play]$ wget https://raw.githubusercontent.com/erkansirin78/datasets/master/retail_db/categories.csv
[train@localhost play]$ wget https://raw.githubusercontent.com/erkansirin78/datasets/master/retail_db/customers.csv
[train@localhost play]$ wget https://raw.githubusercontent.com/erkansirin78/datasets/master/retail_db/departments.csv
[train@localhost play]$ wget https://raw.githubusercontent.com/erkansirin78/datasets/master/retail_db/order_items.csv
[train@localhost play]$ wget https://raw.githubusercontent.com/erkansirin78/datasets/master/retail_db/orders.csv
[train@localhost play]$ wget https://raw.githubusercontent.com/erkansirin78/datasets/master/retail_db/products.csv
```
- postgres kullanıcısına şifre belirle

`/var/lib/pgsql/10/data/pg_hba.conf` dosyası içinde `host all postgres 127.0.0.1/32 trust` satırını bu hale getir.

postgresql servisini restart et.
psql shell'e bağlan.  
`ALTER USER postgres PASSWORD 'Ankara06';` ile şifre belirle
Tekrar pg_hba.conf dosyasında `host all postgres 127.0.0.1/32 md5` haline getir.
postgresql servisini restart et.
sudo -u postgres psql komutu ile psql shell'e bağlanmaya çalıştırğında artık parola soracaktır.

- Create database, grand priviledges
```
[train@localhost ~]$ psql -h localhost -U postgres -c "CREATE DATABASE retail OWNER train ENCODING 'UTF8';" 
```
- Password dosyasına parola ekle file

`[train@localhost play]$ echo "localhost:5432:retail:train:Ankara06" >> ~/.pgpass`  

- create a file that consists table names. We will loop through in this file for sqoop jobs
```
[train@localhost play]$ cat table_names.txt
categories
customers
departments
order_items
orders
products
```
- create a script
```
#!/bin/bash
echo "Creating categories table on postgresql retail database"
psql -h localhost -d retail -U train -c "create table if not exists categories(categoryId int, categoryDepartmentId int, categoryName VARCHAR(50));"
echo "truncate categories"
psql -h localhost -d retail -U train -c "\TRUNCATE TABLE categories;"
echo "Copying categories data"
psql -h localhost -d retail -U train -c "\copy categories FROM '/home/train/datasets/retail_db/categories.csv' DELIMITERS ',' CSV HEADER;"
echo "Creating customers table on postgresql retail database"
psql -h localhost -d retail -U train -c "create table if not exists customers(customerId int, customerFName varchar(50), customerLName varchar(50), customerEmail varchar(50), customerPassword varchar(20), customerStreet varchar(50), customerCity varchar(50), customerState varchar(10), customerZipcode int);"
echo "truncate customers"
psql -h localhost -d retail -U train -c "\TRUNCATE TABLE customers;"
echo "Copying customers data"
psql -h localhost -d retail -U train -c "\copy customers FROM '/home/train/datasets/retail_db/customers.csv' DELIMITERS ',' CSV HEADER;"
echo "Creating departments table on postgresql retail database"
psql -h localhost -d retail -U train -c "create table if not exists departments(customerIddepartmentId int, departmentName varchar(20));"
echo "truncate departments"
psql -h localhost -d retail -U train -c "\TRUNCATE TABLE departments;"
echo "Copying departments data"
psql -h localhost -d retail -U train -c "\copy departments FROM '/home/train/datasets/retail_db/departments.csv' DELIMITERS ',' CSV HEADER;"
echo "Creating order_items table on postgresql retail database"
psql -h localhost -d retail -U train -c "create table if not exists order_items(orderItemName int,orderItemOrderId int,orderItemProductId int,orderItemQuantity int,orderItemSubTotal float8,orderItemProductPrice float8);"
echo "truncate order_items"
psql -h localhost -d retail -U train -c "\TRUNCATE TABLE order_items;"
echo "Copying order_items data"
psql -h localhost -d retail -U train -c "\copy order_items FROM '/home/train/datasets/retail_db/order_items.csv' DELIMITERS ',' CSV HEADER;"
echo "Creating orders table on postgresql retail database"
psql -h localhost -d retail -U train -c "create table if not exists orders(orderId int, orderDate timestamp,orderCustomerId int, orderStatus varchar(20));"
echo "truncate orders"
psql -h localhost -d retail -U train -c "\TRUNCATE TABLE orders;"
echo "Copying orders data"
psql -h localhost -d retail -U train -c "\copy orders FROM '/home/train/datasets/retail_db/orders.csv' DELIMITERS ',' CSV HEADER;"
echo "Creating products table on postgresql retail database"
psql -h localhost -d retail -U train -c "create table if not exists products(productId int, productCategoryId int, productName varchar(50), productDescription varchar(50), productPrice float8, productImage varchar(255));"
echo "truncate products"
psql -h localhost -d retail -U train -c "\TRUNCATE TABLE products;"
echo "Copying products data"
psql -h localhost -d retail -U train -c "\copy products FROM '/home/train/datasets/retail_db/products.csv' DELIMITERS ',' CSV HEADER;"
echo "Sqop jobs starting..."
echo "creating hive retail database"
beeline -u jdbc:hive2://localhost:10000 -e "CREATE database if not exists retail;"
for table_name in $(cat $(pwd)/table_names.txt); do
	echo "**********************************************************************************************************************************"
	echo "**************************************  looping for $table_name    ***************************************************************"
	echo "**********************************************************************************************************************************"
	echo "checking if $table_name exists in retail db"
	beeline -u jdbc:hive2://localhost:10000/retail -e "show tables"  | grep $table_name
	if [ $? -eq 0 ]
	then
	  echo "$table_name table found"
	sqoop import --connect jdbc:postgresql://localhost/retail  \
	--driver org.postgresql.Driver \
	--username train --password-file file:///home/train/.sqoop_password \
	--table $table_name \
	--m 1 --hive-import  --hive-overwrite --hive-table retail.$table_name \
	--target-dir /tmp/$table_name  
	else
	  echo "$table_name table not found"
	sqoop import --connect jdbc:postgresql://localhost/retail  \
	--driver org.postgresql.Driver \
	--username train --password-file file:///home/train/.sqoop_password \
	--table $table_name \
	--m 1 --hive-import  --create-hive-table --hive-table retail.$table_name \
	--target-dir /tmp/$table_name
	fi
	echo "renaming table and changing file format"
	beeline -u jdbc:hive2://localhost:10000/retail -e "create table if not exists retail.${table_name}_orc_snappy stored as orc TBLPROPERTIES ('orc.compress'='SNAPPY') as select * from retail.$table_name; drop table $table_name; alter table retail.${table_name}_orc_snappy rename to $table_name;"
done
```
- run script

` [train@localhost play]$ ./week4_answers.sh `


# Cevap-2
Soru:

- Docker-compose kullanarak 3 node bir kafka cluster oluşturunuz.
- Kafka cluster üzerinde topic1 adında bir topic oluşturunuz. Bu topic 5 parçaya sahip olsun ve bu parçalar 3 kopya halinde tutulsun.
- data-generator ile [ChurnModeling.csv] https://raw.githubusercontent.com/erkansirin78/datasets/master/Churn_Modelling.csv veri setini topic1'e gönderiniz. Bu esnada 3 adet console-consumer ile topic1'i aynı consumer group altında consume ediniz ve sonuçları gözlemleyiniz.


Cevap

- docker-compose.yaml

```
version: '2.1'

services:
  zoo1:
    image: zookeeper:3.4.9
    hostname: zoo1
    ports:
      - "2181:2181"
    environment:
        ZOO_MY_ID: 1
        ZOO_PORT: 2181
        ZOO_SERVERS: server.1=zoo1:2888:3888 server.2=zoo2:2888:3888 server.3=zoo3:2888:3888
    volumes:
      - ./zk-multiple-kafka-multiple/zoo1/data:/data
      - ./zk-multiple-kafka-multiple/zoo1/datalog:/datalog

  zoo2:
    image: zookeeper:3.4.9
    hostname: zoo2
    ports:
      - "2182:2182"
    environment:
        ZOO_MY_ID: 2
        ZOO_PORT: 2182
        ZOO_SERVERS: server.1=zoo1:2888:3888 server.2=zoo2:2888:3888 server.3=zoo3:2888:3888
    volumes:
      - ./zk-multiple-kafka-multiple/zoo2/data:/data
      - ./zk-multiple-kafka-multiple/zoo2/datalog:/datalog

  zoo3:
    image: zookeeper:3.4.9
    hostname: zoo3
    ports:
      - "2183:2183"
    environment:
        ZOO_MY_ID: 3
        ZOO_PORT: 2183
        ZOO_SERVERS: server.1=zoo1:2888:3888 server.2=zoo2:2888:3888 server.3=zoo3:2888:3888
    volumes:
      - ./zk-multiple-kafka-multiple/zoo3/data:/data
      - ./zk-multiple-kafka-multiple/zoo3/datalog:/datalog


  kafka1:
    image: confluentinc/cp-kafka:5.5.1
    hostname: kafka1
    ports:
      - "9092:9092"
    environment:
      KAFKA_ADVERTISED_LISTENERS: LISTENER_DOCKER_INTERNAL://kafka1:19092,LISTENER_DOCKER_EXTERNAL://${DOCKER_HOST_IP:-127.0.0.1}:9092
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: LISTENER_DOCKER_INTERNAL:PLAINTEXT,LISTENER_DOCKER_EXTERNAL:PLAINTEXT
      KAFKA_INTER_BROKER_LISTENER_NAME: LISTENER_DOCKER_INTERNAL
      KAFKA_ZOOKEEPER_CONNECT: "zoo1:2181,zoo2:2182,zoo3:2183"
      KAFKA_BROKER_ID: 1
      KAFKA_LOG4J_LOGGERS: "kafka.controller=INFO,kafka.producer.async.DefaultEventHandler=INFO,state.change.logger=INFO"
    volumes:
      - ./zk-multiple-kafka-multiple/kafka1/data:/var/lib/kafka/data
    depends_on:
      - zoo1
      - zoo2
      - zoo3

  kafka2:
    image: confluentinc/cp-kafka:5.5.1
    hostname: kafka2
    ports:
      - "9093:9093"
    environment:
      KAFKA_ADVERTISED_LISTENERS: LISTENER_DOCKER_INTERNAL://kafka2:19093,LISTENER_DOCKER_EXTERNAL://${DOCKER_HOST_IP:-127.0.0.1}:9093
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: LISTENER_DOCKER_INTERNAL:PLAINTEXT,LISTENER_DOCKER_EXTERNAL:PLAINTEXT
      KAFKA_INTER_BROKER_LISTENER_NAME: LISTENER_DOCKER_INTERNAL
      KAFKA_ZOOKEEPER_CONNECT: "zoo1:2181,zoo2:2182,zoo3:2183"
      KAFKA_BROKER_ID: 2
      KAFKA_LOG4J_LOGGERS: "kafka.controller=INFO,kafka.producer.async.DefaultEventHandler=INFO,state.change.logger=INFO"
    volumes:
      - ./zk-multiple-kafka-multiple/kafka2/data:/var/lib/kafka/data
    depends_on:
      - zoo1
      - zoo2
      - zoo3

  kafka3:
    image: confluentinc/cp-kafka:5.5.1
    hostname: kafka3
    ports:
      - "9094:9094"
    environment:
      KAFKA_ADVERTISED_LISTENERS: LISTENER_DOCKER_INTERNAL://kafka3:19094,LISTENER_DOCKER_EXTERNAL://${DOCKER_HOST_IP:-127.0.0.1}:9094
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: LISTENER_DOCKER_INTERNAL:PLAINTEXT,LISTENER_DOCKER_EXTERNAL:PLAINTEXT
      KAFKA_INTER_BROKER_LISTENER_NAME: LISTENER_DOCKER_INTERNAL
      KAFKA_ZOOKEEPER_CONNECT: "zoo1:2181,zoo2:2182,zoo3:2183"
      KAFKA_BROKER_ID: 3
      KAFKA_LOG4J_LOGGERS: "kafka.controller=INFO,kafka.producer.async.DefaultEventHandler=INFO,state.change.logger=INFO"
    volumes:
      - ./zk-multiple-kafka-multiple/kafka3/data:/var/lib/kafka/data
    depends_on:
      - zoo1
      - zoo2
      - zoo3
```

- Run docker compose
```
[train@localhost play]$ docker-compose up -d
Starting play_zoo2_1 ... done
Starting play_zoo1_1 ... done
Starting play_zoo3_1 ... done
Creating play_kafka1_1 ... done
Creating play_kafka3_1 ... done
Creating play_kafka2_1 ... done
```

- Open 3 terminal and run 
```
kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic topic1 --property print.key=true --property key.separator=, --group group1
```

- Open a terminal for data-generator 
```
[train@localhost play]$ cd ~/data-generator/
[train@localhost data-generator]$ source ~/venvspark/bin/activate

(venvspark) [train@localhost data-generator]$ python dataframe_to_kafka.py -i ~/datasets/Churn_Modelling.csv -t topic1

0 - 1,15634602,Hargrave,619,France,Female,42,2,0.00,1,1,1,101348.88,1
1/10000 processed, % 99.99 will be completed in 83.33 mins.
1 - 2,15647311,Hill,608,Spain,Female,41,1,83807.86,1,0,1,112542.58,0
2/10000 processed, % 99.98 will be completed in 83.32 mins.
2 - 3,15619304,Onio,502,France,Female,42,8,159660.80,3,1,0,113931.57,1
3/10000 processed, % 99.97 will be completed in 83.31 mins.
3 - 4,15701354,Boni,699,France,Female,39,1,0.00,2,0,0,93826.63,0
4/10000 processed, % 99.96 will be completed in 83.30 mins.
4 - 5,15737888,Mitchell,850,Spain,Female,43,2,125510.82,1,1,1,79084.10,0
5/10000 processed, % 99.95 will be completed in 83.29 mins.
5 - 6,15574012,Chu,645,Spain,Male,44,8,113755.78,2,1,0,149756.71,1
6/10000 processed, % 99.94 will be completed in 83.28 mins.
6 - 7,15592531,Bartlett,822,France,Male,50,7,0.00,2,1,1,10062.80,0
7/10000 processed, % 99.93 will be completed in 83.28 mins.
7 - 8,15656148,Obinna,376,Germany,Female,29,4,115046.74,4,1,0,119346.88,1
```

- Shutdown the cluster

```
[train@localhost play]$ docker-compose down
Stopping play_kafka3_1 ... done
Stopping play_kafka1_1 ... done
Stopping play_kafka2_1 ... done
Stopping play_zoo1_1   ... done
Stopping play_zoo2_1   ... done
Stopping play_zoo3_1   ... done
Removing play_kafka3_1 ... done
Removing play_kafka1_1 ... done
Removing play_kafka2_1 ... done
Removing play_zoo1_1   ... done
Removing play_zoo2_1   ... done
Removing play_zoo3_1   ... done
Removing network play_default

```
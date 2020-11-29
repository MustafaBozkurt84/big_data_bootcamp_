SPARK2 KURULUMU 
----------------
Java 8 yüklü olduğunu varsayarak...

1. Spark binary indir

```
(venvspark) [train@localhost pyspark]$ wget https://archive.apache.org/dist/spark/spark-2.4.7/spark-2.4.7-bin-hadoop2.7.tgz -P /opt/manual
```
2. Arşivi aç
```
(venvspark) [train@localhost manual]$ tar xzf spark-2.4.7-bin-hadoop2.7.tgz 
```

3. spark-2.4.7-bin-hadoop2.7 klasörüne spark2 isminde bir soft link ver 

```
(venvspark) [train@localhost manual]$ ln -s spark-2.4.7-bin-hadoop2.7 spark2
```

4. Template olan spark-env.sh ismini değiştir ve içine aşağıdaki ortam değişkenlerini ekle.
```
(venvspark) [train@localhost manual]$ mv /opt/manual/spark2/conf/spark-env.sh.template /opt/manual/spark2/conf/spark-env.sh
(venvspark) [train@localhost manual]$ nano /opt/manual/spark2/conf/spark-env.sh

HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop/
YARN_CONF_DIR=$HADOOP_HOME/etc/hadoop/
JAVA_HOME=$JAVA_HOME
PYSPARK_PYTHON=python3
PYSPARK_DRIVER_PYTHON=python3
```

5. Hive configürasyonlarını kopyala.

```
(venvspark) [train@localhost manual]$ cp /opt/manual/hive/conf/hive-site.xml /opt/manual/spark2/conf/
```
6. Postgresql driver'ını spark jarları içine kopyala 
```
(venvspark) [train@localhost manual]$ cp /opt/manual/hive/lib/postgresql-42.2.14.jar /opt/manual/spark2/jars/
```
7. spark-default.conf template adını değiştir ve içine aşağıdaki konfigürasyonları ekle.
```
(venvspark) [train@localhost manual]$ mv /opt/manual/spark2/conf/spark-defaults.conf.template /opt/manual/spark2/conf/spark-defaults.conf
(venvspark) [train@localhost manual]$ nano /opt/manual/spark2/conf/spark-defaults.conf

spark.driver.memory                         512m
spark.shuffle.file.buffer                   1m
spark.file.transferTo                       false
spark.shuffle.unsafe.file.output.buffer     1m
spark.io.compression.lz4.blockSize          512k
spark.shuffle.service.index.cache.size      200m
spark.shuffle.registration.timeout          12000ms
spark.shuffle.registration.maxAttempts      5
spark.sql.warehouse.dir                     /user/hive/warehouse
```

8. Jupyter'i aç

`findspark.init("/opt/manual/spark2")`  
ile spark'kı başlat. SparkSession oluştuktan sonra versiyonu kontrol et.
`spark.version`  

Çıktı: `'2.4.7'` olmalıdır.

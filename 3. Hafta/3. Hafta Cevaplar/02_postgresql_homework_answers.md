
# CEVAPLAR
1. 
### Soru
Her 2 dakikada bir çalışacak bir script yazınız. Script aşağıdaki işleri yapmalıdır.
- Postgresql traindb veri tabanında users adında bir tablo olup olmadığını kontrol etsin. 
- users tablosu yok ise oluştursun.
- users tablosu şeması id, name, age şeklindedir.
- Her 2 dakikada bir bu tabloya bir kayıt girsin.
- Girilen kayıtta id yerine o anki saat-dakika-saniyeden oluşan sayı örneğin 143221, name yerine sanal makinenin adını, age yerine kayıt anındaki dakikayı girsin.
- Script stdin, stderr sonuçlarını <scriptadı_logs.log> adında bir dosyaya loglasın.
- 15 dakika boyunca bu görevi açık tutunuz daha sonra kapatınız.
- 15 dakika sonunda tablo içeriğini select komutu ile gösteriniz.

10-15 dk sonunda örnek tablo sorgu sonucu aşağıdaki gibi olmalıdır:
```
   id   |         name          | age
--------+-----------------------+-----
 145402 | localhost.localdomain |  54
 145601 | localhost.localdomain |  56
 145801 | localhost.localdomain |  58
 150002 | localhost.localdomain |   0
 150201 | localhost.localdomain |   2
 150401 | localhost.localdomain |   4
```
### Cevap
- Password promt'u durdur.
create a file that saves the password In the file format should be: `hostname:port:database:username:password`

`[train@localhost git]$ echo "localhost:5432:traindb:train:Ankara06" > ~/.pgpass `

change permissions of pgpass file `[train@localhost git]$ chmod 600 ~/.pgpass `

- Script
```
[train@localhost play]$ cat add_records.sh
#!/bin/bash
psql -h localhost -U train -d traindb -c "create table if not exists users(id SERIAL PRIMARY KEY, name varchar(50), age int);"
psql -h localhost -U train -d traindb -c "select * from users;"
age=$(date +%M)
id=$(date +%H%M%S)
HOSTNAME=$(hostname)
psql -h localhost -U train -d traindb -c "insert into users values($id, '$HOSTNAME', $age);"
```
- Crontab görevi
`*/2 * * * * /home/train/advanced_ds_bigdata/crontab/play/add_records.sh >> /home/train/advanced_ds_bigdata/crontab/play/add_records_logs.log 2>&1`

- Tablo sorgusu
```
[train@localhost git]$ psql -h localhost -U train -d traindb -c "select * from  users;"
   id   |         name          | age
--------+-----------------------+-----
 145402 | localhost.localdomain |  54
 145601 | localhost.localdomain |  56
 145801 | localhost.localdomain |  58
 150002 | localhost.localdomain |   0
 150201 | localhost.localdomain |   2
 150401 | localhost.localdomain |   4
(6 rows)
```

2. 
## Soru
https://github.com/erkansirin78/datasets/tree/master/retail_db
adresindeki csv dosyalarını retail adında bir veri tabanında oluşturacağınız tablolara kopyalayınız.
Tablo isimleri csv dosya isimleri ile aynı olmalıdır.

## Cevap
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
`/var/lib/pgsql/10/data/pg_hba.conf` dosyası içinde 
`host    all             postgres         127.0.0.1/32           trust`  satırını bu hale getir.  
postgresql servisini restart et.  
`sudo -u postgres psql` komutu ile psql shell'e bağlan.  
`ALTER USER postgres PASSWORD 'Ankara06';`  ile şifre belirle  
Tekrar pg_hba.conf dosyasında 
`host    all             postgres         127.0.0.1/32           md5`  haline getir.   
postgresql servisini restart et.  
`sudo -u postgres psql` komutu ile psql shell'e bağlanmaya çalıştırğında artık parola soracaktır.  

- Create database, grand priviledges
```
# create database and grant privileges to train user 
[train@localhost ~]$ psql -h localhost -U postgres -c "CREATE DATABASE retail OWNER train ENCODING 'UTF8';" 
```

- Create table 
```
[train@localhost ~]$ psql -h localhost -d retail -U train -c "create table if not exists categories(categoryId int, categoryDepartmentId int, categoryName VARCHAR(50));"
```
- copy  
```
[train@localhost ~]$ psql -h localhost -d retail -U train -c "\copy categories FROM '/home/train/datasets/retail_db/categories.csv' DELIMITERS ',' CSV HEADER;"
```
- Test  
```
[train@localhost ~]$ psql -h localhost -d retail -U train -c "select * from categories limit 10;"            Password for user postgres:
 categoryid | categorydepartmentid |    categoryname
------------+----------------------+---------------------
          1 |                    2 | Football
          2 |                    2 | Soccer
          3 |                    2 | Baseball & Softball
          4 |                    2 | Basketball
          5 |                    2 | Lacrosse
          6 |                    2 | Tennis & Racquet
          7 |                    2 | Hockey
          8 |                    2 | More Sports
          9 |                    3 | Cardio Equipment
         10 |                    3 | Strength Training
(10 rows)
```

- Diğer csv dosyası da yukarıdaki örneğe uygun olarak kopyalanır.  
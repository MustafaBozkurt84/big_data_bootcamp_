# SORULAR
1. Her 2 dakikada bir çalışacak bir script yazınız. Script aşağıdaki işleri yapmalıdır.
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





# CEVAPLAR
1. 
## Soru
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
## Cevap
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

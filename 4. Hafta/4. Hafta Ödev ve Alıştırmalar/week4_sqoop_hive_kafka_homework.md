# SORULAR
# Soru-1 
Aşağıdaki işleri yapan bir script yazınız.
- `~/datasets/retail_db` klasörü içindeki csv dosyalarını postgresql retail veritabanına kopyalasın.

- Postgresql retail şemasındaki tabloları hive retail veri tabanına aktarsın. Aktarılan tablolar orc formatında snappy sıkıştırmasına sahip olsun.

# Soru-2
- Docker-compose kullanarak 3 node bir kafka cluster oluşturunuz.
- Kafka cluster üzerinde topic1 adında bir topic yaratınız. Bu topic 5 parçaya sahip olsun ve bu parçalar 3 kopya halinde tutulsun.
- data-generator ile [ChurnModeling.csv] https://raw.githubusercontent.com/erkansirin78/datasets/master/Churn_Modelling.csv veri setini topic1'e gönderiniz. 
Bu esnada 3 adet console-consumer ile topic1'i aynı consumer group altında consume ediniz ve sonuçları gözlemleyiniz.
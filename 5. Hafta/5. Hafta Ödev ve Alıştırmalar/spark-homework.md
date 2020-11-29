1. Spark 2.4.7 kurunuz ve  Spark2 ile jupyter notebook üzerinde iris.csv dosyasını okuyan ve çiçek türlerine göre ortalama yaprak ölçülerini ve her türden kaç tane kayıt bulunduğunu gösteren bir spark uygulaması yazınız.

2. Gitea kurulumunu gerçekleştiriniz.

3. Spark local modda dataframe api ile retail_db veri setini kullanarak aşağıdaki iş ihtiyaçlarını çözünüz.

### 3.1. `order_items` tablosunda kaç tane tekil `orderItemOrderId` vardır sayısını bulunuz.

### 3.2. `orders` ve `order_items` tablolarında kaç satır vardır bulunuz.

### 3.3. Toplam satış tutarı bakımından en çok iptal edilen (azalan sıra) ürünleri lokal diske parquet formatında yazınız.
```     
Örnek Sonuç:

|:-:|:---------------------------------------------:|:----------:|
| 0 | Field & Stream Sportsman 16 Gun Fire Safe     | 134393.28  |
| 1 | Perfect Fitness Perfect Rip Deck              | 85785.70   |
| 2 | Nike Men's Free 5.0+ Running Shoe             | 80691.93   |
| 3 | Diamondback Women's Serene Classic Comfort Bi | 80094.66   |
| 4 | Pelican Sunstream 100 Kayak                   | 66196.69   |
| 5 | Nike Men's Dri-FIT Victory Golf Polo          | 65750.00   |
| 6 | Nike Men's CJ Elite 2 TD Football Cleat       | 60705.33   |
| 7 | O'Brien Men's Neoprene Life Vest              | 58126.74   |
| 8 | Under Armour Girls' Toddler Spine Surge Runni | 26153.46   |
| 9 | LIJA Women's Eyelet Sleeveless Golf Polo      | 2145.00    |
| 9 | LIJA Women's Eyelet Sleeveless Golf Polo      | 2145.00    |
```
### 3.4. Toplam satış tutarı bakımından en çok iptal edilen (azalan sıra) kategorileri local diske parquet formatında yazınız.
```
    Örnek sonuç:

|:-:|:----------------------------------------:|:----------:|
| 0 | Fishing                                  | 134393.28  |
| 1 | Cleats                                   | 85785.70   |
| 2 | Cardio Equipment                         | 81351.93   |
| 3 | Camping & Hiking                         | 80094.66   |
| 4 | Water Sports                             | 66196.69   |
| 5 | Women's Apparel                          | 65750.00   |
| 6 | Men's Footwear                           | 60705.33   |
| 7 | Indoor/Outdoor Games                     | 58126.74   |
| 8 | Shop By Sport                            | 27423.44   |
| 9 | Electronics                              | 5685.50    |
| 9 | LIJA Women's Eyelet Sleeveless Golf Polo | 2145.00    |
```

### 3.5. En yüksek ortalama satış hangi yılın hangi ayında olmuştur?

### 3.6. En yüksek ortalama satış haftanın hangi gününde olmuştur?
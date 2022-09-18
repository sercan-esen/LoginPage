## Lazy Loading vs Eager Loading
Entity frameworkün yazılan LINQ sorgularına bağlı olarak veriyi veri tabanından yüklemek için kullandığı loading yöntemleridir.

### Lazy Loading

Lazy Loading, sayfada yer alan bir ögenin ihtiyaç duyulmadığı takdirde çağrılmaması prensibi ile çalışır
yani bir nesne örneğinin gerçekten ihtiyaç duyulacağı ana kadar alınmaması ve bekletilmesi amacıyla kullanılır.
Bu yöntemde veriler sorguya bağlı olarak çekilir ve veri setinin içindeki tüm dataları yüklemek yerine kullanılacağı an tekrar sorgu atar ve veriyi çeker.

Örneğin, aşağıda verilen sorguyu çalıştırdığımızda, UserDetails tablosu Kullanıcı tablosu ile birlikte yüklenmeyecektir.
```
User usr = dbContext.Users.FirstOrDefault(a => a.UserId == userId);
```
Yalnızca, aşağıda gösterildiği gibi, açıkça aradığınızda yüklenir.

```
UserDeatils ud = usr.UserDetails; // UserDetails are loaded here
```
Buda her yeni yükleme için veritabanına bir sorgu atılması demektir.

### Eager Loading

Lazy Loading’in tam tersi yönde çalışır.
Kullanacağımız nesneleri, nesnenin ihtiyaç anından çok önce yaratır ve bekletir.
Eager loading Linq sorgusu çalıştırıldığında verilerin tamamını yükler ve hafızaya alır.


Örneğin, bir Kullanıcı tablonuz ve bir KullanıcıDetaylar tablonuz (Kullanıcı tablosuyla ilişkili varlık),
o zaman aşağıda verilen kodu yazacaksınız. Burada, kullanıcıyı kullanıcı detayları ile birlikte kullanıcı kimliğine eşit bir ID ile yüklüyoruz.

```
User usr = dbContext.Users
                    .Include(a => a.UserDetails)
                    .FirstOrDefault(a => a.UserId == userId);
```

Eğer birden fazla alt öğe seviyemiz varsa, aşağıdaki sorguyu kullanarak yükleyebilirsiniz.

```
User usr = dbContext.Users
.Include(a => a.UserDetails.Select(ud => ud.Address))
.FirstOrDefault(a => a.UserId == userId);
```
Bu, ilişkili varlıkları sorgunun bir parçası olarak döndüren ve bir kerede büyük miktarda veri yükleyen yöntem bize zaman ve hız kazandırıyor.

### Avantaj/Dezavantajları

İki farklı yönteminde kendine göre avantajı ve dezaavantajı vardır bunlar geliştirmekte olduğumuz uygulamanın ihtiyaçlarına göre
veri tabanımızın büyüklüğüne göre ve orada ki ilişkilerin çokluğuna göre değişir.

Yukarıda ki basit örnekten yolaçıkarsak, lazy loading ile birbiriyle ilişkili olan entityler ihtiyaç oldukça çekilir.
Bu da bize içinde bulunduğumuz case’e göre performans açısından yarar sağlayabilir.Eager loading’e göre veritabanına çok daha fazla kez bağlanır
ve sorgu atar bunun da program için bir maliyeti vardır. Eager loading ise tek sorguda gerekli bilgileri elde eder.

![started](https://user-images.githubusercontent.com/99819569/190926899-edd04d49-5018-467e-8a27-2f4810ea6cdb.png )
![createa![login](https://user-images.githubusercontent.com/99819569/190926911-6866265a-d37c-4e04-b560-3a9d96f57a7d.png )
![login](https://user-images.githubusercontent.com/99819569/190926929-22fccb8d-1e77-43f8-bba9-6a7ee4384140.png )


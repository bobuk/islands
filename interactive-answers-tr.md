#İnteraktif yanıtlar

##Giriş
Yandex, site sahiplerinin arama sonuçlarındaki yanıtları interaktif hale getirmelerine yardımcı olan yeni bir arabirim ve yeni araçlar olarak "Adalar" platformunu piyasaya sürdü:
* Veri girişini arama sonuçları sayfasına taşıma
*	Sunulan bilgileri gerçek zamanlı biçimde iletme
*	İşlemleri arama sonuçları sayfasına taşıma

İnteraktif yanıtları oluşturmak için form açıklama dili ve Yandex ile gerçek zamanlı etkileşim API'si kullanılmasını öneriyoruz. Burada önce bu teknolojileri gözden geçirerek ve sonra da bunları kullanarak oluşturabileceğiniz yanıt örneklerine geçeceğiz.

Özelliklere kısa bir bakış
*	Form açıklama dili
*	Xml biçimindeki serbest semantik formun açıklanmasına ilişkin kurallar
*	İnteraktif arama yanıtında form test etme aracı
*	Etkileşim için Open Graph işaretleme (markup) standardı uzantısı: web-handler (deep link) ve http-handler (gerçek zamanlı etkileşim).

Bu belge ön belirtim statüsündedir. Yandex aramasında interaktif yanıtları hayata geçirme yollarıyla ilgili görüşlerimizin açıklandığı birinci sürümdür. Site sahipleriyle ortak çalışma sürecinde belirtimle ilgili eksikliklerin tamamlanacağını düşünüyoruz; bu nedenle sizlerden geri bildirim almaktan memnuniyet duyarız.

Şu an için, aracın web uzmanlarına yönelik beta sürümü kullanılabilmektedir. Bu araçta örnek olarak Yandex.Oto servisi için aramada interaktif yanıt özelliği kullanılmıştır. Siteniz için böyle bir yanıt oluşturmak isterseniz belgeleri okuyun ve form düzenleyicisinden yararlanın.

##Form açıklama dili
İnteraktif öğelerin yanıtta görünebilmesi için form açıklama dilinin kullanılmasını öneririz. Bunun yardımıyla formun alanları hakkında bilgi verebilir, türlerini ve olası değerlerini belirtebilirsiniz.

Form alanlarının açıklaması, sayfanın içinde işaretleme kullanılarak veya ayrı bir xml/json dosyasında Yandex'e bildirilebilir. İki yöntem için de destek vereceğiz.

Alanların olası değerleri (sözlükler) Yandex'in sitenizi ilgilendiren arama sorgularını çözümlemesine ve değerleri otomatik olarak formun ilgili alanlarına eklemesine olanak sağlar. Kullanıcı; açılan listeleri, onay kutularını vs. kullanarak sorguyu daraltabilir ve bunun yanı sıra düğmeyi tıkladığında sitenizdeki doğru şekilde oluşturulmuş URL'ye gider.

###Semantik
Alan adları ve alan değerleri isteğe bağlı ya da sabit olabilir. Bazı durumlarda form açıklamasının belirli bir tema için geliştirilmiş semantik standarda uygun olmasını zorunlu tutacağız. (Örneğin, uçuş kayıtları yaptırılırken.) Bu tür konu belirtimleri bu belgeye ek olarak yayınlanacaktır. Ayrıca daha önce servislerimizin faaliyete geçmesiyle birlikte kullanılmaya başlanan semantiği devralmayı tasarlıyoruz. (Örneğin, Yandex.Hizmetler için.)

Şimdi de şu örnekleri inceleyeceğiz: işlem düğmesi, birkaç alanı içeren basit form ve büyük değer sözlükleri içeren karmaşık form.

###İşlem düğmesi
gerçekleştiriliyorsa arama sonuçlarına işlem düğmesi koyabilirsiniz. Kullanıcı bu düğmeyi tıkladığında sitenizde işlemi gerçekleştireceği sayfaya gider.

**Örnek:** Uçuş kaydı için işlem düğmesi
<img src="http://img-fotki.yandex.ru/get/6800/148869347.0/0_a0a9e_7bbf842_orig">

#### Open Graph standardında işaretleme örneği
İşlem yapma olanağını belirtmek için, bu işlemi ilişkilendirmek istediğiniz sayfaya iki meta etiketi eklenmesi gerekir. Bir kafede masa rezervasyonu için görünüm şöyle olur: 
```html
<html prefix="og: http://ogp.me/ns#">
  <head>
    <meta property="og:type" content="..." />
    ...
    <meta property="og:interaction" content="BookTable" /> 
    <meta property="og:interaction:web_handler" content="http://menu.ru/order/table" /> 
  ...
  </head>
  ...
</html>
```
Interaction ile başlayan etiketler, Open Graph Protocol (http://ogp.me) açık standardının uzantısıdır.
* **interaction** (interaction [array](http://ogp.me/#array)): Olası işlemi belirten zorunlu etiket. Örnek olarak restoran rezervasyonu ("BookTable" değeri), otel rezervasyonu ("BookHotel" değeri) vs. verilebilir. Belirli işlemleri ve bunlara ilişkin değerleri ürünler çıktıkça kademeli olarak yayınlayacağız.
*	**interaction:web_handler** ([URL](http://ogp.me/#url)): İşlemin tamamlanması için gidilmesi gereken sayfanın adresini belirten zorunlu etiket. Bu adres, işlem sayfanızda gerçekleştirilebiliyorsa sitenizin bir sayfası olabileceği gibi, kendi siteniz böyle bir olanak sağlamıyorsa başka bir sitenin sayfası (toplayıcı sayfası) da olabilir.
*	**interaction:paid_service** ([Boolean](http://ogp.me/#bool)): İşlem adımlarından birinde kullanıcının para ödemesi gerektiğini belirten isteğe bağlı etiket. Etiket belirtilmezse, varsayılan olarak işlemin tümüyle ücretsiz olduğu kabul edilir. Ürün satın alınması, ücretli bir işleme örnektir.

Ayrıca standardın bir gereği olarak html veya head etiketinde prefix="og: http://ogp.me/ns#" öneki belirtilmelidir.

###Basit form
Az sayıda alan içeren basit formlar için açıklamanın sayfadaki form html etiketinin içinde yapılması uygundur. Schema.org veya Open Graph standartlarında konudaki parametreler sabit ise, form açıklaması işaretleme kullanılarak yapılabilecektir.

**Örnek:** Uçuş kaydı için basit bir form
<img src=http://img-fotki.yandex.ru/get/6711/148869347.0/0_a0ac3_77715c8_orig>

**todo:** [XForms](http://en.wikipedia.org/wiki/XForms) standardını kullanma olasılığını değerlendirme

###Karmaşık form ve sözlükler
Karmaşık arama formları ve büyük sözlükler içeren siteler için, formun ayrı bir dosyada açıklanması daha uygundur. Bu seçenek, web uzmanlarına yönelik interaktif yanıtlar beta sürümü'nde desteklenmektedir. Form düzenleyicisinden yararlanabilirsiniz: Alanlarla ilgili açıklamayı iletebilir ve algoritmamızın işleyişini test edebilirsiniz. Burada ayrıntılı belgeler de bulunmaktadır.

**Örnek:** Otomobillerle ilgili site için arama formu 
<img src=http://img-fotki.yandex.ru/get/6709/148869347.0/0_a0ac7_800b06d0_orig> 

##Gerçek zamanlı interaktif API
Kullanıcıyla gerçek zamanlı etkileşim amacına yönelik etkileşimli yanıtlar oluşturmak için, sitenizde API'yi uygulamaya geçirmek gerekir. Sitenizde böyle bir API kullanılmaya başlandığını bize Open Graph işaretlemesi kullanarak veya özel Yandex.Webmaster bölümünde bildirebilirsiniz. Şimdi de, arama sonuçları sayfalarında desteklemeyi planladığımız API özelliklerini vereceğiz.

####API, belirli bir sayfa için gerçek zamanlı verileri sonuç olarak getirebilir
Buna örnek olarak bir servis için bugünkü hava durumu veya havaalanı tarifesi, bir ürün sayfası için ürünün fiyatı ve stok durumu, varlık listesini içeren bir sayfa için bulunanların sayısı veya fiyat aralığı verilebilir. Bu durumda API, sitenin belirli bir sayfasıyla ilişkilendirilmelidir.

####API, giriş parametrelerine bağlı olarak gerçek zamanlı verileri sonuç olarak getirebilir
API giriş parametreleri isteğe bağlı olabilir ve alan açıklamaları ve form değerleri aracılığıyla Yandex'e iletilebilir. Bazı durumlarda parametreler belirli bir konu için sabit olacaktır (bkz. semantik hakkında ayrıntılı bilgi). Gerçek zamanlı verilerle sunulan yanıta ek olarak servis API'si, kullanıcıyı, formda girilen parametrelere uygun düşen ve kullanıcının bağlamını (örneğin, bölge ve dil) dikkate alan doğru URL'ye (deep link) yönlendirebilir.

Ayrıca API için yanıt biçimi (xml/json), yanıt zaman aşımı (konuya bağlı olarak), izin verilen başvuru sıklığı belirtilecektir. API'nin yüksek bir hızla (300-400 ms) yanıt vermesini ve belirli bir yükü kaldırmasını bekliyoruz.


**Örnek:** Tarih ve uzman türü seçimi doktorların güncel çizelgesini göstermektedir  
<img src=http://img-fotki.yandex.ru/get/6701/148869347.0/0_a0ac5_c7519c39_orig>


###Open Graph standardına göre API belirtimi

Open Graph'ta işaretlemeyi kullanarak belirli bir konuda ("otel rezervasyonu") API'nin ve yanıtının nasıl belirtilebileceğini bir örnek üzerinde göstereceğiz.

Geliş tarihlerine (arrival), ayrılış tarihlerine (departure) ve konuk sayısına (guests) göre otel odası fiyatını göstermek istediğimizi düşünelim. Bununla birlikte, doğru şekilde görüntüleme için para biriminin (currency) ve yanıt dilinin (lang) belirtilmesi de gerekmektedir. Kullanıcı siteye gitmeden geliş ve ayrılış zamanını değiştirerek seçtiği tarihler için otel odası fiyatını ve işleme devam edebileceği URL'yi alır.

#####API işleyişinin açıklanmasıyla ilgili örnek
İşaretleme, nesne açıklamasının bir parçası olarak görünebilir:

```html
<html prefix="og: http://ogp.me/ns#">
  <head>
    <meta property="og:type" content="..." />
    ...
    <!--Возможность забронировать номер в отеле -->
    <meta property="og:interaction" content="BookHotel" /> 
    <meta property="og:interaction:http_handler" content="http://host/prefix?" /> 
  ...
  </head>
  ...
</html>
```
Interaction ile başlayan etiketler, Open Graph Protocol (http://ogp.me) açık standardının uzantısıdır.

*  **interaction:http_handler** (URL): URL API'sini belirten zorunlu etiket. Bu nedenle, URL'nin kullanıcı tarafından girilen parametrelere bağlı olarak yanıt alma olanağı olmalıdır.
*	**interaction:http_handler:response_type** (enum): API için sorgu türünü belirten isteğe bağlı etiket. Etiket belirtilmezse varsayılan olarak GET olduğu kabul edilir. Olası değerler GET ve POST'tur.
*	**interaction:http_handler:response_format** (enum): Yanıt türünü belirten isteğe bağlı etiket. Etiket belirtilmezse varsayılan olarak JSON olduğu kabul edilir. Olası değerler JSON ve XML'dir.
*	**interaction:paid_service** (Boolean): İşlem adımlarından birinde kullanıcının para ödemesi gerekebileceğini belirten isteğe bağlı etiket. Etiket belirtilmezse varsayılan olarak işlemin tümüyle ücretsiz olduğu kabul edilir. Ürün satın alınması, ücretli bir işleme örnektir.

Aynı şekilde; ancak zorunlu olmayan parametreler de belirtilerek:
```html
<html prefix="og: http://ogp.me/ns#">
  <head>
    <meta property="og:type" content="..." />
    ...
    <!--Возможность забронировать номер в отеле -->
    <meta property="og:interaction" content="BookHotel" /> 
    <meta property="og:interaction:http_handler" content="http://host/prefix?hotel=433" /> 
    <meta property="og:interaction:http_handler:response_type" content="GET" />
    <meta property="og:interaction:http_handler:response_format" content="JSON" />
    ...
  </head>
  ...
</html>
```
Örnekteki koşullara göre, kullanıcı geliş tarihini (arrival), ayrılış tarihini (departure) ve konuk sayısını (guests) belirtir; para birimi (currency) ve yanıt dili (lang) parametrelerini de geçerli bağlamından anlarız. Böylece Yandex'ten API'ye gönderilen tam sorgu şöyle görünür: **http://host/prefix?hotel=433&arrival=2014-12-10&departure=2014-12-21&currency=RUB&lang=RU&guests=2**.

API servisinin yanıt olarak, seçilen tarihler için otel odası fiyatını ve ayrıca sonraki işlem adımının gerçekleştiği URL'yi vermesi gerekir. Örneğin:
```json
{
   "price" : {
     "value" : 2455.00,
     "currency" : "RUB"
   },
   "url" : "http://host/url"
}
```

##İnteraktif yanıtlara örnekler
"Taksi çağırma" örnek olay incelemesinde farklı interaktif yanıt seçeneklerini inceleyeceğiz. Yukarıda açıklanan teknolojilere dayanarak işlevsel öğeleri sırayla ekleyeceğiz.

####Gerçek zamanlı verilerle yanıt
Öncelikle, interaktif yanıta gerçek zamanlı verileri ekleyeceğiz. Yandex bunun için, arama sonuçları sayfasının yüklenmesiyle eşzamansız olarak servis API'sini sorgular. Bundan sonra API, verileri gönderdikçe (örneğin, boş otomobil sayısı) bu veriler yanıta yüklenmeye devam eder.

<img src="http://img-fotki.yandex.ru/get/6801/148869347.0/0_a0ae6_8a78dfb5_orig">

####İşlem düğmesi
Şimdi de sitede bir işlem yapma, yani bu örneğe göre taksi çağırma olanağını belirteceğiz. Sitenin, işlemin gerçekleştirildiği doğru dahili URL'yi Yandex'e göndermesi gerekir. Bazı durumlarda bu işlem üçüncü taraf servislerce ve ayrı bir URL'de gerçekleştirilebilir (örneğin, sinema için bilet alma).

<img src="http://img-fotki.yandex.ru/get/6706/148869347.0/0_a0ae7_b133c343_orig">

####İşlem formu
Kullanıcının yolculuk güzergahını belirtebilmesi veya arama sorgusunda bunu bildirebilmesi için birkaç alan ekleyelim. Örneğin, [Haydarpaşa Garı'na gitmek için taksi çağırma] sorgusunda konumu algılayacağız ve "Nereye" alanına yerleştireceğiz. "Sipariş et" düğmesine basıldığında kullanıcı işleme devam edeceği siteye gider ve Yandex de belirtilen güzergahın parametrelerini siteye iletir.

<img src="http://img-fotki.yandex.ru/get/6714/148869347.0/0_a0ae8_d4c52ee1_orig">

Arama interaktif yanıtını demo olarak göstermek için [Yandex.Oto]() servisi için kullanıma hazır bir örnek hazırladık.

Для демонстрации поискового интерактивного ответа мы подготовили [работающий пример для сервиса Яндекс.Авто](http://interactive-answers.webmaster.yandex.com.tr/).

####Gerçek zamanlı önizleme sonucunu içeren işlem formu
Son olarak, forma etkileşim özelliğini ekleyeceğiz ve kullanıcının işlemine gerçek zamanlı olarak yanıt vermeye başlayacağız. Örneğimizde servis API'si, belirtilen güzergaha göre yolculuk maliyetini döndürmektedir.

<img src="http://img-fotki.yandex.ru/get/6703/148869347.0/0_a0ae9_b9739a01_orig">

####Arabirimde SERP (Arama Motoru Sonuç Sayfası) üzerinde işlem
Bazı durumlarda, hem kullanıcı hem de site sahibi için uygun olduğunda, işlem tümüyle arama sonuçları sayfasında gerçekleştirilebilecektir. Örneğin, uçuş kaydı veya masa rezervasyonu yapılabilecektir. Bunun için daha önce olduğu gibi form açıklama dilini ve gerçek zamanlı interaktif API'yi kullanırız. Birincisinde, işlemin tamamlanması için veri girişi yapılacak alan açıklanmalı ve ikincisinde de API özelliği genişletilmeli ve işleme göre uygun durum bilgileri ("olası", "işlemde", "gerçekleştirildi", "iptal edildi") Yandex'e geri gönderilmelidir.

Örneğimizde, ad ve iletişim bilgilerini girilmesi ve taksi gönderilme saatinin belirtilmesi kalır. Servis API'si siparişin onaylandığını doğrularsa Yandex bunu kullanıcıya bildirir.

<img src="http://img-fotki.yandex.ru/get/6722/148869347.0/0_a0b24_6d6c769a_orig">

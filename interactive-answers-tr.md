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

###Простая форма
Для простых форм, которые содержат небольшое количество полей, описание удобно выполнять на самой странице внутри html-тега **form**. Если параметры в тематике зафиксированы в страндартах schema.org или Open Graph, то описание формы можно будет сделать c помощью разметки.

**Пример:** простая форма для регистрации на рейс
<img src=http://img-fotki.yandex.ru/get/6711/148869347.0/0_a0ac3_77715c8_orig>

**todo:** рассмотреть возможность использования стандарта [XForms](http://en.wikipedia.org/wiki/XForms)

###Сложная форма и словари
Для сайтов, которые содержат сложные поисковые формы и большие словари значений, форму удобнее описывать в отдельном файле. Этот вариант поддержан в **[бета-версии интерактивных ответов](http://interactive-answers.webmaster.yandex.ru/)** для вебмастеров. Вы можете воспользоваться  редактором форм: передать описание полей и протестировать работу нашего алгоритма. Там же размещена **[подробная документация](http://help.yandex.ru/webmaster/?id=1127879)**.

**Пример:** поисковая форма для сайта про автомобили
<img src=http://img-fotki.yandex.ru/get/6709/148869347.0/0_a0ac7_800b06d0_orig> 

##API для real-time взаимодействия
Для создания интерактивных ответов, которые предполагают реал-тайм взаимодействие c пользователем, на вашем сайте необходимо реализовать API. Сообщить нам о появлении такого API можно будет с помощью  [разметки в Open Graph](#-api---open-graph) или в специальном разделе Яндекс.Вебмастера. Далее приводим возможности API, которые собираемся поддерживать на страницах результатов поиска.

####API может возвращать real-time данные для конкретной страницы
Например, для сервиса это могут быть погода на сегодня или табло аэропорта, для страницы товара &mdash; его цена и наличие; для страницы, которая содержит списки объектов, &mdash; количество найденного или диапазон цен. В этом случае API должен быть привязан к конкретной странице сайта.

####API может возвращать real-time данные, в зависимости от входящих параметров
Параметры на входе API могут быть произвольными и передаваться Яндексу через описание полей и значений формы. В некоторых случаях параметры будут фиксироваться для конкретной тематики (см. подробнее [о семантике](#-1)). Помимо ответа real-time данными API сервиса может перенаправлять пользователя на правильный URL (deep link), который соответствует набранным параметрам в форме и учитывает контекст пользователя (например, регион и язык). 

Также для API будут специфицированы формат ответа (xml/json), время устаревания ответа (зависит от тематики), разрешенная частота обращений. Мы ожидаем, что API будут отвечать с высокой скоростью (300-400 мсек) и выдерживать некоторую нагрузку.

**Пример:** выбор даты и вида специалиста показывает актуальное расписание врачей
<img src=http://img-fotki.yandex.ru/get/6701/148869347.0/0_a0ac5_c7519c39_orig>


###Спецификация API по стандарту Open Graph

Покажем на примере, как с помощью разметки в Open Graph может быть специфицировано API и его ответ в конкретной тематике &mdash; &laquo;бронирование отелей&raquo;. 

Представим, что мы хотим показать цену на номер в отеле в зависимости от дат заезда (arrival), выезда (departure) и количества гостей (guests). При этом для корректного отображения необходимо также задать валюту цены (currency) и язык ответа (lang). Изменяя время заезда и выезда пользователь без перехода на сайт получает цену номера в отеле на выбранные даты, а также URL, по которому можно продолжить транзакцию.

#####Пример описания работы API
Разметка может выглядеть как часть описания объекта:

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

Теги, начинающиеся с **interaction**, являются расширением открытого стандарта Open Graph Protocol (http://ogp.me).

* **interaction:http_handler** ([URL](http://ogp.me/#url)) **обязательный** тег, обозначающий API урл. По этому урлу должна быть возможность получить ответ в зависимости от заданных пользователем параметров.
* **interaction:http_handler:response_type** ([enum]((http://ogp.me/#enum)) &mdash; необязательный тег, обозначающий тип запроса к API. Если тег не указан, по умолчанию тип считается GET. Возможные значения GET, POST.
* **interaction:http_handler:response_format** ([enum]((http://ogp.me/#enum)) &mdash; необязательный тег, обозначающий тип ответа. Если тег не указан, по умолчанию тип считается JSON. Возможные значения JSON, XML.
* **interaction:paid_service** ([Boolean](http://ogp.me/#bool)) &mdash; необязательный тег, указывающий, что на одном из шагов транзакции пользователю может потребоваться заплатить денег. Если тег не указан, по умолчанию считается, что вся транзакция бесплатная. Пример платной транзакции &mdash; покупка товара.

То же, но с указанием необязательных параметров:
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
По условиям примера пользователь указывает дату заезда (arrival), выезда (departure) и количества гостей (guests), а параметры &mdash; валюта цены (currency) и язык ответа (lang) мы понимаем из его текущего контекста. Тогда полный запрос к API от Яндекса будет выглядеть так: **http://host/prefix?hotel=433&arrival=2014-12-10&departure=2014-12-21&currency=RUB&lang=RU&guests=2**. 

В ответ API сервиса должен вернуть цену номера в отеле на выбранные даты, а также URL, на котором происходит следующий шаг транзакции. Например, так:
```json
{
   "price" : {
     "value" : 2455.00,
     "currency" : "RUB"
   },
   "url" : "http://host/url"
}
```

##Примеры интерактивных ответов
Рассмотрим различные варианты интерактивных ответов на примере кейса &laquo;заказ такси&raquo;. Будем последовательно добавлять функциональные элементы, опираясь на описанные выше технологии. 

####Ответ real-time данными
Вначале добавим real-time данные в интерактивный ответ. Для этого Яндекс вместе с загрузкой страницы результатов поиска асинхронно запрашивает API сервиса. После того, как API отдает данные, например, количество свободных машин, они догружаются в ответ.

<img src="http://img-fotki.yandex.ru/get/6801/148869347.0/0_a0ae6_8a78dfb5_orig">

####Транзакционная кнопка
Теперь укажем на возможность совершить транзакцию на сайте, в данном случае &mdash; заказать такси. Сайт должен передать Яндексу правильный внутренний URL, на котором выполняется действие. Для некоторых случаев транзакция может выполняться сторонним сервисом и на отдельном URL (например, покупка билета в кино). 

<img src="http://img-fotki.yandex.ru/get/6706/148869347.0/0_a0ae7_b133c343_orig">

####Транзакционная форма
Добавим несколько полей, чтобы пользователь мог указать маршут поездки или сообщить его в поисковом запросе. Например, в запросе [заказ такси до ленинградского вокзала] мы распознаем географию и проставим ее в поле &laquo;Куда&raquo;. По клику на кнопку &laquo;Заказать&raquo; пользователь переходит на сайт для продолжения транзакции, а Яндекс передает сайту параметры указанного маршрута.

<img src="http://img-fotki.yandex.ru/get/6714/148869347.0/0_a0ae8_d4c52ee1_orig">

Для демонстрации поискового интерактивного ответа мы подготовили [работающий пример для сервиса Яндекс.Авто](http://interactive-answers.webmaster.yandex.ru/).

####Транзакционная форма с real-time предпросмотром результата 
Наконец, добавим форме интерактивности &mdash; начнем real-time отвечать на работу пользователя с формой. В нашем примере API сервиса возвращает стоимость поездки в зависимости от указанного маршрута.

<img src="http://img-fotki.yandex.ru/get/6703/148869347.0/0_a0ae9_b9739a01_orig">

####Транзакция на СЕРПе в интерфейсе
В некоторых случаях, когда это выгодно и пользователю, и владельцу сайта, транзакцию можно будет полностью провести на странице результатов поиска. Например,  зарегистрироватся на рейс или забронировать столик. Для этого мы по-прежнему будем использовать язык описания форм и API для real-time взаимодействия. В первом случае необходимо описать поля по вводу данных для совершения транзакции, во втором - расширить возможности API и возвращать Яндексу соответствующие статусы по транзакции (&laquo;возможна&raquo;, &laquo;в процессе&raquo;, &laquo;проведена&raquo;, &laquo;отменена&raquo;).

В нашем примере осталось ввести свое имя, контактный телефон и указать время подачи такси. Если API сервиса подтвердит оформление заказа, Яндекс сообщит об этом пользователю.

<img src="http://img-fotki.yandex.ru/get/6722/148869347.0/0_a0b24_6d6c769a_orig">

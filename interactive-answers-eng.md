#Interactive snippets

##Introduction
[Yandex Islands](http://beta.yandex.com/), a new search platform, gives website owners an interface and tools to create interactive snippets for their websites. 

* move data entry to the search results page.
* provide information from your service in real-time. 
* make transactions on the search results page.
 
To create interactive snippets, we offer a [form description language](#form-description-language) and guidelines for preparing the [API for real-time interaction](#api-for-real-time-interaction). First, we document our technologies, and then we show examples of how to use them.

**Overview of features** 
* Form description language
*	[Guidelines](http://help.yandex.com/webmaster/?id=1127882) for describing forms with custom semantics in XML format
*	Tool for testing forms in interactive snippets
*	Extension of the Open Graph markup standard for interaction: [web-handler](#example-of-open-graph-markup) (deep link) and [http-handler](#api-specification-based-on-the-open-graph-standard) (real-time interaction)

This document covers the concepts of interactive snippets in Yandex Search. This is the first version of the document, and we welcome feedback. Only the beta version of the "Interactive answers" tool is currently available, which implements the search form description as a separate XML file. The other types of interactive snippets will be supported in the future.

##Form description language
In order for interactive elements to appear in search results, we use a form description language. Using this language, you can describe the fields on a form, specify their types, and list acceptable values. You can submit the form description in two ways: either as markup on the page itself, or in a separate XML/JSON file. We will support both methods.

Lists of acceptable field values (dictionaries) make it possible for Yandex to parse search queries related to your website and put them in the appropriate fields on the form. The user can refine the query using drop-down lists, checkboxes, and so on, and will then be sent to the appropriate URL on your website. 

###Semantics
The names of fields and values can be arbitrary or fixed. In certain cases we require that form descriptions follow a semantic standard that has been developed for a particular subject area, such as for flight check-ins. These thematic specifications will be published as appendices to this document. In addition, we are preserving semantics that were already introduced with the launch of our services, such as the semantics for [Yandex.Amenities](http://help.yandex.ru/webmaster/?id=1112634).

Now we will look at examples: the transaction button, a basic form with a few fields, and a complex form with large dictionaries of values. 

###Transaction button
If your website performs online transactions (such as reservations, registration, appointment scheduling, or purchases), you can put a transaction button in the search results. After clicking it, a user goes directly to completing the action on your website.  

**Example:** transaction button for flight check-in
<img src=http://img-fotki.yandex.ru/get/6701/148869347.0/0_a0b02_6e7681fd_orig>

####Example of Open Graph markup

To indicate that a transaction can be made, you need to add two metatags to the page on your website that you want to bind this action to. For reserving a table at a restaurant, it looks like this: 
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

Tags that start with **interaction** are our extension of the open standard Open Graph Protocol (http://ogp.me).

* **interaction** (interaction [array](http://ogp.me/#array)) &mdash; **mandatory** tag that defines possible actions. Examples are making a reservation at a restaurant ("BookTable" value), reserving a hotel room ("BookHotel" value) and others. We will be publishing specific actions and values for them as products are added.
* **interaction:web_handler** ([URL](http://ogp.me/#url)) &mdash; **mandatory** tag that defines the address of the page to go to for completing the action. This can be a page on your website if you can complete the action there, or a page on another website (an aggregator's site) if your site does not offer this feature. 
* **interaction:paid_service** ([Boolean](http://ogp.me/#bool)) &mdash; optional tag indicating that the user may be required to pay money at some point during the transaction. If omitted, by default the entire transaction is assumed to be free of charge. An example of a paid service is purchasing merchandise. 

Don't forget to set prefix="og: http://ogp.me/ns#" in the html tag or head &mdash; this is required by the protocol.

###Basic forms
For simple forms that contain a small number of fields, it is convenient to put the description on the page itself, inside the HTML tag **form**. If the parameters are thematically established by a standard (schema.org or OpenGraph), the form description can be created using markup.

**Example:** basic form for flight check-in:

<img src=http://img-fotki.yandex.ru/get/6715/148869347.0/0_a0b01_399afda6_orig>

**todo:** consider using the [XForms](http://en.wikipedia.org/wiki/XForms) standart 

###Complex forms and dictionaries
For websites that contain complex search forms and large dictionaries of values, it is more convenient to put the form description in a separate file. This option is supported in the **[Beta version of interactive snippets](http://interactive-answers.webmaster.yandex.com/)** for webmasters. You can try out the form editor: submit a form description and test our algorithm for generating the form and parsing queries for your site. You will also find **[detailed instructions](http://help.yandex.com/webmaster/?id=1127882)** there. 

**Example:** search form for a website about automobiles
<img src=http://img-fotki.yandex.ru/get/6721/148869347.0/0_a0b11_fe663071_orig>

##API for real-time interaction
To create interactive answers that presume real-time interaction with users, you will need to implement an API on your website. You can notify us that you have added an API either using [Open Graph markup](#api-specification-based-on-the-open-graph-standard) or in a special section of Yandex.Webmaster. These are the API features that we plan to support on search results pages: 

####The API can return real-time data for a specific page
For example, for a service, this might be today's weather forecast or an airport departures/arrivals board; for a product page, it could be the price and availability; or for a list page, the number of objects found or a range of prices. In this case, the API must be bound to a specific page on the website.

####The API can return real-time data dependent on input parameters
Parameters that are input to the API may be arbitrary and can be passed to Yandex in the description of the form's fields and values. In some cases, parameters will be standardized for a particular subject area (see detailed information [on semantics](#semantics). In addition to responding with real-time data, the service's API can redirect the user to the appropriate URL (deep link), which matches the parameters entered in the form and can be sensitive to the user's context (such as region and language).

Additionally, the API will have specifications for the response format (XML/JSON), expiration of the answer (depending on the subject), and the allowed access rate. We expect that the API will respond at a high speed (300-400 msec) and be able to handle a reasonable load.

**Example:** choosing a date and type of specialist shows doctors' updated schedules
<img src=http://img-fotki.yandex.ru/get/6805/148869347.0/0_a0b03_a1b4f705_orig>


#### API specification based on the Open Graph standard

Let's look at an example of how to use Open Graph markup for the API and its response for a specific area, &laquo;making a hotel reservation&raquo;.

Let's imagine that we want to show the price of a hotel room based on the check-in date (arrival), check-out date (departure) and number of occupants (guests). In addition, to display it correctly, we also need to set the price's currency (currency) and the language for the answer (lang). The user can change the dates for arrival and departure to get the price of a hotel room for the desired dates, without going to the website. The user also gets a URL for continuing the transaction.

####Example of describing the API in Open Graph markup
The markup may look like a part of an object description:

```html
<html prefix="og: http://ogp.me/ns#">
  <head>
    <meta property="og:type" content="..." />
    ...
    <!--Hotel reservation feature -->
    <meta property="og:interaction" content="BookHotel" /> 
    <meta property="og:interaction:http_handler" content="http://host/prefix?" /> 
  ...
  </head>
  ...
</html>
```

Tags that start with **interaction** are our extension of the open standard Open Graph Protocol (http://ogp.me).

* **interaction:http_handler** ([URL](http://ogp.me/#url)) &mdash; **mandatory** tag that defines the URL of the API. This URL must work for getting a response that depends on parameters set by the user. 
* **interaction:http_handler:response_type** ([enum]((http://ogp.me/#enum)) &mdash; optional tag that defines the type of API request. If omitted, by default the type is assumed to be GET. Acceptable values are GET and POST.
* **interaction:http_handler:response_format** ([enum]((http://ogp.me/#enum)) &mdash; optional tag that defines the type of response. If omitted, by default the type is assumed to be JSON. Acceptable values are JSON and XML.
* **interaction:paid_service** ([Boolean](http://ogp.me/#bool)) &mdash; optional tag indicating that the user may be required to pay money at some point during the transaction. If omitted, by default the entire transaction is assumed to be free of charge. An example of a paid service is purchasing merchandise.

Don't forget to set prefix="og: http://ogp.me/ns#" in the html tag or head &mdash; this is required by the protocol.

The same thing, but with optional parameters:

```html
<html prefix="og: http://ogp.me/ns#">
  <head>
    <meta property="og:type" content="..." />
    ...
    <!--Hotel reservation feature -->
    <meta property="og:interaction" content="BookHotel" /> 
    <meta property="og:interaction:http_handler" content="http://host/prefix?hotel=433" /> 
    <meta property="og:interaction:http_handler:response_type" content="GET" />
    <meta property="og:interaction:http_handler:response_format" content="JSON" />
    ...
  </head>
  ...
</html>
```

For this example, the user selects the check-in date (arrival), check-out date (departure) and number of occupants (guests), while we get the price currency (currency) and language (lang) to display based on the current context.  So the complete request from Yandex to the API will look like this: **http://host/prefix?hotel=433&arrival=2014-12-10&departure=2014-12-21&currency=RUB&lang=RU&guests=2**. 

In response, the service's API must return the price of a hotel room for the selected dates, along with a URL where the next step of the transaction will be completed. For example, like this:

```json
{
   "price" : {
     "value" : 3455.00,
     "currency" : "RUB"
   },
   "url" : "http://host/url"
}
```


##Examples of interactive snippets
Let's look at different types of interactive snippets for the use case of "ordering a taxi". We will progressively add functional elements, relying on the technologies described above.

####Answer with real-time data
First we'll add real-time data to the interactive snippet. To do this, while loading the search results page, Yandex asynchronously sends a request to the service's API. After the API returns the data, such as the number of taxis available, this data is loaded in the island.

<img src=http://img-fotki.yandex.ru/get/6721/148869347.0/0_a0afd_177fa34d_orig>

####Transaction button
Now we will point out that a transaction can be made on the site; in this case, the user can order a taxi. The site must send Yandex the correct internal URL for performing this action. In some cases, the transaction may be performed by a different service and at a separate URL (for example, for buying movie tickets).  

<img src=http://img-fotki.yandex.ru/get/6719/148869347.0/0_a0afe_b69a9c99_orig>

####Transaction form
We will add a few fields so the user can choose the trip route or specify it in the search query. For example, in the query [order taxi to train station], we recognize the geographic location and put it in the "To" field. After clicking "Order" the user is redirected to the website for continuing the transaction, and Yandex passes the route parameters to the site.

<img src=http://img-fotki.yandex.ru/get/6711/148869347.0/0_a0afa_e8171299_orig>

To demonstrate interactive search snippets, we have created a [working sample for the Сars.com](http://interactive-answers.webmaster.yandex.com/).

####Transaction form with real-time preview of results
Finally, we will make the form interactive &mdash; we will start responding to the user's actions on the form in real-time. In our example, the service's API returns the trip price depending on the selected route.

<img src=http://img-fotki.yandex.ru/get/6829/148869347.0/0_a0afb_9b6d17df_orig>

####Transaction on SERP in the interface
In certain cases, when it is to the advantage of both the user and the website owner, the transaction can be completed entirely on the search results page. Examples of this are checking in for a flight or reserving a table at a restaurant. To do this, we still use the form description language and the API for real-time interaction. In the first case, we must describe the fields for entering data for completing the transaction, and in the second case, we will need to expand the API features to send Yandex status updates for the transaction (possible, pending, confirmed, rejected).

The only step left in our example is to enter the name, phone number, and the time to deliver the taxi. If the service's API confirms the order, Yandex will notify the user.

<img src=http://img-fotki.yandex.ru/get/6709/148869347.0/0_a0b04_e635852d_orig>

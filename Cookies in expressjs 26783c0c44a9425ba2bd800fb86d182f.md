# Cookies in expressjs

Momenteel volg ik de minor Web Development aan de hogeschool van Amsterdam. Tijdens één van de modules kwam het begrip cookies voorbij. Echter had ik tijdens deze module niet genoeg tijd om dit onderdeel te onderzoeken. Om toch te leren wat cookies precies zijn en hoe men dit kan gebruiken, heb ik in een later stadium cookies onderzocht. In dit artikel beschrijf ik dit onderzoek. Het onderzoek focust zich vooral op het gebruik van cookies in expressjs.

![Cookies%20in%20expressjs%2026783c0c44a9425ba2bd800fb86d182f/cookie-monster.jpg](Cookies%20in%20expressjs%2026783c0c44a9425ba2bd800fb86d182f/cookie-monster.jpg)

Credit: Miguel Fernandez Source: [https://gegen-den-strich.com/](https://gegen-den-strich.com/)

# Wat zijn cookies?

Een cookie is een stuk tekst dat een web server kan opslaan op de harde schijf van de gebruiker. Dankzij cookies kan een web site informatie opslaan op de harde schijf van de gebruiker en kan deze later terughalen. De stukken informatie wordt opgeslagen als naam-waarde duo's. Een naam-waarde duo is een simpel stukje data die een naam heeft.

Een website kan enkel data terughalen die het zelf heeft opgeslagen. De website kan niet bij andere cookies; of bij iets anders op jouw computer.

Als men een cookie opent, kan men de naam en content zien, welke website deze heeft geplaatst, wanneer deze is aangemaakt en wanneer deze verloopt.

Een cookie laat een website onthouden in welke staat de browser zich verkeerd. Voorbeelden van cookies zijn: een user-ID, een session-ID, user preferences. Ook is het zetten van een cookie met een uniek ID voor iedere bezoeker een accurate manier om te meten hoeveel bezoekers een website heeft en hoeveel hiervan nieuw zijn of de website al een keer heeft bezocht. Dit is niet volledig bulletproof, omdat cookies verwijderd kunnen worden.

# Hoe werken cookies?

De browser stuurt een request voor de website. De browser controleert op cookies voor desbetreffende site. Als het een of meerdere cookies vind, dan verstuurd het deze cookies mee. Als de browser geen cookies kan vinden, verstuurd hij niks mee.

Als de webserver cookies ontvangt, kan het deze vervolgens gaan gebruiken. Wanneer er geen cookies zijn ontvangen, weet de webserver dat de gebruiker nog niet bekend is op de website. En zal deze de benodigde cookies (zoals bv. een user-ID) creëren en opslaan op de harde schijf van de gebruiker. De webserver kan cookies aanpassen en toevoegen.

# Hoe implementeert men cookies?

Een cookie wordt gecreëerd als een webserver de browser verteld dat hij een cookie moet aanmaken. De instructies hiervoor worden gestuurd in een HTTP header. Dit ziet er zo uit: `Set-Cookie: <cookie_name≥<cookie_value>` .

Men kan ook gebruik maken van `document.cookie` aan de client-side.

Men kan een 'session cookie' maken, wiens alleen geldig is tijdens de huidige sessie, maar ook een cookie die langer bewaart wordt maken. Dit kan men doen door de `expires` attribute toe te voegen aan de header.

# Wetgeving

Sinds een aantal jaren zijn er een aantal wetten gemaakt wat betreft cookies. De wetgeving verschilt per locatie.

## EU

Elk EU-land heeft dezelfde cookie wet. In deze wet staat dat wanneer een website zich richt op gebruikers binnen deze landen of het bedrijf gevestigd is in de EU, eerst toestemming gevraagd moet worden aan de gebruiker voordat cookies gebruikt worden.

## Amerika

Als cookies gebruikt worden voor sales en reclame op basis van third-party tracking, moet het volgens de Federal Trade Commission men de gebruiker ervan bewust maken wat er gebeurt.

## Privacy Policy

In meerdere landen, inclusief de VS, Australië, VK en ieder land in de EU, moet men de gebruiker laten weten waar hun persoonlijke gegevens voor worden gebruikt. Als men cookies gebruikt om de activiteit van de gebruikers op te slaan (met bijvoorbeeld Google Analytics) moet men een privacy policy hebben. Hierin moet staan welke data verzameld wordt en hoe het gebruikt wordt.

# Cookies bekijken in Chrome

1. Ga naar instellingen door op de drie bolletjes te drukken rechtsboven en op instellingen te drukken.
2. Ga naar het kopje Privacy en beveiliging
3. Klik op Site-instellingen
4. Klik op Cookies en sitegegevens
5. Klik op Alle cookies en sitegegevens weergeven

# Cookies in expressjs

In expressjs kan men cookies aanmaken en lezen door middel van middleware. Om cookies aan te maken en te lezen kan men de cookie-parser middleware gebruiken. Echter zijn dan de cookies leesbaar op de client-side. Om dit te verhelpen kan men gebruik maken van sessies met de express-sessions middleware. Met sessies krijgt de client een uniek ID. Vervolgens worden alle requests van de client gekoppeld aan dit ID. Informatie van de client wordt dan op de server opgeslagen met dit ID.

### Cookie-parser middleware

### Setup

Allereerst moet men de middleware in het project installeren.

```bash
$ npm install cookie-parser
```

Vervolgens moet de middleware ingeladen worden en moet er tegen de app verteld worden dat het gebruik moet maken van de cookie parser.

```jsx
const cookieParser = require('cookie-parser');
app.use(cookieParser());
```

### Cookie aanmaken

Met `res.cookie('name', 'value')` kan men een cookie aanmaken.

```jsx
app.get('/', function (req, res) {
  res.cookie('name', 'firstCookie');
  res.render('home');
});
```

Om te controleren of de cookie is aangemaakt kan men in de Chrome devtools kijken. Ga naar de tab application en klik onder storage op cookies. Hier zul je de cookie vinden.

Een andere manier om te bekijken of de cookie juist is aangemaakt kunnen we doen door middel van de console van de browser. Typ `console.log(document.cookie)` in de console en je zult de cookie zien.

Om de cookie te loggen op de server kan men gebruik maken van `req.cookies`.

```jsx
app.get('/', function (req, res) {
  console.log('Cookies: ', req.cookies);
  res.cookie('name', 'firstCookie');
  res.render('home');
});
```

### Aflopende cookie maken

Men kan een cookie een bepaalde aflooptijd meegeven. Dit kan met `expire` of met `maxAge`. De tijd wordt genoteerd in milliseconden.

```jsx
// Verloopt na 360000 ms vanaf de tijd dat de cookie is aangemaakt.
res.cookie(name, 'value', { expire: 360000 + Date.now() });

// Verloopt na 360000 ms vanaf de tijd dat de cookie is aangemaakt.
res.cookie(name, 'value', { maxAge: 360000 });
```

### Cookie verwijderen

Om een cookie te verwijderen gebruikt men `res.clearCookie()`. Tussen de haakjes gaat de naam van de cookie die verwijderd moet worden.

```jsx
res.clearCookie('name');
```

## Express-session middleware

### Setup

Net zoals bij de cookie parser moet men eerst de sessie middleware installeren en inladen. Versies voor 1.5.0 had deze middleware ook de cookie parser middleware nodig, maar vanaf versie 1.5.0 werkt deze middleware ook zonder. Het is dus niet nodig om ook de cookie parser middleware te installeren.

```bash
$ npm install --save express-session
```

```jsx
const session = require('express-session');
```

### Options

**Server-side session storage**

De default server-side sessie opslag is `MemoryStore`. MemoryStore is specifiek niet ontworpen om te gebruiken in een productie omgeving. Het type opslag moet gewijzigd worden, als men naar productie gaat.

```jsx
app.use(
  session({
    secret: 'Dit is geheim!',
    resave: false,
    saveUninitialized: true,
    cookie: { secure: true },
  })
);
```

**secret**

Deze `string` moet men veranderen. De `string` wordt gebruikt om de hash te berekenen. Zonder deze `string` krijgt men geen toegang tot de sessie.

**resave**

Dwingt de sessie om terug opgeslagen te worden in de session store. Ook als de sessie nooit is gewijzigd. Meestal wil je hier `false` invullen.

Om te kijken welke nodig is voor het type opslag je gebruikt is door te controleren of het type opslag die gebruikt wordt de `touch` methode implementeert. Als dit gebeurt dan is het `resave: false`, als dit niet gebeurd is het `resave: true`.

`resave` is niet verplicht om aan te geven, maar de default waarde is verouderd. De default waarde zal gewijzigd worden in de toekomst, hierdoor is het aan te raden om deze momenteel wel aan te geven.

**saveUninitialized**

Dwingt een sessie met de status `uninitialized` om opgeslagen te worden. Een sessie is uninitialized als de sessie nieuw is maar niet gewijzigd is.

`saveUninitialized` is niet verplicht om aan te geven, maar de default waarde is verouderd. De default waarde zal gewijzigd worden in de toekomst, hierdoor is het aan te raden om deze momenteel wel aan te geven.

**cookie.secure**

Achter `cookie.secure` komt een `boolean`. Met de waarde `true` wordt de cookie beveiligd. De default waarde is `false`, het wordt echter aangeraden om deze op `true` te zetten. Dit omdat sommige clients in de toekomst een cookie niet terug zullen sturen als de browser geen HTTPS connectie heeft. Dit betekent wel dat een HTTPS website nodig is.

**cookie.maxAge**

Specificeert een getal in milliseconden wanneer de cookie verloopt.

Zie [de express sessions documentatie](http://expressjs.com/en/resources/middleware/session.html) voor alle attributen.

### Voorbeeld: pagina teller

Onderstaand voorbeeld is een simpele express app. Deze app telt - door middel van cookies - hoevaak een client de pagina aanroept.

```jsx
const express = require('express'),
  port = 3000,
  app = express(),
  session = require('express-session');

app.use(express.static('public')).use(
  session({
    secret: 'Dit is geheim',
    resave: false,
    saveUninitialized: true,
    cookie: { secure: false },
  })
);

app.get('/', function (req, res) {
  if (req.session.page_views) {
    req.session.page_views++;
    res.send('Je hebt deze pagina ' + req.session.page_views + ' keer bezocht');
  } else {
    req.session.page_views = 1;
    res.send('Welkom op deze pagina voor de eerste keer!');
  }
});

const server = app.listen(port, () =>
  console.log(`App listening on port ${port}`)
);
```

Als een client de pagina voor het eerst opvraagt, is er nog geen sessie cookie. Deze wordt aangemaakt en opgeslagen op de server. De client zal `Welkom op deze pagina voor de eerste keer!` zien in de browser. Wanneer dezelfde client de pagina herlaadt, is er wél een cookie met zijn sessie ID op de server. Hierdoor zal de client nu `Je hebt deze pagina x keer bezocht` zien.

## Bronnen

[How Internet Cookies Work](https://computer.howstuffworks.com/cookie1.htm)

[The Ultimate Guide to Cookies](https://html.com/resources/cookies-ultimate-guide/#Understanding_Cookies_A_Developer8217s_Guide)

[4.x API](https://expressjs.com/en/api.html)

[cookie-parser](https://www.npmjs.com/package/cookie-parser)

[ExpressJS - Cookies](https://www.tutorialspoint.com/expressjs/expressjs_cookies.htm)

[Express session middleware](http://expressjs.com/en/resources/middleware/session.html)

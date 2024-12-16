# Blazor

Blazor gør det muligt at udvikle moderne, interaktive brugerflader direkte i C#. En stor fordel er, at Blazor også understøtter JavaScript, hvilket åbner op for brug af JavaScript-biblioteker og man kan skrive kode i Java Script.

Introduktionen af Blazor Web App i .NET 8.0 har gjort det muligt at mixe forskellige render modes i Blazor, til forskel for
.NET 7.0 hvor man kun kunne vælge imellem Blazor Server eller Blazor WebAssembly for sin applikation.

Man kan sætte rendermode til for hele applikationen og ikke tænke mere over det eller man kan sætte en rendermode for hver individuel komponent.

Det giver mere fleksibilitet, men også en højere kompleksitet, da man har UI komponenter i både et Client og Server projekt at holde styr på. Hvilken render mode man kan vælge og hvorfor, kigger jeg nærmere på herunder.

## Blazor Varianter

# [Interactive Server](#tab/interactive-server)

**Sådan fungerer det**  
Serveren sender kun det det indhold der skal bruges, når en bruger navigerer til en side, og hvis brugeren har adgang til siden.
Dataen bliver sendt via en SignalR-forbindelse, der muliggør realtidskommunikation mellem serveren og klienten gennem websockets (eller alternative protokoller som Server-Sent Events eller Long Polling, hvis websockets ikke er tilgængelige).

**Interaktivitet**  
Brugeraktivitet behandles direkte på serveren af ASP.NET Core runtime.

**Stateful (tilstandsfuld)**  
Applikationens tilstand er afhængig af en konstant forbindelse til serveren.
Serveren håndterer forbindelsen for alle forbundne klienter og opdatering af UI.

**Fordele**  

- Hurtig initial indlæsning da alt bruger aktivitet på siden forgår direkte via serveren.
  Skal kun downloade Java Script, HTML og CSS per side.
  Uanset hvor stor applikationen er, vil den loade hurtigt.
- Lavere krav til klientens ydeevne, da alt beregnes på serveren.
- Man behøver kun 1 server projekt.
- Organisering af kode er enkelt da det er en monolith (med mindre at man bruger micro services).

**Ulemper**  

- Netværk:  
  Afhængighed af en stabil netværksforbindelse. Hvis forbindelsen afbrydes, mister applikationen sin funktionalitet.
- Omkostninger:
  Høj belastning på serveren, især ved mange samtidige brugere eller meget interaktivitet.
- Udsving i netværksforbindelsen kan påvirke brugeroplevelsen negativt.

**Sikkerhed**  
Da hele applikationen ligger på serveren, kan man sikre at det kun er brugere med de rette brugsrettigheder, der har adgang til specifike komponenter, sider eller layouts.
Man kan bruge en ViewModel som man binder til en komponent, og validere modelen med eksemspelvis [dataannotations](https://medium.com/@ravipatel.it/comprehensive-guide-to-data-annotations-in-net-8-a-beginner-friendly-approach-e6d6c8a8588f) eller [FluentValidation](https://docs.fluentvalidation.net/en/latest/).
Det giver brugeren direkte feedback i klienten, men validering i klienten kan manipuleres, eksempelvis via [Dev Tools](https://developer.chrome.com/docs/devtools), hvis klienten er en browser.
Da man allerede har validering for ViewModelen, kan man bruge den samme i en service eller metode der kører på severen, som en bruger ikke har direkte adgang til.

# [Interactive WebAssembly](#tab/interactive-webassembly)

**Sådan fungerer det**  
.NET runtime og app bundle downloades og lagres i browserens cache, ved første indlæsning en komponent.
Komponenter skal bygges fra et separat client projekt, der opsætter Blazor WebAssembly-værten.
Skal der sendes eller modtages data til eller fra en server, foregår det via en API, typisk via HTTPS.

**Interaktivitet**  
Brugeraktivitet håndteres direkte i klientens browser.

**Stateless (tilstandsløs)**  
Den håndterer tilstanden af applikationen i browseren, uafhængig af serveren, da alt er lagret i browseren.

**Fordele**  

- Kan bruges offline, efter at den er downloaded, da klient delen ligger, der er catched i browseren, så den kan stadig fungere, selv hvis netværksforbindelsen svigter efterfølgende.
- Hurtig UI-reaktionstid, da al interaktivitet udføres lokalt i browseren uden afhængighed af netværksforbindelsen,
 og man kan opnå en hastighed der er det tæt på desktop app hastighed.
- Klient- og serverprojekt kan udvikles og skaleres separat.
- Server projektet (API) kan bruges til forskellige klienter.
- Man kan bruge den samme C# kode for validering i både Klient og Serber projektet.

**Ulemper**  

- Længere initial indlæsningstid da hele klientprojektet skal downloades før at en bruger kan interigere med applikationen.
- Kræver en mere kraftfuld klientenhed, da al interaktion foregår lokalt i browseren.
- Er afhængig af en et .NET serverprojekt for at kunne fungere, da det er serverprojektet der 'serverer' klientprojekt til browseren

**Sikkerhed**
I en WebAssembly-applikation downloades og caches alle komponenter i browserens cache, hvilket giver brugeren adgang til hele klientprojektet via værktøjer som browserens Developer Tools.
Validering skal derfor altid foretages på backenden (serveren) for at sikre dataintegritet og forhindre misbrug.
For eksempel kan man ved at højreklikke på et website, vælge Inspicér, og navigere til fanen Application finde cachelagrede filer under Cache Storage. Intet i klienten er skjult eller sikkert fra brugere med adgang til Developer Tools.

UI-komponenterne i Blazor WebAssembly tilbyder ikke reel sikkerhed mod uautoriserede brugere, da eventuelle adgangsbegrænsninger i UI’et kun er kosmetiske.
Det er derfor afgørende at beskytte API'en og sikre korrekt adgangskontrol og validering på serveren frem for at stole på UI-komponenternes sikkerhed.

# [Interactive Auto](#tab/interactive-auto)

**Sådan fungerer det**  
Fungerer som Blazor WebAssembly, men med den vigtige forskel, at .NET runtime kører på serveren, indtil hele applikationen er downloadet og cached i brugerens browser. Dette giver en hurtigere "first render" for brugeren og en mere glidende overgang til en fuldt klientbaseret applikation.

**Fordele**  

- Brugeren kan se og interagere med UI'et næsten med det samme, fordi serveren midlertidigt håndterer logik og rendering. Dette skaber en hurtig og responsiv oplevelse, selv for brugere med langsom internetforbindelse, da serveren kun skal håndtere den specifike side som brugeren har navigeret til.

- Når applikationen er fuldt downloadet og cached, skifter den automatisk til at køre som en Blazor WebAssembly-app, hvilket reducerer serverbelastningen og udnytter klientens ressourcer optimalt.
Brugeren oplever derfor ikke en tom side eller en langsom load-fase, da serveren straks leverer UI.

**Ulemper**  

- Brugeren kan se UI'et hurtigt, men de kan ikke sende eller modtage data fra API'en, før .NET runtime er fuldt downloadet og kører lokalt i browseren. Dette kan give en begrænset interaktivitet i starten.
- Overgangen fra server til klient kan skabe potentielle problemer, hvis noget går galt under download eller caching-processen.

# [Blazor Standalone](#tab/blazor-standalone)

**Sådan fungerer det**  
Blazor Standalone bygger på samme klient-side rendering (CSR)-principper som Blazor WebAssembly, men adskiller sig ved ikke at være afhængig af en .NET-server til hosting. Den genererer en samling statiske filer som HTML, CSS og JavaScript, som kan hostes på enhver server, der understøtter levering af statiske filer. Dette gør den særligt velegnet til enklere hostingløsninger, mens forretningslogik og databaseadgang håndteres via en ekstern API.

Blazor Standalone deler mange af de samme fordele og ulemper med Blazor WebAssembly: Hurtig UI-reaktionstid, offline-funktionalitet og genbrug af C#-kode. Samtidig kræver begge løsninger, at sikkerhed og validering implementeres på serversiden. Den væsentligste forskel ligger i Blazor Standalones større hostingfleksibilitet og dens mangel på indbygget Microsoft Identity-support.

**Fordele**  

- Kan hostes på enhver server, der understøtter statiske filer, inklusiv billigere løsninger som GitHub Pages eller Azure Static Web Apps.
- Kræver ikke en .NET-server til at levere klientprojektet, hvilket kan reducere kompleksiteten i drift.
- Hurtig UI, offline-funktionalitet og genbrug af C#-kode gælder også for Blazor Standalone.

**Ulemper**  

- Blazor Standalone-templaten inkluderer ikke en indbygget løsning med Microsoft Identity til authentication. Udviklere skal selv implementere sikkerhed, ofte ved brug af tredjepartsløsninger eller en API, der sikrer kommunikationen.
- For at sikre forbindelsen mellem Blazor Standalone og en API anbefales det ofte at bruge OpenID Connect (OIDC) med en Identity Provider (IP). Dette kan øge kompleksiteten og afhængigheden af tredjepartsudbydere.
- Dokumentation om emner som sikkerhed og cookiebaseret authentication i Blazor Standalone er sparsom. Dette kan gøre det udfordrende at implementere robuste løsninger uden ekstern hjælp. Manglen af dokumentation gælder også authentication for OIDC udbydere som OAuth, da Blazor Standalone er ikke lige så udbredt som andre frameworks som React, Angular eller Vue.

# [+ PWA](#tab/progressive-web-app)

**Sådan fungerer det**  
En Progressive Web App (PWA) er en webapplikation, der kan downloades og lagres i browserens cache. Dette gælder for Interactive WebAssembly, Interactive Auto og Standalone applikationer. Efter download kan applikationen installeres direkte på klientens enhed.

Ordet "Progressive" refererer til, at en bruger starter med at opdage applikationen via browseren og derefter kan fortsætte med at installere den som en app på sin enhed.

**Fordele**  

- Kan køre offline og starte med det samme, uafhængigt af netværksforbindelse, hvis applikationen er downloadet.
- Kan køre i sit eget vindue, uafhængigt af browseren.
- Kan startes fra brugerens operativsystems startmenu eller skrivebord med et dedikeret ikon.
Understøtter push-notifikationer og kan håndtere baggrundsopgaver via en service worker, selv når applikationen ikke aktivt bruges.
- Kan bruges på alle enheder med en browser, herunder desktop, laptop, tablet og mobiltelefon.
- Kan downloades fra platforme som App Store eller Microsoft Store.
- Bygger bro mellem web og native apps ved at udnytte standard webteknologier til funktioner, der tidligere krævede enhedsspecifikke programmeringssprog. Dog er der stadig begrænsninger, afhængigt af brugerens browser og enhed. For et overblik over, hvad der understøttes i forskellige browsere, kan man besøge [What Web Can Do Today](https://whatwebcando.today/). Denne liste opdateres løbende, da browser- og OS-udviklere konstant udvider adgangen til native funktioner. Funktioner som filsystemadgang og hardwarekonfiguration vil dog sandsynligvis forblive sandboxed af sikkerhedsmæssige årsager.

**Ulemper**
- I Blazor Standalone-templaten kan man tilvælge PWA, men dette gælder ikke for Blazor Web App Interactive WebAssembly-templaten, hvor PWA-funktionaliteten skal tilføjes manuelt.
Applikationen kan kun testes manuelt i offline-tilstand efter deployment.
- Opdateringer er kun tilgængelige, hvis brugeren lukker applikationen helt. En simpel opdatering af browseren er ikke tilstrækkelig. Dette skal overvejes af udviklere og kommunikeres klart til brugerne.
- Hvis en opdatering udrulles, som ikke er kompatibel med tidligere versioner, kan brugeren risikere at miste adgang til applikationen uden at forstå hvorfor. Det er afgørende at kommunikere opdateringer tydeligt (noget som eksempelvis NemID ikke prioriterede – rant out!).
- Hvis brugeren ikke har netværksforbindelse, er det ikke muligt at få en ny access token til autentificering. Dette forhindrer adgang til beskyttede dele af applikationen. Det afhænger dog af, om access tokens har en udløbstid, hvilket generelt anbefales.

---

## Omkostninger

##### Udvikling

- **Blazor Server**  
Da koden er samlet i ét projekt, er det hurtigt og nemt at implementere nye funktioner. Kodebasen er enkel og gør det muligt at udvikle direkte i en enkelt komponent uden at skulle opdele koden i separate klasser, hvilket reducerer kompleksitet og sparer tid.

- **Blazor WebAssembly**  
Applikationen kræver opdeling i to projekter: frontend og backend. Dette betyder ekstra opsætning og behov for et delt bibliotek til DTO'er (Data Transfer Objects) for at sikre korrekt dataoverførsel mellem frontend og backend. Det kan tage længere tid at strukturere og udvikle funktioner sammenlignet med Blazor Server.

- **Blazor Standalone**  
Opsætning af sikkerhed og authentication kræver betydelig indsats og erfaring, især hvis der bruges tredjepartsbiblioteker eller custom integrationer. Dette kan føre til længere udviklingstid sammenlignet med de andre løsninger.

##### Vedligehold

- **Blazor Server**  
Den simple struktur gør det nemt at organisere og overskue kodebasen, hvilket gør vedligeholdelse relativt enkel. Nye udviklere kan hurtigt forstå applikationens opbygning.

- **Blazor WebAssembly**  
Vedligeholdelse kan være mere kompleks, da frontend og backend skal holdes synkroniseret, især ved ændringer i API'et eller DTO'er. Et delt bibliotek kan reducere risikoen for fejl, men det introducerer en afhængighed, der kræver løbende opdatering.

- **Blazor Auto**  
Samme som WebAssembly

- **Blazor Standalone**  
Authentication og sikkerhedsløsninger er ofte brugerdefinerede og kræver løbende tilpasninger, især når tredjepartsbiblioteker ændrer standarder. Dette kan gøre vedligeholdelse mere tidskrævende end de andre modeller.

##### Drift

- **Blazor Server**  
Hele applikationen kører på serveren, hvilket kan føre til betydelige omkostninger ved høj brugeraktivitet. Serveren skal håndtere både forretningslogik og UI-opdateringer, hvilket kræver kraftigere serverressourcer og begrænser fleksibiliteten i hosting.

- **Blazor WebAssembly**  
Klienten kører direkte i brugerens browser, hvilket reducerer belastningen på serveren. Dette resulterer ofte i lavere serveromkostninger, især ved mange samtidige brugere, da backenden kun håndterer API-kald. Ressourcer caches i browseren, hvilket reducerer behovet for gentagne downloads.

- **Blazor Standalone**  
Hostingomkostningerne er typisk lave, da applikationen kun består af statiske filer. Dette gør Standalone til en økonomisk løsning, hvis der ikke er behov for en server. Hvis der kræves en backend til forretningslogik via API, kan driftsomkostningerne stige og gøre løsningen mindre fordelagtig sammenlignet med andre modeller.

- **Blazor Auto**  
Interactive Auto har stort set samme omkostninger som Blazor WebAssembly, da serveraktiviteten kun er midlertidig, indtil applikationen er downloadet og kører som WebAssembly. Hostingomkostningerne kan være en anelse højere i applikationens opstartsfase, men dette er typisk ubetydeligt, da brugeren hurtigt overgår til en klientbaseret oplevelse.

## Omkostninger PWA

Da PWA skilder sig ud ved at være en tilføjelse til applikationen har den sit eget afsnit.

**Udvikling**  
Blazor Standalone:
Tilføjelsen af PWA-funktionalitet kræver en konfiguration af en service worker og en manifestfil. Det er en relativt simpel proces, især med indbyggede templates, hvilket gør det hurtigt at implementere PWA i applikationen.

Blazor WebAssembly:
PWA skal konfigureres manuelt, da standard WebAssembly-template ikke inkluderer PWA. Dette kræver mere tid og erfaring med service workers og caching-strategier, hvilket kan øge udviklingstiden.

**Vedligehold**  
PWA kræver, at service workers holdes opdateret for at sikre, at applikationen fungerer korrekt, især ved ændringer i kodebasen. Derudover skal versionering håndteres effektivt for at undgå problemer med kompatibilitet mellem applikationsversioner.

Opdateringshåndtering kan også være en udfordring, da applikationen kun vil blive opdateret, når brugeren lukker og genstarter den, hvilket kræver opmærksomhed og kommunikation fra udviklerens side.

**Drift**  
Driftsomkostningerne for PWA er i vid udstrækning de samme som Interactive WebAssembly, Interactive auto eller Standalone, da store dele af applikationen caches og kører lokalt i brugerens browser.

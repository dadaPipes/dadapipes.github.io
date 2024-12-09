# Blazor Varianter

## Overvejelser og Sammenligning af Blazor rammer

Jeg har undersøgt og sammenlignet de forskellige relevante Blazor varianter, for web udvikling, i .NET 8.0

### Blazor Web App

Introduktionen af Blazor Web App i .NET 8.0 har gjort det muligt at mixe forskellige render modes i Blazor, til forskel for
.NET 7.0 hvor man kun kunne vælge imellem Blazor Server og Blazor WebAssembly.

Man kan sætte rendermode for hele applikationen og ikke tænke mere over det eller man kan sætte en rendermode for hver individuel komponent, så man ikke er tvunget til at bruge enten server rendering eller webassembly rendering for hele applikationen.

På den ene side giver det mere fleksibilitet, men på den anden side giver det mere kopmleksitet, hvilket jeg dækker senere.
I nogle tilfælde kan det give mening at have forskelig rendermode, men for større dele af applikationen, som en epic (en gruppering af features, eller for en gruppe af epics der er relaterede).
(TODO: INDSÆT INTERNT LINK TIL DER HVOR JEG BESKRIVER FEATURES OG HVORDAN MAN KAN ORGANISERE KODEN DYNAMISK, UD FRA KRAVENE, I TAKT MED AT APPLIKATIONEN VOKSER).

Begge har SPA funktionallitet, så de kan opdatere enkelte komponenter på en side uden at lave en fuld page refresh.

# [Interactive Server](#tab/interactive-server)

**Sådan fungerer det**  
Klienten viser kun det indhold som skal bruges, og bliver sendt via en SignalR-forbindelse, der muliggør realtidskommunikation mellem serveren og klienten gennem websockets (eller alternative protokoller som Server-Sent Events eller Long Polling, hvis websockets ikke er tilgængelige).

**Interaktivitet**  
Brugeraktivitet behandles direkte på serveren af ASP.NET Core runtime.

**Stateful (tilstandsfuld):**
Applikationens tilstand er afhængig af serveren og håndterer forbindelsen for alle forbundne klienter og opdatering af UI.

**Fordele:**  

- Hurtig initial indlæsning da alt bruger aktivitet på siden forgår direkte via serveren.
  Skal kun downloade Java Script bundle displaye den første side inklusiv HTML og CSS for den første side.
  Uanset hvor stor applikationen er, vil den loade hurtigt.
- Lavere krav til klientens ydeevne, da alt beregnes på serveren.
- Man behøver kun 1 projekt da alt kører på serveren.
- Koden er simplere og man er ikke tvunget til at have et API lag.

**Ulemper:**  

- Netværk:  
  Afhængighed af en stabil netværksforbindelse. Hvis forbindelsen afbrydes, mister applikationen sin funktionalitet.
- Omkostninger:
  Høj belastning på serveren, hvilket kan resultere i øgede omkostninger, især ved mange samtidige brugere eller meget aktivitet.
- Udsving i netværksforbindelsen kan påvirke brugeroplevelsen negativt.

**Sikkerhed**  
Da hele applikationen ligger på serveren kan man sikre at det kun er brugere med de rette brugsrettigheder der har adgang til specifike begrænsede komponenter.
Derfor(!) kan man sikre følsom kode direkte i en komponent (UI). ( Hård nød er knækket.. ).

Hele koden holdes sikker på serveren og komponenter kan kun tilgås hvis man har brugerrettigheder til at tilgå dem.
Validering af data kan derfor foregå direkte i UI, da det kun er autoriserede brugere der har adgang til serveren.
Man skal selvfølgelig sørge for at de kun kan sende og modtage data der er relevante for dem, så de ikke kan fucke med hele systemet, men kun hvad der vedrører dem selv.

# [Interactive WebAssembly](#tab/interactive-webassembly)

**Sådan fungerer det**  
Hele frontend delen bliver downloaded og cached i klientens browser, inclusiv .NET WebAssembly runtime.
UI og brugeraktivitet håndteres direkte i browseren. Ved første indlæsning af en komponent downloades hele .NET runtime sammen med applikationen til browseren og caches, hvor koden eksekveres i WebAssembly-format.
API-kald og andre interaktioner med serveren håndteres via http(s) eller andre forbindelser som websockets.

**Interaktivitet**  
Brugeraktivitet håndteres i browseren på klienten via den WebAssembly baserede Blazor runtime.

**Stateless (tilstandsløs):**
Den håndterer tilstanden af applikationen i browseren, uafhængig af serveren, da alt er lagret i browseren.

**Fordele**  
Kan bruges offline, efter at den er downloaded, da frontend delen ligger i browseren, derfor kan den stadig fungere, selv hvis netværksforbindelsen svigter efterfølgende.
Hurtig UI-reaktionstid, da al interaktivitet udføres lokalt i browseren uden afhængighed af netværksforbindelsen,
kan man opnå en hastighed der er det tæt på desktop app hastighed.

Bedre skalerbarhed, da klient og server kan udvikles og skaleres separat.
Nemmere vedligehold i tilfælde af en omfattende applikation, da kommunikationen imellem front- og backend foregår via HTTTP(S), er der en meget løs kobling og to udviklere kan arbejde i hver deres ende, uden at påvirke hinanden.
Server projektet kan bruges til forskellige klienter.

**Ulemper**
- Længere initial indlæsningstid da hele frontend delen skal downloades før at en bruger kan interiger med appen.
- Kræver en mere kraftfuld klientenhed, da al interaktion foregår lokalt i browseren.
- Er afhængig af en .NET backend for at kunne fungere, og skal ligge på den samme server, da det er backend der 'serverer' frontenden delen til browseren.
- Øget kompleksitet:
Kræver et seperat server projekt i form af en API og typisk også et delt projekt bibliotek der har objekter til at transportere data imellem front- og backend.
Implimenterer CORS for at begrænse hvilke domæner der har adgang til API’en.

**Sikkerhed**
Validering i UI er ikke beskyttet da det kan manipuleres direkte i browseren. Oven i hatten, så er alle UI komponenter downloaded og catched i browseren og en bruger har derfor adgang til alle dele af frontend delen.
Validering SKAL derfor sikres på backend delen (serveren) for at være sikker.

Du må ikke sige det til nogen.. men hvis man højreklikker på et website, trykker inspect, klikker på 'application' knappen, kan man finde filerne under 'cache storage'. Ingenting er gemt eller glemt i [Dev tools](https://developer.chrome.com/docs/devtools).

De forskellige dele af UI er derfor **IKKE** sikret imod brugere uden de rigtige retigheder. Den begrænsede adgang man kan tilføje i Blazor er rent kosmetisk.
Derfor er man nødt til at sikre sin API og det er ikke nok bare at sikre sin UI komponenter.

# [Interactive Auto](#tab/interactive-auto)

Fungerer på samme måde som Blazor WebAssembly, men bruger .NET runtime på serveren indtil at hele applikationen er downloaded og catched i browseren.

# [Blazor Standalone](#tab/blazor-standalone)

Blazor Standalone er en bunke statiske filer som HTML CSS of Java Script.
Den har den de samme fordele og ulemper som Blazor Standalone, men adskilder sig stadig på nogle punkter.

Den er server uafhængig.
Den er uafhængig af .NET og kan hostes på en hvilken som helst server der kan sende filerne til borowseren.
Skal man sikre foretningslogik eller databaseadgang, skal den kommunikere med en API.

**Fordele**  
Blazor Standalone templated understøtter Progressive Web App, som det eneste template.

**Ulemper**  
Det er et sandt mareridt at sikre forbindelsen mellem Standalone og en API.
Den officielle dokumentation opfordrer til at man bruger OpenID Connect (OIDC) Identity Provider (IP), som er et tredje parts bibliotek der håndterer tokens uden for applikationen.
Den typiske brugssag er Social Media login, så man kan logge ind via Google, Facebook, X, Twitter etc.
Problemet er at mange Auth leverandører ikke har lavet noget til Blazor Standalone så man hurtigt kan spinde Authentication op.
En mulig årsag er at Blazor som statiske filer, ikke er nært så populært som React, Angular, Vue.

Det er **IKKE** en mulighed at vælge authentication i Blazor Standalone templated, så man er overladt til sig selv.
Der findes meget sparsom dokumentation om hvordan man sikrer Blazor Standalone med cookies, hvilket er det rekomenderede, da browseren håndterer cookies, uden at eksponere dem for Java Script.

Idioten her fattede ikke en skid af alt det, før efter at have brugt 4 uger på at sikre Blazor Standalone med cookies, hvilket i den grad øgede kompleksiteten i frontend delen.
Til gengæld er jeg fri for at være afhægig af en tedje part udbyder, og kan sikre applicationen via Microsoft Identity endpoints eller lave mine egne ( hvis ikke at jeg har brug for en lang række endpoints jeg ikke skal bruge).

# [Progressive Web App](#tab/progressive-web-app)

Hele applikationen kan downloades til en klient.
Ordet ’Progressive’ kommer af at en bruger starter med at opdage applikationen, som kører i en browser, og derefter kan progress/fortsætte med at installerer applikationen på sin enhed.

**Fordele**  
- Kan kører offline og med det samme, uafhængig af netværksforbindelse, hvis applikationen er downloaded. 
- Kan kører i sit eget vindue og ikke kun et browser vindue.
- Startes fra en brugers Operations Systems start menu eller skrivebord.
- Modtage push notifikationer og håndtere baggrunds tasks via en serviceworker, selv hvis en bruger ikke bruger applikationen.
- Kan bruges alle klienter der har en browser, som desktop, laptop, tablet og mobiltelefon.
- Kan downloades fra App Store.
- Har sit eget icon.

**Ulemper**
- Offline support er kun tilgængelig hvis applikationen er udrullet. Man kan kun manuelt teste applikationen i offline tilstand efter at den er udrullet.
- Updateringer er kun tilgængelige hvis en bruger lukker helt ned for applikationen. Det er ikke nok, kun at refreshe browseren. Som udvikler skal man være opmærksom på det og det skal kommunikeres til brugerne.
Hvis der udrulles en opdatering som ikke kan bruges med en tidligere version, er der risiko for at en bruger ikke kan bruge applikationen og samtidigt ikke har nogen som helst ide om hvorfor at den ikke kan bruges ( noget som folkene bag NemID ikke havde prioriteret som et essentielt krav. Rant out ).
- Hvis en bruger ikke har netværksforbindelse, kan man ikke authentikeres og få en access token, og har derved ikke adgang til dele af applikationen som kræver authentikering.

**Begrænsninger**  
Den har **IKKE** adgang til klientens Operations System, da den kører i browserens sandbox, så den kan ikke gøre brug af eksempelvis kammera på en telefon.
Til gengæld gør det den mere sikker, da den ikke kan manipulere, opsnappe eller plante ting i klienten.

---

## Læringsmål

### Viden (Huske og Forstå)

**Jeg kan identificere de forskellige Blazor rendermodes**  
Jeg har viden om de forskellige Blazor rendermodes, herunder Blazor Server, Blazor WebAssembly og Blazor Web App med Interactive Auto.

**Jeg kan beskrive forskellene mellem Blazor Server og Blazor WebAssembly**  
Jeg forstår de grundlæggende forskelle i arkitektur og performance, som adskiller Blazor Server og Blazor WebAssembly.

**Jeg kan forklare anvendelsen af Blazor rendermodes i webapplikationer**  
Jeg har viden om, hvordan Blazor rendermodes anvendes i forhold til specifikke webapplikationers krav til skalering, hastighed og brugeroplevelse.

### Færdigheder (Anvende og Analysere)

**Jeg kan anvende Blazor rendermodes i udvikling af en webapplikation**
Jeg er i stand til at vælge og implementere den rette Blazor rendermode baseret på applikationens krav og miljøforhold.

**Jeg kan analysere, hvilken Blazor rendermode der er mest effektiv i et givet scenarie**  
Jeg kan vurdere behovene for en applikation og analysere, om Blazor Server eller Blazor WebAssembly vil være mest effektiv i forhold til performance, sikkerhed og netværkskrav.

**Jeg kan optimere en Blazor-applikation ved at vælge passende rendermode**  
Jeg er i stand til at vælge den rette Blazor rendermode for at optimere både performance og brugeroplevelse i en webapplikation.

### Kompetencer (Evaluere og Skabe)

**Jeg kan evaluere de tekniske og praktiske konsekvenser af valget af Blazor rendermode**  
Jeg kan vurdere, hvordan valget af Blazor rendermode påvirker faktorer som sikkerhed, netværksafhængighed og applikationens skalerbarhed.

**Jeg kan skabe en webapplikation, der kombinerer Blazor Server og Blazor WebAssembly på en hensigtsmæssig måde**  
Jeg er i stand til at kombinere forskellige Blazor rendermodes i én applikation for at opnå optimal performance og funktionalitet, afhængigt af de specifikke krav.

**Jeg kan kombinere Blazor rendermodes med designmønstre for at opnå et specifikt mål**  
Jeg kan integrere Blazor rendermodes med passende designmønstre (fx client-side og server-side separation) for at opnå applikationens mål om fleksibilitet, effektivitet og vedligeholdelse.

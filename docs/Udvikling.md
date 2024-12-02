# Udviklings metoder

TODO: opdtater: Ser man bort fra projekthåndteringen, som jeg ikke dækker i denne blog, og fokuserer på udviklingsmetoden så .. ( hvad er det egentlig at jeg gerne vil have fokus på her ? )
En kort beskrivelse af nogle iterative systemudviklings metoder for at dække de relevante områder.

## Eksempler på udviklings metoder

# [Feature-Driven Development](#tab/fdd)

**Grundlæggende principper og tilgang**  
FDD er en model-drevet proces, hvor udviklingsarbejdet struktureres omkring små, veldokumenterede funktioner, der kan implementeres på få dage. Fokus ligger på at opretholde kontinuerlig fremdrift og at levere funktionalitet, der er reel og målbar.

**De 5 processer i FDD**
FDD følger fem nøgleprocesser:

1. Udarbejdelse af en overordnet model (**Develop an Overall Model**):
Projektet starter med en forståelse af domænet og en model, som beskriver systemets kernestruktur og -adfærd. Dette involverer workshops og samarbejde mellem udviklere og domæneeksperter.

2. Udarbejdelse af en funktionel liste (**Build a Features List**):
Udviklerne opdeler systemet i mindre dele, kaldet funktioner. Funktionerne beskrives som "handlinger udført på et resultat for en given kunde" og organiseres i områder (business domains).

3. Planlægning baseret på funktioner (**Plan by Feature**):
Funktionerne prioriteres og grupperes i udviklingsmilepæle. Denne proces skaber en detaljeret plan, som guider det iterative arbejde.

4. Design af funktioner (**Design by Feature**):
Hver funktion designes af et lille team i tæt samarbejde, hvor designet sikrer, at funktionen kan implementeres på en struktureret måde.

5. Implementering af funktioner (**Build by Feature**):
Funktionerne kodes, testes og integreres i systemet. Denne proces gentages iterativt, indtil projektet er færdigt.

[Wikipedia](https://en.wikipedia.org/wiki/Feature-driven_development)

# [Test-Driven Development](#tab/tdd)

Test-Driven Development (TDD) er en softwareudviklingsmetodologi, hvor testskrivning er integreret som en central del af udviklingsprocessen. Det er en iterativ udviklingsmetode, der fokuserer på at skrive tests før implementering af funktionalitet.

**Hovedidéen bag TDD**  
TDD har som kerneprincip at skrive automatiserede tests for den ønskede funktionalitet, før selve implementeringen påbegyndes. Det hjælper med at sikre, at al kode, der skrives, opfylder kravene og fungerer korrekt.

Denne tilgang understøtter hurtigere fejlidentifikation og sikrer, at ny kode ikke bryder eksisterende funktionalitet.

**TDD-processen**
TDD følger en fast cyklus, kendt som Red-Green-Refactor:

- Red (skriv en test)
Start med at skrive en test, der beskriver en ny funktion eller en ændring. Testen skal fejle, fordi funktionaliteten endnu ikke er implementeret.

- Green (implementer funktionalitet):
Skriv netop nok kode til at få testen til at bestå. Dette sikrer, at udviklingen er fokuseret og minimal.

- Refactor (forbedr koden):
Optimer koden uden at ændre dens adfærd. Refactorering sikrer, at koden er nemmere at læse og vedligeholdelsesvenlig, mens alle tests stadig passerer.

Processen gentages for hver ny funktion eller ændring, hvilket sikrer en iterativ og kontrolleret udviklingsproces.

Levende dokumentation:
Tests fungerer som dokumentation for systemets funktionalitet og kan bruges til at forstå, hvad koden gør.

**Eksempel på TDD i praksis**
Lad os sige, at vi skal udvikle en funktion til at beregne summen af to tal:

**Red:**
Skriv en test, der kontrollerer, om Add(2, 3) returnerer 5. Testen fejler, fordi funktionen endnu ikke er implementeret.

**Green:**
Implementér en simpel version af funktionen:

```csharp
public int Add(int a, int b) 
{
    return a + b;
}
```

Testen består nu.

**Refactor:**
Overvej, om koden kan optimeres eller generaliseres.

[Wikipedia](https://en.wikipedia.org/wiki/Test-driven_development)

# [Behavior-Driven Development](#tab/bdd)

Behavior-Driven Development (BDD) er en softwareudviklingsmetodologi, der bygger videre på Test-Driven Development (TDD) og fokuserer på at fremme samarbejde mellem udviklere, testere og forretningsinteressenter. BDD er designet til at hjælpe teams med at forstå og definere, hvordan en applikation skal opføre sig i forskellige situationer.

**Hovedidéen bag BDD**  
BDD sigter mod at bygge en fælles forståelse af applikationens krav ved at beskrive dem i et simpelt og klart sprog, der er forståeligt for både tekniske og ikke-tekniske interessenter. Fokus er på at definere "adfærden" (behavior) af softwaren i forhold til forretningsværdi og brugeroplevelse.

Denne tilgang gør brug af specifikationer skrevet i naturligt sprog kombineret med eksekverbare tests, der fungerer som dokumentation for applikationens forventede adfærd.

**Nøgleprincipper i BDD**  

- Samarbejde mellem roller:
BDD fremmer samarbejde mellem udviklere, testere og forretningsinteressenter for at sikre, at alle har en fælles forståelse af kravene.

- Brug af naturligt sprog:
Krav beskrives ofte i et format som [Gherkin-syntaks](https://cucumber.io/docs/gherkin/), hvilket gør det lettere at dele og forstå.

- Eksekverbare specifikationer:
Testene fungerer både som kravspecifikation og dokumentation. De kører automatisk og bekræfter, at applikationen opfører sig som forventet.

- BDD's struktur: Given-When-Then
BDD bruger ofte et Given-When-Then mønster til at definere funktionaliteter og scenarier:

Given: En given kontekst eller forudsætning.
When: En bestemt handling eller hændelse.
Then: Den forventede adfærd eller resultat.
Eksempel:

```gherkin
Scenario: User logs in successfully
  Given the user is on the login page
  When they enter valid credentials
  Then they should be redirected to the dashboard
```

[Cucumber](https://cucumber.io/): Bruges til at skrive og køre Gherkin-scenarier.
[SpecFlow](https://docs.reqnroll.net/latest/index.html): En .NET-implementering af Cucumber.
Disse værktøjer integrerer med testautomatiseringsværktøjer og gør det muligt at køre scenarierne som testcases.

[Wikipedia](https://en.wikipedia.org/wiki/Behavior-driven_development#Story_versus_specification).

---

## Eksempler på brug af termer i forhold til Minimal API vs. Controllers

Når man beslutter, [hvilke termer der passer bedst i en given situation](https://www.mountaingoatsoftware.com/blog/stories-epics-and-themes), afhænger det af, om man arbejder med Controllers eller Minimal API, da strukturen og organiseringen af kode varierer.

# [Controller](#tab/controller)

Bruger man controllers i .NET, vil det være en ide at tænke over hvad der dækker en gruppe af endpoints (TODO: eksempel).
Bruger man Minimal API, hvor at et endpoint kan være i en fil for sig selv, kan man reflektere over hvilken term man bruger for 1 enkelt endpoint (TODO: eksempel).

**Eksempel: Brug af termer for grupperede endpoints**  
Theme (højere niveau):
"User Management" kan være temaet for alle endpoints, der håndterer brugere.

**Epic:**  
"Manage Users" kan være en overordnet epic, som beskriver flere funktioner såsom oprettelse, redigering, og sletning af brugere.

**Feature:**
En feature kan være "User Registration".

**Story:**
En specifik story kan være "As an admin, I want to register a new user with email and password."

**Konkrete eksempler på endpoints i en UserController:**
POST /users – Opret bruger (story for User Registration).
GET /users/{id} – Hent bruger.
PUT /users/{id} – Opdater bruger.
DELETE /users/{id} – Slet bruger.

Her vil en controller give mening som en gruppering for endpoints relateret til "User Management", og man kan bruge termer som feature eller story til at specificere formålet med hvert endpoint.

# [Minimal API](#tab/minimal-api)

Med Minimal API kan hvert endpoint implementeres uafhængigt, ofte i sin egen fil. Dette gør det muligt at tænke på hvert endpoint som en isoleret enhed, og terminologien kan tilpasses denne granularitet.

Eksempel: Brug af termer for enkeltstående endpoints
Feature:
Et enkelt endpoint kan ses som en feature i sig selv, fx "User Registration".

Story:
Et endpoint kan implementere en specifik story, fx "As an admin, I want to delete a user by ID."

Konkrete eksempler på Minimal API-endpoints i separate filer:

CreateUserEndpoint.cs:

```csharp
Copy code
app.MapPost("/users", (UserDto user) => { 
    // Logic for creating a user
}).WithTags("User Management");
Term: Feature/story → "User Registration."
```

DeleteUserEndpoint.cs:

```csharp
Copy code
app.MapDelete("/users/{id}", (int id) => {
    // Logic for deleting a user
}).WithTags("User Management");
Term: Story → "Delete a user by ID."
```

Med Minimal API kan man fokusere på mere granulære termer, da hvert endpoint ofte er implementeret for sig selv og ikke nødvendigvis grupperes naturligt som i controllers.

---

Valget af termer afhænger af, hvordan kodebasen er struktureret:

**Controller-tilgang**: Brug termer som theme og epic til at beskrive grupper af endpoints og feature eller story til at beskrive individuelle endpoints inden for en gruppe.

**Minimal API**: Brug mere detaljerede termer, hvor hvert endpoint kan beskrives som en feature eller story, da de ofte ikke grupperes på samme måde.

Det vigtigste er, at teamet er enige om terminologien og dens anvendelse.

Hvordan kagen skal skæres og med hvilke værktøjer er ikke hugget i sten. Det vigtigste er at man kender mulighederne så man kan bruge dem [som det passer bedst](https://medium.com/@davidrhyswhite/tdd-fdd-bdd-7f6c6cb9485b).  

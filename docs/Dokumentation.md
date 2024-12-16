# Dokumentation

[Swagger og alternativer](https://www.youtube.com/watch?v=gAUaRslamYs&t=0s)  
[OpenAPI](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/openapi/overview?view=aspnetcore-9.0)  
Der er forskel på hvordan at controllers, minimal API og Fastendpoints dokumenteres og hvilken information der genereres.
tjek op på conventions (FX: IActionResult vs ActionResult).

TODO: få noget med om sporbarhed. Målet er at kunne sikre sporbarhed mellem den fælles dokumentation som både kunde og udvikler har til rådighed og den implimenterede kode.
Ved at holde den fælles dokumentation på et højt niveau og dokumentere systemet som en sort box, kan kunde og udvikler få et hurtigt og solidt overblik over hvordan applikationen overordnet virker og hvordan enkelte dele fungerer.

**Hvad** giver mening at dokumentere, **hvorfor** og for **hvem** (PO og udvikler) ?

Acceptence tests for at PO og udviklere kan se hvad systemet kan, gør og begrænsninger i forskellige komponenter.

Delte objekter:
API: 
Utility:
Domæne modeller:
Globale objekter:
- Error response i API.
- andre globale objekter EKS (?)

summary, XML comments, DocFX, [GhostDoc](https://marketplace.visualstudio.com/items?itemName=sergeb.GhostDoc)

Automatiseret dokumentation er tidsbesparende, færre fejl og bliver opdateret automatisk.
API: DocFX statisk documentation og .NET .json der kan bruges til API tests i eksemplevis Swagger.

Ved at dokumentere Acceptence Tests med xunit.gherkin.quick og DocFX, giver det et overblik for både product owner og udviklere der arbejder op applikationen. Det er især værdifuldt for Product Owner, da de hurtigt kan få et overblik over **hvad** det at systemet gør, uden at kende til detaljerne omkring **hvordan** at det gør det.
Man sikrer sig også at dokumentationen altid er up to date samtidig med at man sikrer sporbarhed imellem kravene til applikationen og hvordan systemt virker.

Ved at dokumentere kravene til applikationen, bliver det nemmere for nye udviklere at sætte sig ind i hvad systemet gør.
Ved at dokumentere de dele af koden som man ved bliver brugt af forskellige andre dele af koden og som kan bruges når der laves nye features, så kan man nøjes med at kigge i dokumentationen frem for at grave ned i implementeringe af de forskellige objekter, for at finde ud af hvordan at de virker. Selve implementeringen at et objekt er ofte ikke så interesant.

Med en gennemført dokumentation af systemet og kodebasen får man et bedre overblik.

Acceptancetest og Gherkin-baserede user stories, som samtidig fungerer som dokumentation for kunden.

Organiseret på en måde, der afspejler applikationens struktur, og som gør det nemt for kunden at finde og forstå specifikke funktioner.

DocFX

- Features
- Acceptence tests
- Folder struktur (sporbarhed mellem dokumentation og kodebase)

Hvordan sikrer man en dækkende og overskuelig dokumentation som er nem at lave og vedligeholde ?
Fordele ved automatiseret dokumentation (forkert dokumentation er være end ingen. Mindsker fejl og afspejler koden.)

Inkluder kunden i dokumentationen.

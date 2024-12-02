# Dokumentation

Hvad giver mening at dokumentere og hvorfor ?

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

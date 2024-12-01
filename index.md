---
_layout: landing
---

# This is the **HOMEPAGE**

Refer to [Markdown](http://daringfireball.net/projects/markdown/) for how to write markdown files.

TODO: Kom ind på hvordan at det jeg skriver om handle om/relaterer til Blazor og .NET ( vigtigt da jeg berører nærmest samtlige fokusområder, så hvert fokusområde er nødt til at omhandle .NET på en eller anden måde ).

Nogle af de mest diskuterrede emner i forskellige online forumer, inden for .NET, er hvilken arkitektur og hvilke endpoints man skal bruge. Ofte er argumenterne one-liners som "Clean Architectur is over engineering", "DbContext is a Repository", "Only test your endpoints" osv., og man kan undre sig over, om dem som kommenterer, virkelig har en forståelse og erfaring inden for de forskellige (dele/områder/værktøjer), eller om det bare handler om præferencer.

Jeg ved fra mig selv af, at jeg er **super** dygtig til at kritisere ting jeg ikke aner en skid om.
I mange år tænkte jeg for eksemplel at [Björn Afzelius](https://www.youtube.com/watch?v=-ZjbR7Qxxeg) var utrolig cringe, indtil at jeg lyttede til hans musik eller alt hvad der omhandler [Rick James](https://www.youtube.com/watch?v=iWa-6g-TbgI) AKA [Puddel Garn](https://i.pinimg.com/736x/e5/52/29/e55229099117485820ae563dab9449e7.jpg), udelukende baseret på hans hår.
Musiken falder selfølgelig ikke i enhvers smag, og som en DJ skal vide hvilken musik han skal spille, for et bestemt publikum eller event, så skal en udvikler også vide hvilket værktøj der skal bruges i en given situation, og det er umuligt at vælge det rigtige værktøj uden at have undersøgt det grundigt først.

Argumenterne fra de online forumer kan også handle om [delt information bias](https://en.wikipedia.org/wiki/Shared_information_bias), hvor folk har delt sig i 2 grupper med hver deres viden, hvor ved at de to grupper hver især opnår consensus. Det kan holde folk fast i bruge de værktøjer de er bekendte og dygtige i.
Det samme kan gøre sig gældene i et team hvor at man vælger at gøre det som man altid har gjort, hvilket kan være nemt, bekvemt og hurtigt at komme i gang med et projekt. Men muligvis ikke den rigtige strategi på længere sigt.

"It depends" er det universielle svar inden for software udvikling. Så hvad er det lige præcis det kommer an på ?
Det er nogle af de områder jeg vil dække i min blog, omkring Blazor og .NET i web baseret udvikling.

Inden at jeg startede ud med bloggen, havde jeg et projekt at arbejde på.
Jeg kendte til de store dele af kravene, troede at jeg havde helt styr på hvordan koden skulle skæres og implimenteres.

Jeg tog grueligt fejl (emoji).

Nogle af de ting bankede hovedet ned i tastaturet over var kompleksiteten af implementeringen af dele af koden.
"Hvorfor h... skal det være så komplekst ? Er der ikke en mere simpel måde at gøre det på ?"
Men jeg havde ikke afsøgt de værktøjer (internt link) der var til rådighed med Blazor inden at jeg gik i gang.

Det primære i sowtware udvikling er at løse problemer. "Har du problem ?"
Men hvordan løser man et problem ? Man bryder det ned i mindre dele. Det lyder jo meget nemt.
Der er mange andre ting man bliver nødt til at dække:

- Hvilke værktøjer skal man vælge og hvorfor/vælge det rigtige værktøj til jobbet ? (intern link)
- Hvordan sikrer man at kunden får dækket alle krav til systemet ? (intern link)
- Hordan sikrer man at ændringer i systemet fremover ikke skaber problemer andre steder i systemet ? (internt link).
- Hvordan sikrer man en dækkende og overskuelig dokumentation som er nem at lave og vedligeholde ? og sikrer at den er korrekt (forkert dokumentation er værre end ikke at have nogen dokumentation) ? (internt link)

Jeg vil give mit bud på hvordan man kan løse ovenstående problemer, med udgangspunkt i en web applikation med Blazor og .NET.

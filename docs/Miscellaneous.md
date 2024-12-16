# Miscellaneous

Som udgangspunkt er det en god ide at holde det simpelt ([KISS-princippet](https://en.wikipedia.org/wiki/KISS_principle)), og man skal ikke spekulere for meget på, hvad applikationen måske kan udvikle sig til i fremtiden ([YAGNI-princippet](https://en.wikipedia.org/wiki/You_aren%27t_gonna_need_it)). At gætte på fremtidige behov kan komplicere kodebasen unødvendigt, og kompleksitet koster penge i både implementering og vedligehold. Der skal derfor være en meget god grund til at vælge Clean Architecture for at retfærdiggøre den øgede kompleksitet.

Med fokus på arkitektur er Vertical Slice Architecture det mest simple at starte med. Hver "skive" har ét fokusområde, og alt, hvad denne skive skal bruge, er samlet i én folder. Hvis man i fremtiden skulle få brug for at genbruge domænedelen af applikationen, kan man opdatere én skive ad gangen, indtil man har et domæne, der kan skilles ud og deles på tværs af projekter.

Repositories har sine fordele, men de er ikke altid nødvendige, især når man kan bruge en ORM (Object Relational Mapper) som [EF CORE](https://learn.microsoft.com/en-us/ef/core/), der fungerer som dataadgangslag til en database. Hvis man skulle få brug for at skifte til en anden database (jf. [YAGNI-princippet](https://en.wikipedia.org/wiki/You_aren%27t_gonna_need_it)), understøtter EF Core allerede mange [forskellige](https://learn.microsoft.com/en-us/ef/core/providers/?tabs=dotnet-core-cli) databaser via DbContext.

Når man afklarer kravene til applikationen, kan man i modelleringsfasen dele applikationen op i dele som i [Feature Driven Development](/docs/Udvikling.html?tabs=fdd%2Ccontroller#tabpanel_1_fdd):

- Theme

- Epic

- Feature

- User Story

Disse kan afspejle mappestrukturen i applikationen, hvilket giver sporbarhed mellem modellen og applikationen. Udover sporbarhed giver det, som applikationen udvikler sig, en klar afgrænsning af domænet, hvilket kan være svært at opnå enighed om i et team. Hvis applikationen på sigt bliver stor nok, kan dele af den nemt skilles ud og håndteres som selvstændige services, der kan bruges på tværs af andre applikationer.

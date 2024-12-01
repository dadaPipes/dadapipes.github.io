# Afslutning

Kunden har altid ret.
Produkt owner dikterer hvad og hvordan at applikationen skal laves.
Vi er der blot for at rådgive, ud fra den tekniske viden vi har, og lede kunden ind på hvad det er de gerne vil opnå og hvordan. Man må parkere sit ego for en stund og slippe det løs på StackOverflow udenfor arbejdstid. Som udgangspunkt skal man tænke at PO ikke ved noget som helst om webudvikling og hvilke muligheder der eksisterer og hvad man kan forvente. Derfor er vi nødt til at tage dem i hånden og stille alle de simple spørgsmål som, hvilken besked vil du gerne have tilbage hvis du taster dit password forkert 1, 2 eller tre gange ? og så præsenterer dem for de muligheder der er for feed back.
Det er som udvikler arrogant at tro at man ved bedre end PO og derfor er det en essentiel del af udviklingsfasen at spørge ind, både åbent, men også i detaljer.
At jonglere med kontakt til PO, dokumentation, front- og backend-udvikling er som at spille 4D-skak. Der er både dybde og bredde at tage højde for, men også en dimension af [tid](https://en.wikipedia.org/wiki/Spacetime#:~:text=In%20physics%2C%20spacetime%2C%20also%20called%20the%20space-time%20continuum%2C,dimension%20of%20time%20into%20a%20single%20four-dimensional%20continuum.), der styrer, hvornår og hvordan tingene hænger sammen. Hvert træk påvirker ikke kun nuet, men også fremtiden, og alt er forbundet i en større helhed, hvor planlægning og overblik er afgørende for succes.

Måske skal dette omskrives og med:
Douglas Adams’ "The Answer to Life, the Universe, and Everything" (42):
Brug dette som en humoristisk henvisning til at finde løsningen:
"Vi er på jagt efter vores egen version af 'svaret på livet, universet og alt', der samler alle tråde."

Occam's Razor vs KISS ( ikke bandet)
"Vi skal finde en løsning med minimal kompleksitet – som Occam’s Razor foreskriver."
Andre ting der skal flettes ind i det:
Fleksibilitet og skalering: First we take Manhattan, then we take Berlin.

Dokumentanion og fordele ved acceptence tests:
Ofte når der bliver spurgt ind til hvordan at man sætter sig ind i en kodebase hvor der er manglende eller mangelfuld dokumentation, så er svaret ofte at man skal læse testene for at sætte sig ind i hvad koden gør eller at sætte et breakpoint og hakke sig igennem systemet. Hvis man læser unit tests, så kan man se hvad de enkelte dele i systemet gør, men man for ingen forståelse af hvordan at systemet hænger sammen.
Hvis man derimod kigger på acceptence tests, som er det højeste niveau inden for tests og omhandler hvordan en bruger interigerer med systemet, så får man hurtigere et overblik over hvordan at systemet hænger sammen og hvad det gør. Man kunne med fordel dele testene op i 2. Tests for hvad at en bruger vil opnå i en given test, og tests

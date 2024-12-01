# Tests

Tests:

I forskellige online forumer er tests et tema som bliver diskuteret meget og bølgerne kan gå højt.
Ofte er det baseret på præferencer frem for funtionalitet og værdi.

Unit tests er noget som udviklere beekymrer sig om og ikke et mål for Produkt owner.
Unit tests er til for at sikre at mindre dele af koden fungerer efter hensigten, også hvis der bliver ændret i implementeringen af koden, på et senere tidspunkt.
Hvis koden der bliver testet kun er brugt 1 sted, giver det ikke meget værdi at teste den, da det vil være nemt at debugge koden og finde fejlen.
Er koden brugt flere steder i applikationen kan påvirke alle de steder i applikationen som bruger den del af koden. Derfor er det vigtigt at få testet den.
Fokuspmråder for unit tests vil derfor være metoder i bibilioteker, utility klasser og domæne modeler.

Ved at teste så højt oppe i systemet som muligt og fokusere på hvad brugerens krav er, er implementeringen af den undleæggende kode mere fleksibel, da ændringer i koden ikke bryder med brugerens krav.
(Hold det op imod public vs private tests:
"There is some debate among practitioners of TDD, documented in their blogs and other writings, as to whether it is wise to test private methods and data anyway. Some argue that private members are a mere implementation detail that may change, and should be allowed to do so without breaking numbers of tests. Thus it should be sufficient to test any class through its public interface or through its subclass interface, which some languages call the "protected" interface.[14] Others say that crucial aspects of functionality may be implemented in private methods and testing them directly offers advantage of smaller and more direct unit tests.[15][16]"

https://en.wikipedia.org/wiki/Test-driven_development)

Der er eksiterer kun 1 mål ( vejen er målet (?)). Der findes kun 1 form for tests.

I yoga findes der, på trods af ihærdige forsøg på at sælge alt muligt andet, kun en yoga stilling, skrædderstillingen. Det er den stilling hvor at kroppen kan slappe mest af og derved den bedste stilling til at meditere og opnå oplysning.
Alle andre yoga øvelser eksisterer kun som støttende øvelser, for at gøre det nemmere at kunne side i en skrædderstilling.

I webudvikling findes der kun 1 form for test, acceptence test.
I fuld stack vil det være en test der tager udganspunkt i UI og i en API som konsumeres af andre vil det tage udganspunkt i et endpoint.
Alle andre tests eksiterer kun som støttende tests, der gør det nemmere at sørge for at acceptence testen lyser grøn.

Da acceptence testen er udviklet i sammarbejde med product owner. er det på samme måde som skrædderstillingen i yoga, den test der gør det nemmest for en udvikler at slappe af i kroppen.

Støttende tests (unit tests osv): 
ved komplekse metoder ( fuck mit komplekse objekt der skal opdateres og alle de ting der skal tjekkes for!! ) (Derfor er det også nemmere at finde frem til acceptence tests med PO. Det ansvar ligger hos dem, da vi jo ikke kan læse deres tanker og gerne skulle undgå at være arogante).

TODO: (link til fetature page) Eksempel på en feature og acceptance test
Rød, Grøn, Omstrukturer.
Regler at støtte sig op af:
Man skal kunne læse et endpoint eller metode på en skærm uden at scrolle.
Kan man indsætte en kommentar om, hvordan koden virker, kan det indikere, at koden skal opdeles i mindre, selvforklarende dele.

Acceptence test der hvor at det kommer tættest på en bruger og samtidigt kan automatiseres:

- Frontend
- Komponent => Service klassen for den enkelte komponent.
- Ved at bruge en service klasse for en komponent som udgangspunkt for en acceptence test, så har man frie tøjler til at ændre i UI og de klasser som Service klassen sender og modtager data fra. Det vil sige at den kan bruges hvis den kalder andre klasser direkte eller kalder en API.

Tests (acceptence tests) som dokumentation.

Ny Viden !!! 

ATDD (acceptence test driven design), UTDD (unit test driven design), BDD (behavior driven design).

[UTDD]("Unit Test Driven Development") er målrettet udvikleren der skal implementere koden eller testeren der skal skrive tests inden implimentering af koden.

Bruger man en [lagdelt arktitektur](TODO: internt link), hvor at hvor at de forskellige dele af koden har faste pladser i hver deres lag, kan man modelere koden på forhånd og det er muligt for en tester at skrive tests, før at en udvikler implementerer koden. (TODO: Eksempel med Low-level diagram).

[ATDD]("Acceptence Test Driven Development") er målrettet udviklere, testere og forretningsinteressenter.
Bruger man en arkitektur som giver udvikleren mere frie tøjler, som eksempelvis i [VSA](TODO: internt link "Vertical Slice Architectur"), vil det være sværere at modelere på et low-level niveau, og for en tester at skrive unit tests før implementering af koden.

Tests i Client og API. Da der kan være et team der atbejder på hver især Client og Server projektet, kan man med fordel lave både acceptence tests i klient projektet og intergration tests for endpoints i API'en.

Kom ind på Publlic vs Private tests og sammenlign det med acceptence test/intergration tests af endpoints.
Hvad der giver mest værdi for den tid man bruger på dem og at det giver mere frihed til at ændre i koden uden at man kommer til at bryde unnit tests. [The Magic Tricks of Testing by Sandi Metz](https://www.youtube.com/watch?v=URSWYvyc42M) DER ER INDEKSERING MED TIDSPUNKTER I KOMMENTARENE, BRUG DEM!.

Indsæt links:

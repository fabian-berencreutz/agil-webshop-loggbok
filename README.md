# Individuell loggbok – Fabian Berencreutz
**Projekt:** Agil Webbshop

---

## Vårt agila arbetssätt och veckorutin

Vi har arbetat i enveckas-sprintar med en fast Scrum-struktur varje vecka:
- **Måndag (Sprintplanering):** Vi startade varje vecka med att gå igenom backloggen, bryta ner user stories till konkreta tasks och sätta ett sprintmål. Vi fördelade uppgifterna och såg till att beroenden mellan frontend och backend identifierades tidigt.
- **Tisdag, Onsdag, Torsdag (Dagliga avstämningar / Daily Standups):** Korta 10–15 minuters avstämningar där vi gick igenom vad vi gjort, vad vi skulle göra under dagen och om någon stött på hinder (blockers). Detta var extra viktigt för mig när jag jobbade med integrationer och gatewayen så att inte mina ändringar blockerade de andras frontend-arbete.
- **Fredag (Sprint Review & Retro):** Vi avslutade veckan med review där vi demonstrerade vad som var färdigt och testat mot vår Definition of Done. Direkt efteråt körde vi en kort retro för att reflektera över vad som funkat bra och vad vi behövde förbättra till nästa sprint (t.ex. att ta tag i teknisk skuld eller förbättra dokumentationen).

---

## Sammanfattning av Sprint 1, 2 och 3 (v. 39 – 42)

Under de första veckorna fokuserade jag mest på att få igång grunden i frontend och se till att den kunde prata med vår backend (`product-service`):

- **Sprint 1 (v. 39–40):** Jag drog igång frontend-repot och kopplade ihop det med backend så att vi faktiskt kunde hämta och visa produkter på sidan. Jag fixade CORS-strul och såg till att JWT-tokens sparades rätt i sessionen. Dessutom satte jag upp enhetstester med Vitest och skrev tester för produktkortet (`ProductCard`), samt fixade de första admin-länkarna i menyn.
- **Sprint 2 (v. 41):** Här lade jag till stöd för produktkategorier och bildlänkar – både i backend (Java enum/DTO) och i formuläret på frontend (`AddProductPage`). Jag byggde också en route-guard (`AdminRoute`) så att vanliga användare inte kan gå in på admin-sidorna utan att ha admin-rollen.
- **Sprint 3 (v. 42):** Den här veckan handlade mycket om att städa upp och hjälpa till. Jag fixade en irriterande dubblett i `pom.xml` för backend som låg och spökade med säkerhetsberoendena. I frontend granskade och mergade jag pull requests kring CSS och styling, och vi började snacka ihop oss om att vi behövde en gemensam gateway.

Under dessa sprintar satte vi vår rutin med planering på måndagar och review på fredagar, samt att vi alltid körde pull requests och kodgranskade varandra innan merge till `main`.

---

## Sprint 4 – vecka 43

### Vad jag arbetade med
- **Byggde en egen API Gateway / Reverse Proxy:** Jag skapade ett helt nytt Spring Boot-projekt (`gateway-agil`) från grunden. Istället för färdiga moduler kodade jag en egen proxy-service med Javas `HttpClient` som tar emot requests från frontend och vidarebefordrar dem till rätt mikrotjänst (auth, product och order).
- **Hantering av cookies & tokens:** Lade in logik i gatewayen för att läsa ut JWT-token både från `Authorization`-headern och från `HttpOnly`-cookies, så att frontend slipper hantera känsliga tokens direkt i koden.
- **Docker och CI/CD:** Jag skrev en Dockerfile och fixade en GitHub Actions-workflow så att gatewayen automatiskt byggs och pushas till Docker Hub när vi pushar kod.
- **Riktade om frontend:** Jag skrev om anropen i frontend så att alla requests går till vår gemensamma gateway istället för direkt till separata portar.
- **Fixade sessionshanteringen (`/auth/me`):** Jag lade till en lösning så att frontend frågar gatewayen vem som är inloggad när appen laddas om, vilket gjorde att man slipper bli utloggad vid siduppdatering.

### Eventuella problem och hur jag löste dem
- **Problemet:** Det var rörigt i frontend eftersom vi behövde hålla reda på olika adresser och portar för auth och produkter. Dessutom tappades inloggningen varje gång man uppdaterade sidan i webbläsaren, vilket var väldigt störigt när vi testade.
- **Lösningen:** Den egenbyggda gatewayen löste portkaoset genom att samla alla anrop bakom en gemensam bas-URL med samlad CORS-konfiguration. För utloggningsproblemet lade jag in en check i frontend mot `/auth/me` vid uppstart som hämtar användarinfon igen med sparad token så att sessionen lever vidare.

### Samarbete och agilt arbete
- På **måndagsplaneringen** kom vi fram till att vi behövde få ordning på arkitekturen och samla anropen via en gateway för att kunna gå vidare med testerna.
- Under **avstämningarna tisdag–torsdag** var det väldigt smidigt att kunna flagga hur omläggningen till gatewayen gick. Jag förklarade hur de nya proxy-endpoints och cookies fungerade så att varken Axel eller Maryam blev blockerade i sitt arbete med `/auth/me` och mocktester.
- På **fredagens sprint review** demonstrerade vi hela inloggningsflödet och produktvisningen via gatewayen. På retron efteråt konstaterade vi att gatewayen förenklade miljön rejält, men att vi nu behövde fokusera på affärslogik som lagersaldo till nästa sprint.

---

## Sprint 5 – vecka 44 (Pågående)

### Vad jag arbetade med
- **Lagersaldo och köpknapp:** Fixade så att köpknappen blir inaktiverad (disabled) och det står "Slutsåld" om en produkt har 0 i lagersaldo.
- **Välja antal vid köp:** Lade till en funktion på produktsidan där användaren kan välja hur många exemplar man vill köpa (multibuy) istället för att bara kunna klicka hem en i taget.
- **Städning i backend:** Tog bort lite gammal SQL-kod i `product-service` som inte behövdes längre så att vi kör helt rent med JPA/Hibernate.
- **Admin & lagersaldo:** Granskade och godkände Maryams PR för att admin ska kunna fylla på lagersaldot via gatewayen.
- **Dokumentation:** Skrev en tydlig README så att både vi i gruppen och läraren enkelt kan se hur man bygger och startar igång servicen.

### Eventuella problem och hur jag löste dem
- **Problemet:** Man kunde tidigare klicka på köp även om varan var helt slut, och det gick inte att köpa fler än en åt gången från produktsidan.
- **Lösningen:** Jag lade till en enkel koll i React-komponenten (`stock === 0`) som styr knappens status och visar en tydlig text för användaren. Sedan lade jag till ett `quantity`-state med knappar/input så rätt antal skickas med till varukorgen.

### Samarbete och agilt arbete
- Under **måndagsplaneringen** prioriterade vi köp- och lagerflödet i backloggen. Vi delade upp arbetet så att jag fokuserade på UX och lagervalidering vid köp, medan Maryam tog hand om admin-möjligheten att öka lagret.
- På **dagliga standups** stämde vi av så att våra ändringar i datamodellen och anropen synkade ihop utan att krocka. 
- Vi fortsätter att strikt följa vår **Definition of Done**: varje feature körs i en egen branch, granskas via Pull Request av minst en annan i teamet och måste passera GitHub Actions CI innan den mergas till `main`. På fredag har vi review där vi visar upp det kompletta flödet från tomt lager till påfyllning och köp.

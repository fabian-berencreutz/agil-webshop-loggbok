# Individuell loggbok – Fabian Berencreutz
**Projekt:** Agil Webbshop

---

## Sammanfattning av Sprint 1, 2 och 3 (v. 39 – 42)

Under de första veckorna fokuserade jag mest på att få igång grunden i frontend och se till att den kunde prata med vår backend (`product-service`):

- **Sprint 1 (v. 39–40):** Jag drog igång frontend-repot och kopplade ihop det med backend så att vi faktiskt kunde hämta och visa produkter på sidan. Jag fixade CORS-strul och såg till att JWT-tokens sparades rätt i sessionen. Dessutom satte jag upp enhetstester med Vitest och skrev tester för produktkortet (`ProductCard`), samt fixade de första admin-länkarna i menyn.
- **Sprint 2 (v. 41):** Här lade jag till stöd för produktkategorier och bildlänkar – både i backend (Java enum/DTO) och i formuläret på frontend (`AddProductPage`). Jag byggde också en route-guard (`AdminRoute`) så att vanliga användare inte kan gå in på admin-sidorna utan att ha admin-rollen.
- **Sprint 3 (v. 42):** Den här veckan handlade mycket om att städa upp och hjälpa till. Jag fixade en irriterande dubblett i `pom.xml` för backend som låg och spökade med säkerhetsberoendena. I frontend granskade och mergade jag pull requests kring CSS och styling, och vi började snacka ihop oss om att vi behövde en gemensam gateway.

Samarbetet flöt på bra genom dagliga korta avstämningar och att vi alltid körde pull requests och granskade varandras kod innan merge.

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
- Eftersom en gemensam gateway ändrade hur alla i teamet anropar backend tog jag upp det på våra standups och förklarade hur proxy-endpoints och tokens fungerade så att ingen blev blockad.
- Jag samarbetade med de andra i gruppen som byggde vidare på endpoints och tester, och granskade deras pull requests löpande så att vi snabbt fick ihop helheten.

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
- Vi synkade ihop backend och frontend så att logiken för att minska lagret (vid köp) och öka lagret (för admin) lirar ihop utan buggar.
- Vi fortsätter att jobba tätt med vår backlog på GitHub/Trello, hålla korta reviews av varandras kod och se till att inte merga något till `main` förrän testerna rullar grönt i våra workflows.

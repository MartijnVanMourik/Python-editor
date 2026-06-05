# Educatieve Python Editor

Een moderne, interactieve en volledig cliënt-side Python-leeromgeving ontworpen voor havo/vwo Informatica. Dit project draait direct in de browser en maakt gebruik van **Skulpt** voor offline Python-uitvoering en **CodeMirror** voor een professionele code-editorervaring.

---

## 🌟 Belangrijkste Kenmerken

*   **Offline Capable & Cliënt-side**: Geen backend of server-installatie vereist voor eindgebruikers. Alles draait veilig en snel in de browser.
*   **Interactieve Turtle Graphics**:
    *   **Volledig Scherm**: Vergroot het tekengebied met vloeiende animaties.
    *   **Interactief Zoomen & Pannen**: Versleep de tekening met je muis (of vinger op mobiel) en zoom in/out met het muiswiel.
    *   **Zwevend Bedieningspaneel**: Snel inzoomen, uitzoomen en de weergave centreren met één klik.
    *   **Live Run in Fullscreen**: Voer code uit en bekijk de animatie direct terwijl je in volledig scherm bent.
*   **Trinket-Style Multi-bestand Projecten (OOP)**:
    *   **Tabbladen-UI**: Direct bestanden aanmaken (`+`), hernoemen (dubbelklik) of verwijderen via een vloeiende tabbladenbalk boven de editor.
    *   **Virtueel Importsysteem**: Skulpt is zo uitgebreid dat studenten hun eigen klassen en bestanden kunnen importeren (bijv. `from dier import Dier`), wat volwaardig objectgeoriënteerd programmeren lokaal in de browser mogelijk maakt!
    *   **Slimme Auto-Save**: Alle geopende bestanden worden per opdracht real-time in de browser (`localStorage`) opgeslagen.
*   **Turtle Auto-detectie & Reactieve Omgeving**:
    *   Zodra er `import turtle` of `from turtle import` in de code wordt getypt, schakelt de editor automatisch over naar de **Turtle Canvas**-weergave. Zodra deze imports worden verwijderd, keert het scherm direct terug naar de standaard **Console**-weergave.
*   **Slimme Automatische Unit Tests**:
    *   Evalueert studentencode automatisch op basis van gedefinieerde testcases.
    *   **Strikte scheiding**: De testrunner scheidt invoerprompts (zoals `Hoe heet je? `) van de daadwerkelijk geprinte waarden (`print()`). Hierdoor kunnen testen betrouwbaar op `"exact"` matchen, terwijl studenten toch duidelijke invoerprompts kunnen gebruiken.
*   **Voortgangsbehoud & Project-Isolatie**: Code en testresultaten worden per opdracht automatisch in de `localStorage` opgeslagen. Dit is volledig **geïsoleerd per opdrachtenreeks (cursus)** op basis van het geladen JSON-bestand in de URL. Hierdoor kunnen studenten aan meerdere verschillende cursussen tegelijkertijd werken zonder dat hun voortgang botst of elkaar overschrijft!
*   **Voortgang Exporteren/Importeren**: Studenten kunnen hun werk exporteren naar een compact `.json` inleverbestand en later weer importeren om verder te werken.

---

## 🔗 Opdrachten Dynamisch Inladen via de URL

Standaard laadt de editor drie ingebouwde demo-opdrachten. Je kunt echter heel eenvoudig je eigen opdrachtenreeksen inladen met een query-parameter in de URL.

### Vereenvoudigde Querystring

Plaats je eigen JSON-opdrachtenbestand in de map `opdrachten/` (bijvoorbeeld `opdrachten/opdracht1.json`). Je kunt dit bestand vervolgens inladen door alleen de naam van het bestand op te geven in de URL:

```text
index.html?config=opdracht1
```

De editor herkent dit automatisch en vertaalt het op de achtergrond naar `opdrachten/opdracht1.json`.

### Flexibiliteit & Backwards Compatibility
De URL-parser is slim en ondersteunt ook handmatige paden en externe URL's:
*   **Submappen**: `index.html?config=opdrachten/opdracht1.json`
*   **Externe hostings**: `index.html?config=https://mijnwebsite.nl/lesmateriaal.json`

### 🎯 Specifieke Opdracht Direct Laden (`task=` / `id=`)
Standaard opent de editor altijd de eerste opdracht uit de reeks. Je kunt echter een specifieke opdracht direct als actieve opdracht openen door de query-parameter `task` of `id` mee te geven met het ID van die opdracht:

👉 `index.html?config=opdracht1&task=opdracht_turtle_id`

---

## 📝 Structuur van het JSON-Configuratiebestand

Een opdrachtenbestand is een JSON-bestand dat een titel en een lijst met opdrachten (tabs) bevat.

### Velddefinities

#### Hoofdniveau:
*   **`title`** *(string)*: De titel van de cursus of module die linksboven in de zwarte balk wordt getoond.
*   **`tabs`** *(array)*: Een lijst met opdracht-objecten die in het menu aan de linkerkant verschijnen.

#### Opdracht-object (binnen `tabs`):
*   **`id`** *(string)*: Een unieke identificatie (zonder spaties) om de voortgang van de student in de browser op te slaan.
*   **`filename`** *(string)*: De bestandsnaam die boven de editor en in het menu wordt getoond (bijv. `som.py`).
*   **`instruction`** *(string)*: De opdrachtomschrijving. Dit veld ondersteunt volledige HTML-opmaak (inclusief Tailwind CSS-klassen) voor rijke en interactieve instructies.
*   **`initialCode`** *(string)*: De startcode die in de editor klaarstaat voor de student. Gebruik `\n` voor nieuwe regels (alleen gebruikt als er geen `files` array is gedefinieerd).
*   **`tests`** *(array)*: Een lijst met testgevallen die de code van de student automatisch controleren (leeg laten `[]` voor Turtle-opdrachten).
*   **`previewImage`** *(string, optioneel)*: Een Base64-gecodeerde afbeelding (DataURL, bijv. `data:image/png;base64,...`) die als verwacht resultaat onder de opdrachtomschrijving wordt weergegeven. Dit maakt de opdrachtenreeks volledig zelfvoorzienend en offline-compatible zonder extra losse bestanden.
*   **`files`** *(array, optioneel)*: Een lijst met startbestanden voor multi-bestand (Trinket-stijl) opdrachten. Elk bestandsobject bevat:
    *   **`filename`** *(string)*: De bestandsnaam van dit bestand (bijv. `dier.py`).
    *   **`initialCode`** *(string)*: De begin-code die in dit bestand staat.
    *   **`isMain`** *(boolean)*: Moet `true` zijn voor exact één bestand (meestal `main.py`), wat de hoofdingang is van de code-uitvoering en tests.

#### Testgeval-object (binnen `tests`):
*   **`name`** *(string)*: De naam van de test die de student ziet (bijv. `Test met Jan (16)`).
*   **`inputs`** *(array van strings)*: De opeenvolgende invoerwaarden die automatisch worden ingevuld wanneer de code van de student om `input()` vraagt.
*   **`expectedOutput`** *(string)*: De exacte tekst die de code via `print()` moet uitvoeren.
*   **`matchType`** *(string)*: Hoe de output gecontroleerd moet worden:
    *   `"exact"`: (Aanbevolen) De geprinte output moet exact overeenkomen met `expectedOutput` (hoofdletterongevoelig, witruimte aan begin/eind genegeerd).
    *   `"contains"`: De `expectedOutput` moet ergens als substring in de geprinte output voorkomen.

---

## 🗂️ Voorbeeld/Sjabloon JSON-bestand

Maak een bestand genaamd `opdracht1.json` aan in de map `opdrachten/` en gebruik de volgende structuur:

```json
{
  "title": "Informatica Klas 4 - Basis Python",
  "tabs": [
    {
      "id": "opdracht_optellen",
      "filename": "optellen.py",
      "instruction": "<h3 class='text-lg font-semibold text-slate-100 mb-2'>De Optelmachine</h3><p class='mb-2'>Schrijf een programma dat de gebruiker om twee getallen vraagt en de som daarvan print.</p><p class='text-xs text-amber-400 font-mono bg-slate-950 p-2 rounded border border-slate-800/60'>Tip: Vergeet niet int() te gebruiken om de invoer om te zetten!</p>",
      "initialCode": "# Vraag om invoer\ngetal1 = input('Eerste getal: ')\ngetal2 = input('Tweede getal: ')\n\n# Bereken en print de som\n",
      "tests": [
        {
          "name": "Eenvoudig optellen (5 + 10)",
          "inputs": ["5", "10"],
          "expectedOutput": "15",
          "matchType": "exact"
        },
        {
          "name": "Negatieve getallen (-3 + 8)",
          "inputs": ["-3", "8"],
          "expectedOutput": "5",
          "matchType": "exact"
        }
      ]
    },
    {
      "id": "opdracht_tekenen",
      "filename": "vierkant.py",
      "instruction": "<h3 class='text-lg font-semibold text-slate-100 mb-2'>Teken een Vierkant</h3><p class='mb-2'>Gebruik de turtle bibliotheek om een vierkant van 100 bij 100 pixels te tekenen.</p>",
      "initialCode": "import turtle\nt = turtle.Turtle()\n\n# Teken hier je vierkant\n",
      "tests": []
    }
  ]
}
```

---

## 👩‍🏫 Docentenmodus & Auteursomgeving (Opdrachten Editor)

De Educatieve Python Editor bevat een ingebouwde, verborgen **Docentenmodus (Auteursmodus)**. Hiermee kun je direct in de browser opdrachten aanmaken, aanpassen, herordenen en unit tests ontwerpen zonder handmatig complexe JSON-bestanden te hoeven schrijven.

### 🔌 Activeren van de verschillende Modi (`mode=`)

Je kunt de weergave en het gedrag van de editor aanpassen met de query-parameter `mode`. De volgende opties zijn beschikbaar:

*   **Docentenmodus** (`mode=teacher` of `mode=docent`): Hiermee kun je opdrachten bewerken, tests aanmaken en downloaden. (De oude parameter `edit=true` werkt ook nog voor achterwaartse compatibiliteit).
    👉 [http://localhost:8000/index.html?config=opdracht1&mode=teacher](http://localhost:8000/index.html?config=opdracht1&mode=teacher)
*   **Leerlingmodus** (`mode=student` of `mode=leerling`): Dit is de standaardmodus met de editor, opdrachtenlijst en console/canvas om code in te schrijven.
    👉 [http://localhost:8000/index.html?config=opdracht1&mode=student](http://localhost:8000/index.html?config=opdracht1&mode=student)
*   **Demonstratiemodus** (`mode=demo` of `mode=demonstratie`): Toont *alleen* het uitgevoerde programma (het resultaat op het canvas of in de console) en een "Voer Code Uit" knop. De code-editor en instructies worden volledig verborgen en de code wordt automatisch bij het laden gestart. Dit is ideaal voor het tonen van werkende voorbeelden of embeds.
    👉 [http://localhost:8000/index.html?config=opdracht1&mode=demo](http://localhost:8000/index.html?config=opdracht1&mode=demo)

Wanneer de Docentenmodus actief is, verschijnt er een opvallende **paarse statusbalk** bovenin het scherm om aan te geven dat je in bewerkingsmodus bent.

---

### 🎨 Canvasgrootte Aanpassen (`canvas_size=`)

Je kunt de grootte van het Turtle-tekengebied (het canvas) bepalen via de parameter `canvas_size` (of `size`). Standaard is dit `400`x`400` pixels. Je kunt hier een getal opgeven om de breedte en hoogte (vierkant) te bepalen, bijvoorbeeld `500` voor een canvas van 500x500 pixels.
Dit is vooral handig in combinatie met de Demonstratiemodus om een specifiek formaat embed te tonen:

👉 [http://localhost:8000/index.html?config=opdracht1&mode=demo&canvas_size=500](http://localhost:8000/index.html?config=opdracht1&mode=demo&canvas_size=500)

---

### 🗂️ Menu Tonen/Verbergen (`menu=`)

Standaard wordt de opdrachtenlijst (het navigatiemenu) automatisch getoond als de cursus meerdere opdrachten bevat, en verborgen als er slechts één opdracht is. Je kunt dit gedrag handmatig overrulen met de `menu` parameter:

*   **Menu forceren**: `menu=true` of `menu=show` (toont het menu altijd, zelfs bij 1 opdracht).
    👉 [http://localhost:8000/index.html?config=opdracht1&id=opdracht_turtle_id&menu=show](http://localhost:8000/index.html?config=opdracht1&id=opdracht_turtle_id&menu=show)
*   **Menu verbergen**: `menu=false` of `menu=hide` (verbergt het menu altijd, zelfs bij meerdere opdrachten).
    👉 [http://localhost:8000/index.html?config=opdracht1&menu=hide](http://localhost:8000/index.html?config=opdracht1&menu=hide)

---

### 🛠️ Mogelijkheden in de Docentenmodus

#### 1. Cursustitel Bewerken
Klik simpelweg op de titel van de cursus (linksboven in de zwarte balk). De tekst wordt omlijnd met een stippellijn en is direct aanpasbaar.

#### 2. Opdrachten Beheren (Linkermenu)
*   **Hernoemen**: Dubbelklik op een opdrachtknop in de lijst om de bestandsnaam (bijv. `opdracht_1.py`) aan te passen.
*   **Volgorde aanpassen**: Gebruik de omhoog/omlaag-pijltjes naast elke opdracht om de volgorde in het menu te wijzigen.
*   **Verwijderen**: Klik op het prullenbak-icoon om een opdracht volledig te wissen.
*   **Nieuwe Opdracht toevoegen**: Klik onderaan de lijst op de paarse knop `+ NIEUWE OPDRACHT` om een nieuwe opdracht op basis van een sjabloon toe te voegen.

#### 3. WYSIWYG Rijke-Tekst Editor (Instructies)
Boven de opdrachtomschrijving verschijnt een bewerkingsbalk. Hiermee kun je geselecteerde tekst opmaken:
*   **B / I**: Vetgedrukt of cursief maken.
*   **Opsommingen**: Bullet-lists en genummerde lijsten invoegen.
*   **Kopteksten & Paragrafen**: H3-koppen en standaard alinea's invoegen.
*   **Dynamische Status-Highlighting**: Wanneer je cursor of selectie zich in een specifiek opgemaakt element bevindt (zoals `b`, `i`, `h3`, `code`, een tip-/infokader of een tabel), licht de bijbehorende knop in de toolbar automatisch op! Hierdoor zie je direct welke elementen actief zijn.
*   **Aan/Uit Toggles**:
    *   `Vet (B)` / `Cursief (I)` / `Koptekst (H3)`: Klik op de knop om de stijl toe te passen. Staat je cursor al in tekst met deze stijl? Klik nogmaals op de knop om de opmaak te verwijderen! Een `H3`-koptekst verandert dan weer netjes terug in een standaard paragraaf (`p`).
    *   `Code` (Inline code): Selecteer tekst en klik op `Code` om er een inline code-element van te maken. Als je cursor of selectie al in een code-element staat, klik dan nogmaals op `Code` om het element te verwijderen.
    *   **Slimme Code-Conversie**: Selecteer je meerdere regels en klik je op `Code`? Dan wordt er direct een groot block-level `<pre><code>` segment van gemaakt. Druk je op **Enter** terwijl je een inline code-element aan het bewerken bent? Dan wordt het code-element ter plekke gesplitst en automatisch omgezet in een `<pre><code>` blok!
    *   `Tip` / `Info` (Callout-kaders): Voeg een opvallend geel tipkader of blauw informatiekader toe. Staat de cursor al in zo'n kader? Klik nogmaals op de knop om het complete kader **inclusief de volledige inhoud direct te wissen**!
*   **Stijlvaste Kopiëren/Plakken Beveiliging 📋**: Wanneer je tekst van externe websites kopieert en in het tip- of info-venster plakt, onderschept de editor dit. Alle externe lettertypes, kleuren en opmaakstijlen worden gewist en automatisch omgezet naar platte tekst, waardoor de standaardkleuren en premium typografie van het lokale thema vlekkeloos behouden blijven!
*   **Interactieve Tabellen 📊**:
    *   `Tabel`: Voegt direct een prachtig donker gestileerde 2x2 tabel toe.
    *   `+ Rij` / `+ Kolom` *(dynamisch)*: Zodra je cursor in een tabelcel staat, verschijnen er automatisch paarse knoppen in de toolbar om direct een rij eronder of kolom ernaast in te voegen.
    *   `🗑️ Tabel` *(dynamisch)*: Verwijdert met één klik de gehele actieve tabel uit de omschrijving.
*   **Kleurenpalet 🎨**: Klik op het palet-icoon om een dropdown met **16 gecureerde, heldere webkleuren** te openen. Hiermee kun je geselecteerde tekst eenvoudig een kleur geven die uitstekend leesbaar is op een donkere achtergrond.
*   **HTML Modus**: Klik op het `<>` (code-compare) icoon rechts op de toolbar om te schakelen tussen de visuele weergave en de ruwe HTML-code. Dit is handig voor docenten die fijnmazige controle willen over de opmaak.

#### 4. Geïntegreerde Voorbeeldweergave ("Verwacht resultaat") 📸
*   **Direct Inbedden**: Bij Turtle-opdrachten kun je op de camera-knop (📸) in het zwevende bedieningspaneel klikken. De editor legt de tekening direct vast en slaat deze op als Base64-gecodeerde PNG-afbeelding (`task.previewImage`) in de opdracht zelf.
*   **Live Preview**: De voorbeeldtekening verschijnt onmiddellijk live onder de omschrijving onder het kopje **"Verwacht resultaat:"**.
*   **Verwijderen**: In Docentenmodus verschijnt er een subtiel vuilnisbak-icoontje naast het kopje om de preview met één klik te wissen.
*   **Offline Compatibiliteit**: Omdat de afbeeldingen rechtstreeks in de geëxporteerde JSON worden ingebed, hoef je geen losse PNG-afbeeldingen meer op een server te plaatsen. De opdrachtenreeks is 100% zelfvoorzienend en offline-compatible!
*   **Disk Fallback**: Mocht er geen ingebedde afbeelding zijn, dan zoekt de editor automatisch naar een lokaal bestand op schijf (`opdrachten/<bestandsnaam-zonder-py>.png`) om compatibel te blijven met bestaand lesmateriaal.

#### 5. Interactieve Unit Test Builder
Wanneer je naar het tabblad **Tests** schakelt onder de console, zie je in de Docentenmodus een interactieve formulier-builder in plaats van lege testresultaten:
*   **Nieuwe Testcase**: Klik op `+ NIEUWE TESTCASE TOEVOEGEN`.
*   **Naam**: Geef de test een duidelijke omschrijving (bijv. "Test met invoer 10 en 20").
*   **Invoerwaarden**: Voer de waarden in die opeenvolgend aan `input()` moeten worden meegegeven (gescheiden door komma's).
*   **Match**: Kies tussen:
    *   `Exact match`: De geprinte output moet exact overeenkomen (hoofdletterongevoelig, witruimte aan begin/eind genegeerd).
    *   `Bevat substring`: De verwachte tekst moet ergens in de geprinte output voorkomen.
*   **Verwachte Output**: Typ de verwachte geprinte tekst. Dit veld ondersteunt meerdere regels.

---

### 💾 Exporteren en Opslaan van je Cursus

Omdat de applicatie volledig cliënt-side in de browser draait, kan deze niet rechtstreeks bestanden op jouw lokale schijf overschrijven. Je beheert en slaat je cursusbestanden als volgt op:

#### 1. Bestandsnaam Aanpassen
In de paarse banner zie je het invoerveld **`Bestand:`**. Hier kun je de gewenste JSON-bestandsnaam typen (bijv. `les_1_optellen.json`). Als je op downloaden klikt, krijgt het bestand automatisch deze naam.

#### 2. Starten met een Lege Cursus
Wil je een compleet nieuwe cursus vanaf de grond af opbouwen? Klik op de paarse knop **`Nieuwe Cursus`** in de banner. De editor wist alle in-memory voortgang en laadt een schone sjabloon-opdracht waarin je direct kunt gaan typen.

#### 3. Downloaden & Opslaan
1.  Klik op de knop **`Download Cursus JSON`**.
2.  Het JSON-bestand wordt direct met de door jou opgegeven bestandsnaam gedownload.
3.  Plaats dit gedownloade JSON-bestand in de map `opdrachten/` van je project.
4.  Laad de pagina met de bijbehorende query-parameter (bijv. `index.html?config=les_1_optellen`) om direct je nieuwe cursus te gebruiken!

---

## 💻 Lokaal Uitvoeren & Testen

Omdat browsers om veiligheidsredenen (CORS) geen lokale bestanden via `file://` mogen inladen (`fetch()`), moet je het project lokaal via een eenvoudige webserver starten.

### Methode 1: Python HTTP Server (Aanbevolen)
Open je terminal in de projectmap en voer het volgende commando uit:

```bash
python3 -m http.server 8000
```

Open vervolgens je browser en navigeer naar:
👉 [http://localhost:8000/index.html?config=opdracht1](http://localhost:8000/index.html?config=opdracht1)

### Methode 2: VS Code Live Server
Als je VS Code gebruikt, installeer dan de extensie **Live Server**, klik rechtsonder op **Go Live** en pas de URL in je browser aan met de querystring `?config=opdracht1`.

---

## 🚀 Publiceren via GitHub Pages (Gratis Hosting)

Je kunt dit project gratis hosten op GitHub Pages zodat je leerlingen direct toegang hebben vanaf elk apparaat:

1.  Maak een nieuwe **publieke** repository aan op [GitHub](https://github.com).
2.  Push al je bestanden (`index.html`, de map `opdrachten/`, etc.) naar deze repository.
3.  Ga in je repository naar **Settings** -> **Pages**.
4.  Kies bij *Source* voor **Deploy from a branch**.
5.  Selecteer de branch `main` (of `master`), laat de map op `/ (root)` staan en klik op **Save**.
6.  Binnen 1-2 minuten is je website live! Je kunt je opdrachten direct delen via de link:
    `https://<jouw-github-naam>.github.io/<repo-naam>/index.html?config=opdracht1`

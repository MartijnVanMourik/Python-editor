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
*   **Slimme Automatische Unit Tests**:
    *   Evalueert studentencode automatisch op basis van gedefinieerde testcases.
    *   **Strikte scheiding**: De testrunner scheidt invoerprompts (zoals `Hoe heet je? `) van de daadwerkelijk geprinte waarden (`print()`). Hierdoor kunnen testen betrouwbaar op `"exact"` matchen, terwijl studenten toch duidelijke invoerprompts kunnen gebruiken.
*   **Voortgangsbehoud**: Code van studenten wordt automatisch per opdracht in de `localStorage` van de browser opgeslagen.
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
*   **`initialCode`** *(string)*: De startcode die in de editor klaarstaat voor de student. Gebruik `\n` voor nieuwe regels.
*   **`tests`** *(array)*: Een lijst met testgevallen die de code van de student automatisch controleren (leeg laten `[]` voor Turtle-opdrachten).

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

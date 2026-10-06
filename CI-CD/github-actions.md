# GitHub Actions

GitHub Actions är en CI/CD-plattform för att automatisera delar av programvaruutvecklingen. Den kan användas för att bygga, testa och driftsätta applikationer, men också för andra uppgifter i ett GitHub-repository, exempelvis att märka nya issues med etiketter.

Plattformen stöder återanvändbara actions och både GitHub-hostade och egenhostade runners. Ett repository, eller repo, är ett versionshanterat kodarkiv. En runner är den körmiljö där ett jobb utförs.

## Komponenterna i GitHub Actions

Ett workflow kan startas manuellt eller konfigureras att starta när en viss händelse inträffar. Exempelvis kan en pull request starta ett workflow som validerar ändringarna som en del av kodgranskningen.

Ett workflow består av följande delar:

- **Workflow:** ett automatiserat arbetsflöde med ett eller flera jobb. Jobben kan köras parallellt eller i en bestämd ordning genom beroenden.
- **Jobb, jobs:** en samling steg som körs på samma runner.
- **Steg, steps:** de enskilda uppgifterna i ett jobb. Ett steg kör antingen kommandon eller en action.

Ett steg med `run` kan innehålla ett kommando eller ett skript med flera kommandon. Ett steg med `uses` anropar en återanvändbar action.

### Workflows

Ett workflow är en konfigurerbar, automatiserad process som definieras i en YAML-fil och checkas in i kodarkivet. Filen placeras i mappen `.github/workflows/` och har filändelsen `.yml` eller `.yaml`.

Ett repo kan innehålla flera workflows med olika uppgifter. Ett kan bygga och testa pull requests, ett annat kan driftsätta applikationen när en release publiceras och ett tredje kan lägga till en etikett när någon öppnar en issue.

Workflows kan startas av händelser, manuellt eller enligt ett schema, beroende på vilka utlösare som definieras i filen.

### Händelser, events

En händelse är något som kan starta en workflow-körning. Exempel är att någon öppnar en pull request, skapar en issue eller pushar commits eller taggar till kodarkivet.

Ett workflow kan också startas enligt ett schema, genom ett anrop till GitHubs REST-API eller manuellt. Workflowet behöver vara konfigurerat för den aktuella utlösaren. `workflow_dispatch` används för manuella körningar och kan även anropas via API.

### Jobb, jobs

Ett jobb innehåller steg som normalt körs i ordning på samma runner. Ett senare steg kan därför använda filer som skapats av ett tidigare steg. Ett steg kan exempelvis bygga applikationen och nästa steg testa det som byggts.

Varje `run`-steg körs däremot i en egen shellprocess. En miljövariabel som sätts med `export` i ett steg följer därför inte automatiskt med till nästa steg. För att föra vidare värden används exempelvis steg-utdata eller GitHubs miljöfiler, beroende på vad värdet ska användas till.

Jobb utan angivna beroenden kan köras parallellt när runners och tillgänglig kapacitet tillåter det. Beroenden mellan jobb anges med `needs`. Ett beroende jobb väntar normalt tills de jobb som anges har slutförts utan fel. Andra beteenden kan konfigureras med villkor.

Man kan exempelvis ha flera byggjobb för olika arkitekturer och ett paketeringsjobb som behöver resultaten från samtliga byggjobb. Byggjobben kan köras parallellt, medan paketeringen startar när de har lyckats. Filer delas inte automatiskt mellan separata jobb. De kan exempelvis överföras som artefakter.

### Actions

En action är en återanvändbar komponent som utför en uppgift i ett workflow. Den minskar behovet av att upprepa samma kod i flera workflow-filer.

En action kan exempelvis checka ut kodarkivet, konfigurera en verktygskedja eller ordna autentisering mot en molnleverantör. Man kan skriva egna actions eller använda färdiga actions, exempelvis från GitHub Marketplace.

### Runners

En runner kör ett jobb åt gången. Ett workflow med flera jobb kan därför använda flera runners.

GitHub erbjuder runners med bland annat Ubuntu Linux, Windows och macOS. Vanliga GitHub-hostade runners kör varje jobb i en ny miljö. Det är alltså jobbet, inte hela workflowet, som tilldelas miljön. För Ubuntu används normalt en ny virtuell maskin.

GitHub erbjuder även större runners med mer resurser. Om man behöver särskild hårdvara, en annan miljö eller tillgång till egna system kan man använda egenhostade runners. Då ansvarar man själv för deras installation och förvaltning. Jobb kan också konfigureras att köra sina steg i en container på en kompatibel runner.

## Ett exempel på en workflow-fil

GitHub Actions använder YAML för att beskriva arbetsflödet. Indrag är en del av syntaxen och visar vilka inställningar som hör ihop.

Följande workflow startar vid en push och gör fyra saker: checkar ut koden, installerar Node.js, installerar Bats och visar den installerade Bats-versionen. Bats står för *Bash Automated Testing System* och är ett testramverk för Bash. Exemplet kör inga faktiska tester, utan kontrollerar bara att verktyget kan startas.

```yaml
name: learn-github-actions
run-name: ${{ github.actor }} lär sig GitHub Actions

on: [push]

jobs:
  check-bats-version:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v7
      - uses: actions/setup-node@v7
        with:
          node-version: '24'
      - run: npm install -g bats
      - run: bats -v
```

Samma exempel med kommentarer:

```yaml
# Valfritt namn som visas på kodarkivets Actions-flik.
# Om namnet saknas visar GitHub sökvägen till workflow-filen.
name: learn-github-actions

# Valfritt namn för varje körning av workflowet.
# github.actor identifierar användaren eller appen som startade körningen.
run-name: ${{ github.actor }} lär sig GitHub Actions

# Starta workflowet när commits eller taggar pushas till kodarkivet.
on: [push]

# Samla jobben som hör till workflowet.
jobs:
  # Jobbets unika identifierare.
  check-bats-version:
    # Kör jobbet på en GitHub-hostad runner med Ubuntu.
    runs-on: ubuntu-latest

    # Kör stegen i ordning på samma runner.
    steps:
      # Checka ut kodarkivet så att senare steg kan läsa dess filer.
      - uses: actions/checkout@v7

      # Installera Node.js och gör node och npm tillgängliga i PATH.
      - uses: actions/setup-node@v7
        with:
          # Välj vilken Node.js-version som ska användas.
          node-version: '24'

      # Installera Bats globalt i jobbets körmiljö med npm.
      - run: npm install -g bats

      # Visa den installerade Bats-versionen.
      - run: bats -v
```

`with` anger indata till en action. I exemplet väljer `node-version` versionen av Node.js som `setup-node` ska konfigurera. Versionsangivelsen efter `@` väljer vilken version av själva action-komponenten som används.

När en händelse startar workflowet skapar GitHub en workflow-körning. Under kodarkivets Actions-flik kan man öppna körningen, följa jobbens status i en visualiseringsgraf och läsa loggarna för varje steg.

## Variabler i workflows

Variabler används för att spara och återanvända konfigurationsvärden. Exempel är byggflaggor, servernamn och andra uppgifter som inte behöver hållas hemliga. Känsliga värden, exempelvis lösenord och åtkomstnycklar, hanteras som secrets.

GitHub tillhandahåller standardmiljövariabler, men man kan också definiera egna värden. Egna variabler kan definieras på två huvudsakliga sätt:

- Med `env` i workflow-filen. Miljövariabeln kan gälla hela workflowet, ett visst jobb eller ett enskilt steg.
- Som konfigurationsvariabler på organisations-, kodarkivs- eller miljönivå. De kan återanvändas i workflows och läsas genom contextet `vars`.

Att en variabel *interpoleras* innebär att en referens till variabeln ersätts med dess värde. Var detta sker beror på syntaxen. GitHub utvärderar uttryck som `${{ vars.SERVER_NAME }}`, medan ett Bash-shell på runnern expanderar en miljövariabel som `$SERVER_NAME` när kommandot körs.

Kommandon kan läsa och ändra miljövariabler i sin process. Ändringen uppdaterar däremot inte automatiskt en sparad konfigurationsvariabel i GitHub eller miljön för andra steg.

### Exempel med miljövariabler

```yaml
name: Hälsning med miljövariabler

on:
  workflow_dispatch:

env:
  DAY_OF_WEEK: Måndag

jobs:
  greeting_job:
    runs-on: ubuntu-latest
    env:
      GREETING: Hej
    steps:
      - name: Skriv en hälsning
        run: echo "$GREETING, i dag är det $DAY_OF_WEEK!"
```

`workflow_dispatch` gör att workflowet kan startas manuellt. Observera kolonet efter nyckeln.

`DAY_OF_WEEK` definieras på workflow-nivå och är tillgänglig i dess jobb. `GREETING` definieras på jobb-nivå och är tillgänglig i stegen i det jobbet. I `run`-kommandot läser Bash variablernas värden. Exemplet skriver ut: ”Hej, i dag är det Måndag!”. Värdet är fast och beräknar inte vilken veckodag det faktiskt är.

## Skript och kommandon i workflows

Med `run` kan man köra kommandon eller skript på runnern. Följande fristående workflow visar hur ett kommando körs:

```yaml
name: Installera Bats

on:
  workflow_dispatch:

jobs:
  example-job:
    runs-on: ubuntu-latest
    steps:
      - run: npm install -g bats
```

Detta exempel använder den Node.js- och npm-installation som finns i den GitHub-hostade Ubuntu-miljön. I det tidigare exemplet används `setup-node` för att uttryckligen välja Node.js-version.

### Köra ett skript från kodarkivet

För att köra ett skript som finns i kodarkivet behöver man först checka ut filerna på runnern. Därefter kan man ange arbetsmappen och köra skriptet:

```yaml
name: Kör ett skript

on:
  workflow_dispatch:

jobs:
  example-job:
    runs-on: ubuntu-latest
    defaults:
      run:
        working-directory: ./scripts
    steps:
      - name: Checka ut kodarkivet
        uses: actions/checkout@v7
      - name: Kör skriptet
        run: bash ./my-script.sh
```

Exemplet förutsätter att filen `scripts/my-script.sh` finns i kodarkivet. `defaults.run.working-directory` anger standardarbetsmappen för jobbets `run`-steg. Inställningen gäller inte automatiskt steg som använder `uses`.

Här körs skriptet genom tolken Bash, så filen behöver vara läsbar men behöver inte ha körbehörighet. Alternativt kan man köra `./my-script.sh` direkt, om filen har körbehörighet och en lämplig shebang-rad som anger tolk. Körbehörighet kan exempelvis sättas med `chmod +x my-script.sh` och sparas i Git.

## Contexts och uttryck

Contexts och uttryck används för att läsa information och styra hur ett workflow körs. Ett context är ett objekt med egenskaper som innehåller information. Ett uttryck använder värdena för att beräkna ett resultat eller kontrollera ett villkor.

### Contexts

Egenskaperna i ett context kan innehålla text, andra värden eller objekt. Genom contexts kan ett workflow läsa information om körningen, jobb, steg, variabler och runnern.

| Context | Innehåll |
|---|---|
| `github` | Information om workflow-körningen, exempelvis händelsen som startade den och aktuell Git-referens. |
| `env` | Miljövariabler som definierats i workflowet, jobbet eller steget. Innehåller inte automatiskt alla variabler i runnerns process. |
| `vars` | Konfigurationsvariabler på organisations-, kodarkivs- eller miljönivå. |
| `job` | Information om det aktuella jobbet. |
| `jobs` | Information om jobb i återanvändbara workflows. Används för att definiera workflowets utdata. |
| `steps` | Information och utdata från avslutade steg i det aktuella jobbet som har ett angivet `id`. |
| `runner` | Information om den runner som kör jobbet. |
| `secrets` | Hemliga värden som är tillgängliga för workflow-körningen. |
| `strategy` | Information om matrisstrategin för det aktuella jobbet. |
| `matrix` | Matrisvärden för den aktuella jobbvarianten, exempelvis operativsystem eller Python-version. |
| `needs` | Resultat och utdata från jobb som angetts som direkta beroenden till det aktuella jobbet. |
| `inputs` | Indata till återanvändbara eller manuellt startade workflows. Finns även i vissa action-sammanhang, exempelvis composite actions. |

En matrisstrategi används för att köra varianter av ett jobb med olika kombinationer av värden. Vilka contexts och egenskaper som är tillgängliga beror på var i workflowet uttrycket används. Alla contexts kan inte användas överallt.

### Contexts jämfört med miljövariabler

GitHub Actions tillhandahåller standardmiljövariabler, exempelvis `GITHUB_REF`. De finns på runnern och kan läsas av kommandon som körs där.

Contexts kan även användas när GitHub behandlar workflowet innan ett jobb skickas till en runner. De kan därför användas för att avgöra om jobbet ska köras alls.

`github.ref` är en egenskap i ett context, medan `$GITHUB_REF` är hur motsvarande miljövariabel läses i Bash. För en push till main är referensen normalt `refs/heads/main`. Andra typer av händelser kan använda andra referenser, exempelvis en särskild merge-referens för en pull request.

### Uttryck, expressions

Ett uttryck kan innehålla fasta värden, referenser till contexts, funktioner och operatorer. Det kan exempelvis tilldela en miljövariabel ett värde eller avgöra om ett jobb eller steg ska köras.

Uttryck skrivs vanligtvis med syntaxen `${{ uttryck }}`. Markeringen anger att GitHub ska utvärdera innehållet.

Exemplet `${{ github.ref == 'refs/heads/main' }}` jämför körningens Git-referens med referensen för main. Resultatet är sant eller falskt.

I ett `if`-villkor kan `${{` och `}}` vanligtvis utelämnas eftersom GitHub redan tolkar värdet som ett uttryck. Om uttrycket börjar med `!` behöver det däremot omslutas med uttrycksmarkeringarna eller hanteras med giltig YAML-citering, eftersom `!` har en särskild betydelse i YAML.

### Fasta värden och sanningsvärden

Fasta värden i uttryck kallas literals. De kan vara booleska värden, `null`, tal eller textsträngar. Booleska värden är `true` och `false`, medan `null` representerar avsaknaden av ett värde.

I ett villkor räknas `false`, `0`, `-0`, tomma strängar och `null` som falskt. Övriga värden räknas som sant. Textsträngen `'false'` är därför sann i ett sådant villkor eftersom den inte är tom. Den är inte samma sak som det booleska värdet `false`.

Textsträngar inne i `${{ ... }}` skrivs med enkla citattecken. Om texten innehåller ett enkelt citattecken skrivs det dubbelt, exempelvis `'It''s open source!'`.

### Operatorer

| Operator | Betydelse | Exempel |
|---|---|---|
| `&&` | Och. Båda villkoren måste vara sanna. | `villkor_a && villkor_b` |
| `\|\|` | Eller. Minst ett villkor måste vara sant. | `villkor_a \|\| villkor_b` |
| `!` | Inte. Vänder villkorets sanningsvärde. | `!villkor` |
| `==` | Lika med. | `github.ref == 'refs/heads/main'` |
| `!=` | Inte lika med. | `github.ref != 'refs/heads/main'` |
| `<` | Mindre än. | `5 < 10` |
| `>` | Större än. | `10 > 5` |
| `<=` | Mindre än eller lika med. | `5 <= 5` |
| `>=` | Större än eller lika med. | `10 >= 5` |

`villkor_a` och `villkor_b` är platshållare för faktiska villkor, inte inbyggda GitHub-variabler. Tabellen beskriver operatorerna när de används i villkor. `&&` och `||` kortsluter utvärderingen och kan även returnera ett operandvärde när de används för att beräkna värden. De returnerar alltså inte alltid bara `true` eller `false`.

Operatorerna kan exempelvis användas för att låta ett steg köras när körningen gäller en viss gren och ytterligare ett villkor är uppfyllt.

### Context-värden som påverkas av användare

Vissa context-värden innehåller användarstyrd information, exempelvis titeln på en pull request. Sådana värden behöver behandlas som opålitliga indata när de används i kommandon.

Om ett värde sätts in direkt i skripttexten med ett uttryck kan texten påverka hur shell-kommandot tolkas. Ett vanligt sätt att minska risken är att föra in värdet genom en miljövariabel och läsa den som citerad data i skriptet. Man behöver fortfarande kontrollera värdet om kommandot använder det som exempelvis en sökväg, ett alternativ eller kod som ska exekveras.
# Från experiment till ett fungerande produktionssystem

## Notebooks och övergången till produktion

Många ML-projekt börjar i notebooks, exempelvis Jupyter Notebook eller notebookmiljöer i Kaggle och Google Colab. Där kan man arbeta i celler med kod, text och resultat. Det gör dem användbara för experiment, utforskning och dokumentation av det man undersöker.

En Jupyter-notebook med filändelsen `.ipynb` lagras som JSON. Om man öppnar filen som vanlig råtext ser man strukturen med nycklar, metadata, celler och sparade resultat. Det kan kännas rörigt att läsa jämfört med en vanlig Python-fil, även om innehållet har en definierad struktur.

Notebooks är särskilt användbara för experiment, men ett lyckat experiment är inte automatiskt ett produktionsklart system. Ett projekt kan fastna i notebookstadiet om man inte tar hand om nästa steg: hur koden ska köras reproducerbart, testas, integreras, driftsättas och övervakas. Notebooks kan ingå i automatiserade arbetsflöden, men behöver då hanteras med samma krav på tillförlitlighet som andra delar av systemet.

## Experimentell utveckling och reproducerbarhet

Maskininlärning är experimentell till sin natur. Man provar olika features, algoritmer, modelleringstekniker och parameterkonfigurationer för att hitta vad som fungerar bäst för problemet. Utmaningen är att hålla reda på vad som fungerade och vad som inte fungerade, samtidigt som arbetet ska vara reproducerbart och koden kunna återanvändas.

Utan spårbarhet kan det vara svårt att förstå varför en viss körning gav ett bra eller dåligt resultat. Det blir också svårt att upprepa experimentet. Därför behöver man dokumentera vilka data, inställningar och versioner som användes. En del av arbetet är att förstå vad som kan gå fel och bygga kontroller som förebygger eller upptäcker problemen.

## Modellen som en svart låda

En svart låda, eller black box, är ett sätt att beskriva ett system utifrån dess indata och utdata utan att behöva beskriva den interna mekanismen. Begreppet används bland annat inom cybernetik, som handlar om styrning och återkoppling. Det är inte begränsat till datorer eller maskininlärning.

När en ML-modell beskrivs som en svart låda betyder det inte att ingen vet något om vad som händer inuti. Algoritmen, modellstrukturen och träningsprocessen kan vara kända, men det kan vara svårt att förklara exakt varför modellen gav en viss prediktion. Hur lätt modellen är att tolka beror också på vilken typ av modell det är.

Vid traditionell felsökning kan man undersöka när ett fel inträffade och sedan gå till loggarna för den tidpunkten. Man kan kontrollera vilka indata som skickades, vilken kod som kördes och vilka felmeddelanden eller stack traces som uppstod.

MLOps bygger motsvarande förutsättningar runt modellen: loggar, versionsinformation, mätvärden och felrapportering. Det gör det möjligt att undersöka både tekniska fel och förändringar i modellens beteende. Man behöver kunna inspektera och förstå systemet runt den svarta lådan, även när modellens interna beslutsprocess är svår att tolka. Loggning öppnar inte automatiskt modellen eller förklarar varje prediktion, men ger underlag för felsökning.

## Versionering och beroenden

För att förstå och återskapa en modellkörning behöver man veta vilken modellversion som användes, hur modellen tränades och vilka hyperparametrar som valdes. Man behöver också känna till vilka datatyper modellen förväntar sig och vilken form eller struktur indatan ska ha. Dessa uppgifter hjälper både vid felsökning och när modellen ska integreras i en applikation.

Programvaruberoenden är också viktiga. Om ett paket uppdateras kan ändrade gränssnitt eller beteenden innebära att koden behöver anpassas eller att kompatibiliteten med en sparad modell behöver kontrolleras. En paketuppdatering innebär inte automatiskt att modellen måste tränas om, men den behöver hanteras så att miljön och resultaten förblir kontrollerbara.

## Data och modellens förändrade beteende

En modell som tränats på historiska data kan få sämre prediktionskvalitet när förhållandena i produktion förändras. De data modellen möter kan skilja sig från träningsdata, eller sambandet mellan indata och det man försöker förutsäga kan förändras. Modellen behöver därför övervakas även när koden fortsätter att köras utan fel.

Det är inte säkert att en modell börjar bete sig märkligt bara för att tiden går, och alla modeller behöver inte tränas om kontinuerligt. Behovet av omträning beror på användningsfallet, förändringar i data och modellens uppmätta kvalitet. Ny träning är en möjlig åtgärd när utvärderingen visar att den behövs.

Data kan hämtas från ett eller flera API:er. Då behöver man hantera tillgången till dessa källor och eventuella beroenden mellan dem. Om ett steg behöver data från ett annat steg måste arbetsflödet ordnas så att rätt data finns tillgängliga vid rätt tidpunkt. Det är en del av orkestreringen.

## Tekniska fel och tysta kvalitetsfel

En modell kan misslyckas tyst, vilket ofta beskrivs som att den ”fails silently”. Den kan fortfarande ta emot indata och returnera prediktioner, trots att prediktionerna blivit sämre eller inte längre är användbara. Det behöver alltså inte uppstå ett undantag eller ett tydligt felmeddelande.

Ett vanligt exekveringsfel i programkod kan däremot ge ett felmeddelande eller en stack trace som visar var körningen avbröts. Även vanlig programvara kan dock ha tysta logiska fel, och ML-system kan också få vanliga exekveringsfel. Skillnaden är därför inte absolut. Poängen är att fungerande kod och en fungerande tjänst inte räcker som bevis för god modellkvalitet.

Man behöver bygga övervakning och kontroller som upptäcker när modellen eller dess indata avviker från det förväntade. Det omfattar både statistik över datan och uppföljning av modellens prediktionskvalitet när den kan mätas. Avvikelser kan leda till notifieringar, vidare undersökning, omträning eller återgång till en tidigare version, en rollback. Vilken åtgärd som är lämplig beror på orsaken; en äldre modell löser inte automatiskt ett problem med förändrade data.

## Testning av ML-system

Ett ML-system behöver vanliga enhetstester och integrationstester, precis som andra mjukvarusystem. Därutöver behöver man validera data och datascheman samt utvärdera och validera de tränade modellerna.

Testningen omfattar alltså både om systemets komponenter fungerar och om modellen håller tillräcklig kvalitet för sitt användningsfall. Det gör testningen mer omfattande än att bara kontrollera att en funktion returnerar ett förväntat värde eller att två tjänster kan kommunicera.

## Driftsättning och infrastruktur

Att produktionssätta ett ML-system innebär att hantera miljöer, hårdvara, nätverk och tillgång till data. En demo eller ett PoC kan visa att en idé fungerar i en avgränsad miljö, medan ett produktionssystem behöver kunna användas och förvaltas under verkliga förhållanden.

Driftsättning kan innebära att en offline-tränad modell görs tillgänglig som en prediktionstjänst. Ett mer automatiserat system kan också behöva en pipeline med flera steg för att träna om, validera och driftsätta modeller. Då måste arbetsmoment som tidigare gjordes manuellt av data scientists kunna köras på ett kontrollerat sätt.

Det är alltså inte ett krav att varje ML-driftsättning omfattar en hel träningspipeline. Skillnaden är att ett ML-system kan behöva hantera både modellen, prediktionstjänsten och processen som producerar kommande modellversioner. Det tillför komplexitet och gör orkestrering, automatisering och övervakning viktiga.

## CI och CD och CT i maskininlärning

Kontinuerlig integration, CI, omfattar versionshantering, automatiserade byggen och kontroller av förändringar. För ett ML-system behöver kontrollerna inte begränsas till kod och komponenter. De kan också omfatta data, datascheman och modeller. Vilka kontroller som körs i CI beror på systemets upplägg och vad som är möjligt och relevant att testa där.

Kontinuerlig leverans, CD, handlar om att hålla testade förändringar redo för leverans. I ett ML-system kan det gälla både programvarupaket, modeller och träningspipelines. I ett automatiserat upplägg kan en levererad träningspipeline i sin tur producera en validerad modell som driftsätts i en prediktionstjänst.

Förkortningen CD används även för continuous deployment, kontinuerlig driftsättning. Då driftsätts godkända förändringar automatiskt till produktion. Vid continuous delivery kan själva produktionsdriftsättningen fortfarande kräva ett manuellt beslut. Det är därför bra att vara tydlig med vilken betydelse som avses.

Kontinuerlig träning, CT, är ett särskilt begrepp inom MLOps och handlar om att automatisera träning eller omträning av modeller. ”Kontinuerlig” betyder här inte att träningen måste pågå utan avbrott. Träningen kan exempelvis startas enligt ett schema, när nya data finns eller när övervakningen visar ett behov. En ny modell behöver valideras innan den ersätter den befintliga modellen i produktion.

## Återkoppling och termostatexemplet

En termostat är ett exempel på en sluten återkopplingsslinga. Man ställer in en önskad temperatur, exempelvis 21 grader. Termostaten jämför den uppmätta temperaturen med inställningen och styr uppvärmningen när det är för kallt. Uppvärmningen påverkar temperaturen, som sedan mäts på nytt.

Resultatet av åtgärden återkommer alltså som ny information till styrningen. Det kallas en feedback loop, eller återkopplingsslinga, och är ett centralt begrepp inom cybernetik.

Samma tanke är användbar för att förstå MLOps-cykeln: modellen används i produktion, resultaten övervakas och informationen kan leda till ändringar eller ny träning. Därefter kan en förbättrad modell valideras och driftsättas. Återkopplingen behöver inte vara lika direkt eller automatisk som i termostaten, men principen hjälper till att beskriva sambandet mellan användning, mätning och förbättring.

## Kursexemplet med cli.py och manifest.json

I kursexemplet beskrivs `cli.py` som ett skript som fungerar som en liten pipeline. Funktionerna anger vad som ska hända när modellen tränas. Enligt anteckningarna ingår också att skapa en `manifest.json` med metadata om körningen, exempelvis vad som kördes och hur det gick.

Syftet med manifestet är att spara information som hjälper till att följa upp körningen och förstå resultatet. När du går igenom exemplet, jämför funktionerna i `cli.py` med innehållet i `manifest.json` för att se vilka uppgifter som faktiskt registreras.

# MLOps och DevOps

## Vad MLOps är och varför det behövs

MLOps står för Machine Learning Operations och handlar om att utveckla, produktionssätta och driva maskininlärningssystem på ett robust, reproducerbart och skalbart sätt. Det omfattar både arbetssätt, samarbete och verktyg. Orkestrering, övervakning och versionering är centrala delar, men hur arbetet organiseras skiljer sig mellan företag.

MLOps liknar DevOps, men behöver också hantera maskininlärningens experimentella natur och beroendet av data. I traditionell programmering skriver man uttryckliga regler för hur programmet ska agera. Vid maskininlärning lär sig modellen i stället mönster från träningsdata inom de ramar som algoritmen och träningsprocessen ger. MLOps är arbetet runt utvecklingen och driften av sådana modeller; det är alltså inte MLOps i sig som ersätter programmerade regler med inlärda mönster.

En stor utmaning när maskininlärningsprojekt ska implementeras och skalas är bristen på en tydlig strategi och att arbetet sker i organisatoriska silos. När olika team arbetar isolerat från varandra blir övergången mellan experiment, modellutveckling och produktion svårare. MLOps hjälper till att knyta ihop dessa delar och att gå från ett proof of concept, PoC, eller ett pilotprojekt till ett fungerande system i produktion.

Det finns flera förutsättningar för att använda maskininlärning effektivt: stora datamängder, tillgång till beräkningsresurser och specialiserade acceleratorer på molnplattformar. Kostnaden för beräkning beror dock på arbetslasten och resurserna; den är inte alltid låg. Utvecklingen inom exempelvis datorseende, språkförståelse, generativ AI och rekommendationssystem ger också fler möjligheter att använda maskininlärning.

Företag investerar därför i data science och maskininlärning för att utveckla modeller som kan skapa verksamhetsvärde för användarna. Att träna en modell som presterar bra på ett separat utvärderingsdataset är en viktig del av arbetet. En ytterligare och ofta större systemutmaning är att bygga ett integrerat ML-system som fortsätter att fungera i produktion.

Själva ML-koden kan vara en liten del av hela systemet. Runt modellen behövs konfiguration, datainsamling, dataverifiering, testning och felsökning, resurshantering, infrastruktur för prediktioner, övervakning, analys och hantering av processer och metadata. Det är dessa delar som gör att modellen kan användas tillförlitligt över tid.

## DevOps som grund

DevOps förenar människor, processer och verktyg för att utveckla och driva programvara och löpande leverera värde till slutanvändarna. Målet är att skapa robusta och reproducerbara applikationer och att göra leveranser snabbare och mer tillförlitliga.

Agil planering kan hjälpa teamet att organisera arbetet och leverera i mindre steg. Versionshantering, eller source control, underlättar samarbete och gör ändringar spårbara. Automatisering minskar behovet av manuellt arbete och kan göra utvecklings- och leveransprocessen snabbare. DevOps kan därmed bidra till kortare utvecklingscykler, snabbare driftsättning och tillförlitliga releaser.

Två centrala begrepp är kontinuerlig integration, CI, och kontinuerlig leverans, CD. Ett ML-system är också ett mjukvarusystem, så samma grundprinciper är användbara när man bygger och driver maskininlärning i större skala.

MLOps tillämpar och anpassar dessa principer till maskininlärningens livscykel. Automatisering och övervakning omfattar integration, testning, releaser, driftsättning och infrastrukturhantering. Målet är att skapa, driftsätta och övervaka robusta och reproducerbara modeller som levererar värde till användarna, exempelvis genom datapipelines eller realtidsapplikationer.

## Modellens livscykel och de två looparna

MLOps ska göra maskininlärningens livscykel skalbar. Den förenklade cykeln i anteckningarna består av sex steg:

1. Träna modellen.
2. Paketera modellen.
3. Validera modellen.
4. Driftsätta modellen.
5. Övervaka modellen.
6. Träna om modellen vid behov.

Det är en översikt över livscykeln, inte en fullständig beskrivning av varje arbetsmoment. Datainsamling, databearbetning och utvärdering ingår också i arbetet som omger stegen.

Arbetet med att utveckla och träna modellen beskrivs ofta som den inre loopen, eller inner loop. Data scientists fokuserar vanligtvis på dataanalys, experiment, val av modell och träning. De undersöker vad som fungerar för det aktuella problemet och gör ändringar utifrån resultaten.

Den yttre loopen, eller outer loop, handlar om att ta den tränade modellen vidare till produktion. Där paketeras, valideras, driftsätts och övervakas modellen. Data scientists kan behöva samarbeta med ML engineers och andra utvecklare eller driftansvariga för att bygga ett system som fungerar i större skala. När övervakningen visar att modellen behöver förbättras eller tränas om återgår arbetet till den inre loopen.

Med ett fungerande MLOps-arbetsflöde kan man övervaka, träna om och driftsätta en ny modellversion samtidigt som en befintlig version används i produktion. För att det ska fungera behöver övergången mellan versionerna vara planerad och kontrollerad.

## Roller och ansvar

Ett ML-projekt behöver flera kompetenser och verktyg. Data scientists och ML-forskare arbetar ofta med utforskande dataanalys, modellutveckling och experiment. Alla har inte samma erfarenhet av att bygga och driva produktionstjänster. Därför behövs samarbete med personer som har kompetens inom mjukvaruutveckling, infrastruktur och drift.

Det betyder inte att data scientists saknar ansvar för robusthet eller reproducerbarhet. Även experiment måste gå att förstå, spåra och så långt som möjligt upprepa. Däremot kan ansvaret för att omvandla ett experiment till en stabil produktionstjänst ligga hos andra roller eller delas mellan flera personer. Gränserna varierar mellan organisationer.

En MLOps-roll kan innebära att ta över en modell som någon annan har utvecklat och sedan ansvara för systemet runt den. Modellen blir då något man ”ärver” och behöver ta hand om. Det kan omfatta automatisering, övervakning, integration, testning, releaser, driftsättning, infrastruktur och förutsättningar för fortsatt träning. MLOps är samtidigt ett gemensamt arbetssätt genom livscykeln, inte bara ett sista överlämningssteg.

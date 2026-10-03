# Git och versionshistorik

## Ångra en commit med revert eller reset

Om en commit redan har pushats och ingår i en historik som andra använder är `git revert` ofta det lämpliga sättet att ångra dess ändringar. Revert skriver inte om den befintliga historiken. I stället skapar kommandot normalt en ny commit som försöker upphäva ändringarna från den valda commiten.

`git reset` kan användas för att flytta den aktuella grenens pekare till en annan commit, exempelvis bakåt i den egna lokala historiken. Hur indexet och arbetskatalogen påverkas beror på vilket reset-läge man använder. Reset används också för att ta bort ändringar från staging utan att flytta grenpekaren när man anger filsökvägar.

Det avgörande är alltså om historiken är delad och vad man vill åstadkomma, inte enbart om commiten har pushats. Att skriva om historik som andra arbetar utifrån kräver samordning.

## Varför rebase kan vara riskabelt på delad historik

Rebase kan skriva om historiken genom att återapplicera commits på en ny bas. De återskapade commitsen får nya identiteter. Om någon annan redan har hämtat den ursprungliga historiken kan personens lokala gren därför avvika från den omskrivna historiken.

Det kan ge problem vid fortsatt samarbete och kan leda till konflikter eller dubblerade ändringar. Det leder däremot inte automatiskt till mergekonflikter varje gång. Om en publicerad gren har skrivits om och man vill ersätta motsvarande historik på fjärrservern kan det krävas en push som tillåter en uppdatering som inte är en fast-forward. Det är en följd av hur historiken ändrats, inte ett sätt att lösa själva kodkonflikten.

Rebase är användbart för att städa den egna feature-grenen innan den delas, exempelvis genom att ordna om eller slå ihop commits. Det är mer riskabelt på historik som andra redan arbetar utifrån.

## Undersöka sparade stashar

Med `git stash list` kan man se vilka stashar som finns. `git stash show` visar en sammanfattning av ändringarna i en stash. Om man vill se själva diffen använder man `git stash show -p`.

Utan en uttrycklig stashreferens visar `git stash show` den senaste stashen. För en annan stash kan man ange dess referens, exempelvis `git stash show -p 'stash@{1}'`. Referenserna hämtas från `git stash list`.

## Varför samma kodändring kan få en ny commit-hash

En commit identifieras inte bara av själva kodändringen. Dess identitet beror på commitobjektets innehåll, bland annat den sparade filstrukturen, föräldercommits, commitmeddelande och metadata om författare och den som skapade commiten.

Detta märks exempelvis vid cherry-pick och rebase. Anta att historiken består av `A → B → C`. Om ändringen från C cherry-pickas till en annan gren skapas normalt en ny commit, C′. C och C′ kan representera samma kodändring, men C′ har en annan förälder och därmed ett annat commitobjekt och en ny hash.

Samma ändring betyder alltså inte att det är samma commit. Commiten anger både ett innehåll och dess plats i historiken.

## Konfliktmarkörer och hur man fortsätter

Vid en vanlig merge kan en konflikt markeras med `<<<<<<< HEAD`, följt av innehållet från den aktuella sidan. Markören `=======` skiljer versionerna åt. Därefter kommer innehållet från den andra sidan och slutmarkören `>>>>>>>`, följd av en grenreferens eller annan identifierare, exempelvis `feature`.

Man behöver bestämma vad slutresultatet ska vara. Det kan innebära att välja en version eller kombinera delar av båda. Det önskade innehållet ska vara kvar och konfliktmarkörerna ska tas bort. Därefter markerar man filen som löst med `git add`.

Nästa kommando beror på vilken operation som pågår. Vid en merge avslutar man normalt med `git commit` eller `git merge --continue`. Vid en rebase fortsätter man med `git rebase --continue`. Vid cherry-pick fortsätter man med `git cherry-pick --continue`.

Under rebase kan beteckningarna ”ours” och ”theirs” kännas omvända jämfört med en vanlig merge. ”Ours” syftar då på historiken som man bygger vidare på, inklusive redan återapplicerade commits, medan ”theirs” syftar på ändringen som just återappliceras. Därför ska man läsa innehållet och sammanhanget i konflikten, inte bara anta att en viss sida alltid är ”min version”.

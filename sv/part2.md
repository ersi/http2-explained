# 2. HTTP idag

HTTP 1.1 har blivit ett protokoll som används för nästan allt på Internet. 
Gigantiska investeringar har gjorts på protokoll och infrastruktur som
utnyttjar detta. Det här är taget till sin spets så att det är idag oftast
lättare att få saker att fungera över HTTP än att bygga något eget nytt. 

## 2.1 HTTP 1.1 är enormt

När HTTP skapades och kastades ut i världen så var det troligtvis sett 
som ett tämligen enkelt och rakt på-protokoll, men tiden har visat att det 
var fel. HTTP 1.0 i RFC 1945 är en 60 sidor lång specifikation som 
släpptes 1996. RFC 2616 som beskriver HTTP 1.1 släpptes endast tre år 
senare i 1999 och har växt signifikant till 176 sidor. När vi inom IETF 
arbetade på uppdateringen till den specifikationen så delades den upp till 
sex dokument, med ett mycket större antal sidor totalt sett (Resultatet 
blev RFC 7230 med familj). Oavsett hur man räknar, så är HTTP 1.1 enormt 
och innehåller en myriad av detaljer, subtiliteter och inte minst en hel 
del valfria tillägg. 

## 2.2 En värld av möjligheter

Eftersom det är i HTTP 1.1s natur att ha många små detaljer och valfria 
tillägg tillgänligt för senare expansion har gjort att ett ekosystem av 
programvara växt fram där nästan ingen implementation någonsin 
implementerar allt och där det egentligen inte riktigt är möjligt att 
exakt definera vad "allt" ens är. Detta har lett till en situation där 
funktioner som initiellt användes lite, 
har sett få implementationer och de som faktiskt implementerade dessa - såg 
väldigt låg användning utav dom.

Senare orsakade detta ett inkompabilitetsproblem då klienter och servrar
började öka användningen av sådanna funktioner. HTTP Pipelining är ett 
utmärkt exempel på en sådan funktion. 

## 2.3 Bristfällig användning av TCP 

HTTP 1.1 har det svårt att på riktigt ta tillvara på den kraft och
prestanda som TCP erbjuder. HTTP-klienter och webbläsare måste vara 
väldigt kreativa för att hitta lösningar på att minsta tiden för sidor 
att ladda.

Andra försök som har pågått parallelt över åren har även som bekräftat 
att TCP inte är särskilt lätt att ersätta och således fortsätter vi att 
arbeta på att förbättra både TCP och protokollen ovanför.

Enkelt sagt kan TCP bli bättre utnyttjat för att undvika pauser eller tid 
som bättre kunde ha använts för att faktiskt skicka eller ta emot mer data. 
De följande avsnitten kommer gå igenom några av dessa tillkortakommandena.

## 2.4 Överföringsstorlek och antal objekt

När man tittar på trender för några av de allra mest populära sidorna på 
dagens webb och kollar på vad som krävs för att ladda deras framsidor 
så dyker ett klart mönster upp. Över åren har mängden data som behöver 
hämtas gradvis stigit upp och över 1,9MB. Vad som är ännu viktigare i den 
här kontexten är att det i genomsnitt krävs över hundra individuella 
resurser för att visa varje sida.

Som grafen nedan visar så har trenden hållt i ett tag och det finns lite till
ingen indikation på att det kommer förändras inom snar framtid. Den visar
tillväxten av den totalla överföringsstorleken (i grönt) och det totala 
antal hämtningar i snitt (i rött) för att ladda de mest populära 
webbsidorna i världen och hur det har förändrats över de senaste fyra
åren.

![transfer size growth](https://raw.githubusercontent.com/bagder/http2-explained/master/images/transfer-size-growth.png) 

## 2.5 Latens dödar

<img style="float: right;" src="https://raw.githubusercontent.com/bagder/http2-explained/master/images/page-load-time-rtt-decreases.png" />

HTTP 1.1 är väldigt latenskänsligt, delvis på grund utav att HTTP
Pipelining fortfarande är i ett sådant dåligt skick att merparten av 
webbläsarna har det avstängt.

Medans vi har sett en stor ökning i tillgänglig bandbredd för folk de 
senaste åren så har vi samtidigt inte sett en liknande nivå av 
förbättring när det gäller minskad latens. Höglatenslänkar, som 
exempelvis merparten utav de nuvarande mobila tekninkerna - gör det 
riktigt svårt att få en bra och snabb webbupplevelse, även om du har 
en länk med väldigt hög bandbredd. 

Ett annat användarfall som verkligen behöver låg latens är vissa sorters
video, såsom videokonferenser, spel och liknande där det inte finns 
förgenererat material att skicka ut. 

## 2.6 "Head of Line"-blockering ("Först i kö"-blockering)

"HTTP Pipelining" är ett sätt att skicka en förfrågan samtidigt som man 
väntar på svar från en tidigare förfrågan. Det är som att vänta vid 
kassan hos en bank eller i en butik. Du vet helt enkelt inte om personen
framför dig är en snabb kund eller en irriterande långsam sådan som 
kommer ta för evigt på sig innan den är klar: "head of line" (eller
"Först i kö")-blockering

<img style="float: right;" src="https://raw.githubusercontent.com/bagder/http2-explained/master/images/head-of-line-blocking.jpg" />

Visst kan du vara försiktig när du väljer kö, så att du väljer den som 
du verkligen tror är den korrekta och ibland kan du till och med starta 
en helt ny kö själv. Men i slutändan så är du ändå tvungen att göra 
ett beslut och när du väl har gjort det kan du inte byta.

Att skapa en ny kö är också associerad med ett visst prestanda- och
resursstraff. Det finns helt enkelt ingen perfekt lösning på problemet.

Ytterligare läsning i det här ämnet kan hittas i till exempel Firefox
ärendehanteringssystem Bugzilla, specifikt [Bugzilla 264354](https://bugzilla.mozilla.org/show_bug.cgi?id=264354).

# LoRaLock

5.	Tutorial
5.1.	Gateway
Om te beginnen moeten we pin 22 en 24 aan elkaar solderen omdat er een fout zit in de LoRa/gps hat 
  
Na deze aan elkaar te solderen kunnen we de hat op de pi monteren. Dit is vrij vanzelfsprekend aangezien er bij de meeste hats zelfs nog extra schroeven en standoffs zijn geleverd.
Eenmaal dit gebeurd is beginnen we met in de “sudo raspi-config”, SPI te activeren in geavanceerde  opties. 
Nu moeten we de wiringpi downloaden en instaleren om ook SPI te kunnen gebruiken met “sudo apt-get install wiringpi” (deze stap kan u al eerder hebben gedaan als u al een project met SPI hebt gebruikt).
Dan moeten we het pakket dat we nodig hebben van github halen gebruik makende van “wget”. De locatie van dit pakket is “https://github.com/tftelkamp/single_chan_pkt_fwd/archive/master.zip”. Eenmaal dit is gedownload kan u het unzippen gebruikmakende van “uzip”. Dit wordt gebruikt als volgt: “uzip master.zip”. 
Door dit te doen wordt er een nieuwe map aangemaakt genaamd “single_chan_pkt_fwd-master”. In deze map moet je het bestand “main.ccp” aanpassen door in de lijn met “#define SERVER1 "54.72.145.119"” het IP-adres aan te passen naar “52.169.76.203” dit IP verwijst naar de thingsnetwork servers.

Eenmaal u dit gedaan hebt, voert u het “make” commando uit terwijl u zich in de “single_chan_pkt_fwd-master” map bevindt (cd). 
Hierna doen we “sudo ./single_chan_pkt_fwd” en krijgen we iets als volgt te zien. Het belangrijkste van deze waarden is de 2de lijn namelijk de gateway ID want dit is een uniek ID dat aan elke gateway gegeven wordt en dit gaan we nodig hebben om de gateway aan te maken op het thingsnetwork.
 
Nu gaan we even weg van de pi en kijken bij gateways in de thingsconsole om onze pi te registreren. Op de site https://console.thethingsnetwork.org/ kunnen we kiezen voor gateways of applications en daar kiezen we voor gateways. Dan staat er een groen plusje met register gateway. Als je hierop klikt kom je op het menu om een nieuwe gateway aan te maken. Het eerste dat je nu moet doen is het onderstaande vakje aanvinken en dan je ID ingeven in het tekstveld hierboven. Voor de rest van de vakjes mag u zelf kiezen wat u erin zet.
  
Eenmaal u dan op geriste gateway hebt geklikt hoort u teruggebracht te worden naar het overzicht van alle gateways en hoort hier nu ook u net aangemaakt gateway bij te staan. Als er bij de gateway een niet verbonden staat kan u altijd eens nakijken of het ip van de things server niet is aangepast of er niets mis is met u internetverbinding.

5.2.	Node
5.2.1.	The things network
Voor we kunnen beginnen met programmeren gaan we eerst naar “the things network” (https://www.thethingsnetwork.org/). Daar kan u navigeren naar de console door op het icoontje in de rechter bovenhoek te klikken met uw naam op. 
Eenmaal in de console kan u kiezen tussen applications en gateway. Hier klikken we op applications. Hier maken we dan een nieuwe applicatie aan door op add application te klikken. Eenmaal u daarop geklikt hebt, hoort u op een pagina terecht te komen die er ongeveer zo uitziet:
  
Hier kan u zelf een “application ID” invullen naar keuze en een beschrijving van deze applicatie. Het enige waar u nu moet op letten is dat u nu de juiste “handler” kiest voor uw regio onderaan. 
Dan wordt u naar deze net aangemaakte applicatie gebracht. Hier vindt u een vak devices waar we naartoe scrollen en op register device klikken.
  
Als we hierop geklikt hebben moeten we een device ID kiezen. Dit is een zelf gekozen naam die we aan het toestel geven. Dan kiezen we een device EUI. Dit mag een volledig zelf bepaalde lijst van 8 nummerparen zijn (bv. 12 34 56 78 90 12 34 56). Van de andere 2 invulvelden moet u zich niks aantrekken.
  
Nu is er een nieuw toestel aangemaakt maar voor we verder kunnen moeten we eerst de standaard instelling van OTAA naar ABP veranderen. Dit kan gedaan worden door op instellingen te klikken en dan onder “activation method” op ABP te klikken. Dan op save onderaan: belangrijk anders worden deze waarden niet opgeslagen. 

5.2.2.	Hardware 
Voor de hardware sluit je de componenten aan zoals te zien in het schema hieronder. Voor een exacte pinout kan u terug kijken naar de tabel in het hoofdstuk pin I/O.

5.2.3.	Code 
Voor we het programma kunnen gebruiken, moeten we eerst de juiste bibliotheken van het Arduino netwerk halen. Deze kan u gewoon vinden in de Arduino library manager. 
Als u deze dan geïnstalleerd hebt, kunnen we met de code beginnen werken. Hiervoor kan u gewoon de code copiëren die ik in de bijlage heb toegevoegd. Het enige dat u nog in deze code moet vervangen zijn NWKSKEY,  APPSKEY en DEVADDR. Deze kan u terugvinden in the things netwerk. Bij het toestel dat u net heeft aangemaakt zal u in het overview deze veldjes zien die u gaat moeten kopiëren en in de code plakken (tip: door op <> te klikken is de opmaak al correct voor in de code en het klembordje op het einde kopieert het al voor u)
 
 
In de code gaan we achter: “static const PROGMEM u1_t NWKSKEY[16] =” de waarde plaatsen die in het veld “Network Session Key” staat.
Dan in de lijn: “static const u1_t PROGMEM APPSKEY[16] = “zetten we de “App Session Key”
en in de: “static const u4_t DEVADDR = 0x” zetten we het device adres. Vergeet zeker ook niet achter elke lijn de “;” terug te zetten en voor de “devaddr” de 0x te laten staan.
Eenmaal u de code heeft geüpload en de Arduino dicht genoeg bij een gateway in de buurt is, horen de informatie verstuurd te zijn. Deze kan u dan terugvinden op “the things site” onder het toestel in het veld data.

5.2.4.	Cayenne
Om een Cayenne op te zetten gaan we eerst nog verder op “the things site” en gaan we in onze application. Hier klikken we op payload format waar we een drop down menu vinden dat je moet omzetten naar “CayenneLPP”. Hierna gaan we naar het integrations menu en klikken hier op add integration en dan op my devices. Als dit gebeurd is, gaan we naar de cayenne.mydevices.com/ site. Hier maken we een account aan. Op dit account moet je een nieuw toestel aanmaken door te klikken op de ”add new” knop en dan op “device/Widget”. Dan komt er een hele lange lijst met alle mogelijke toestellen. Aan de zijkant gaan we dan filteren op LoRa en dan op the things network.
   
Aan de rechterkant moet je beginnen scrollen door de lijst tot je Cayenne LPP tegenkomt. 
Als je hier dan op klikt, krijg je aan de rechterkant dit menu: 
 
Hier kunnen we een naam kiezen voor ons toestel. En in het veld devEUI vul je het device EUI in dat je kan terugvinden onder je device op the things site. Hierna zullen de data die je verstuurt automatisch verschijnen in het nu aangemaakte toestel.

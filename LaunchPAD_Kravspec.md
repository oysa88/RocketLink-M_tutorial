# LaunchPAD

### @diffs true

## Del 1: @unplugged

### Programmere LaunchPAD

I denne veiledningen skal vi gå igjennom hvordan man programmerer LaunchPAD-kofferten til RocketLink oppskytningssystem. 

Veiledningen er delt i 3 deler:
- Må-krav             (Absolutt nødvendig funksjonalitet for at systemet skal fungere trygt)
- Bør-krav            (Gir økt sikkerhet og en bedre opplevelse)
- «Kjekt-å-ha»-krav   (For fine-schmeckere som ønsker maks ut av RocketLink)

Vi kommer til å hoppe mellom veiledningen for å programmere ControllerPAD og LaunchPAD. Du får beskjed når du skal bytte mellom veiledningene og når du skal teste koden underveis.

Lykke til!

![Launch-PAD.jpg](https://i.postimg.cc/Sxhw0Mck/Launch-PAD.jpg)


## Del 1.1: 

### Må: Ved start - radio

I blokken ``||basic: ved start||`` skal vi sette opp de funksjonene som kun skal brukes når kofferten skrus på.

Det første vi skal sette opp er en ``||radio: radiogruppe||``. (Vi skal bruke radiogruppe nr. 1 på koffertene.)

Sett også opp at ``||radio: sendereffekt||`` skal være lik 7. (Gir oss sterkere radiosender.)

```blocks
radio.setGroup(1)
radio.setTransmitPower(7)
```

## Del 1.2:

### Må: Sjekke ArmStatus på kofferten

Inni ``||basic: Gjenta for alltid||``:

Sett opp en ``||logic: hvis-betingelse ||`` som sjekker om Arm Switch (``||pins: les digitalverdi P1 ||``) er 1. 

Hvis ``||pins: P1 = 1 ||``, sett variabelen ``||variables: ArmStatus ||`` til ``||logic: sann||``, ellers sett ``||variables: ArmStatus ||`` til ``||logic: usann||``.

```blocks
basic.forever(function on_forever() {
    if (pins.digitalReadPin(DigitalPin.P1) == 1) {
        ArmStatus = true
    } else {
        ArmStatus = false
    } 
})
```

## Del 1.2:

### Må: Skyte opp raketten!

Raketten skal sendes opp når ``||radio: radio mottar receivedNumber = 42 ||``.

Får å få til dette, må vi lage en ``||logic: hvis-betingelse ||`` inni ``||radio: radio mottar receivedNumber ||``. Her skal vi vi sjekke om ``||variables: receivedNumber||`` = 42. Hvis den er det, skal vi sette en ny variable vi kaller ``||variables: Oppskytning||`` til ``||logic: sann||``.

Så, inne i ``||logic: gjenta for alltid||`` skal vi kalle opp den nye funksjonen ``||functions: Launch ||``, hvis ``||variables: Oppskytning||`` er ``||logic: sann||``.

```blocks
radio.onReceivedNumber(function (receivedNumber) {
    if (receivedNumber == 42) {
        Oppskytning = true
    }
})
basic.forever(function on_forever() {
    if (pins.digitalReadPin(DigitalPin.P1) == 1) {
        ArmStatus = true
    } else {
        ArmStatus = false
    } 
    if (Oppskytning) {
        Launch()
    } 
})
function Launch () { 
    
}
```

## Må: Del 1.3:

### Oppskytningssekvensen

Inni ``||functions: Launch ||``:

Start med å sette ``||variables: Oppskytning ||`` til ``||logic: usann ||``

Oppskytningssekvensen får kun lov til å bli utført ``||logic: hvis ||`` ``||variables: ArmStatus ||`` er ``||logic: sann ||``.

Så ``||logic: hvis ||`` ``||variables: ArmStatus ||`` er ``||logic: sann ||``: 

- ``||pins: Skriv digital til P16 ||`` til HØY (1)
- Lag en ``||basic: pause ||`` på 500ms.
- ``||pins: Skriv digital til P16 ||`` til LAV (0)

```blocks
function Launch () { 
    Oppskytning = false
    if (ArmStatus) {
        pins.digitalWritePin(DigitalPin.P16, 1)
        basic.pause(500)
        pins.digitalWritePin(DigitalPin.P16, 0)
    }
}
```

## Del 1.4:

### Må: Rearmere kofferten før ny oppskytning

Kofferten får ikke skyte opp en ny rakett før den er rearmert. (Armeringsbryteren må skrus AV.)

Så for å "låse" kofferten etter at Launch-kommandoen er sendt, bruk en ``||loops: gjenta hvis sann ||``, og sett rearm-LED ``||pins: skriv digital til P8 ||`` til å blinke så lenge ``||pins: les digital P1 = 0 ||``.

Bruk en ``||basic: pause ||`` på 500 ms mellom hver gang ``||pins: P8 ||`` er 1 og 0.


```blocks
function Launch () { 
    Oppskytning = false
    if (ArmStatus) {
        pins.digitalWritePin(DigitalPin.P16, 1)
        basic.pause(500)
        pins.digitalWritePin(DigitalPin.P16, 0)
        while (pins.digitalReadPin(DigitalPin.P1) == 1) {
            pins.digitalWritePin(DigitalPin.P8, 1)
            basic.pause(500)
            pins.digitalWritePin(DigitalPin.P8, 0)
            basic.pause(500)
        }
    }
}
```

## Del 1.5:

### Teste Oppskytningssekvensen!

``||math: Last ned||`` koden til begge koffertene og sjekk at oppstartsekvensen fungerer som den skal.


## Del 1.6: @unplugged

### Bytte Tutorial

Gratulerer! Vi er nå ferdige med å lage MÅ-koden på begge koffertene!

Bytt veiledning, og fortsett å lage koden til ControllerPAD!

![Controller-PAD.jpg](https://i.postimg.cc/VLM3HRrK/Controller-PAD.jpg)


## Del 2.1:

### Bør: Sette opp lysene på kofferten

For å vise status på systemene på kofferten, bruker vi NeoPixels. Det er koblet til 4 NeoPixels på ``||pins: digital pin P0||``. (I menyen ``||neopixel: Neopixel||`` finner du ``||variables: sett strip til||`` ``||neopixel: NeoPixel at pin P0 with 4 leds||``.)

Skriv også at ``||pins: digital pin P15 = 1||``. Dette er lyset på Status Check-knappen på kofferten.

```blocks
radio.setGroup(1)
radio.setTransmitPower(7)
let strip = neopixel.create(DigitalPin.P0, 4, NeoPixelMode.RGB)
pins.digitalWritePin(DigitalPin.P15, 1)
```

## Del 2.2:

### Bør: SelfStatus

Øverst i ``||basic: gjenta for alltid||`` skal vi sette en ny variabel ``||variables: SelfStatus||`` til ``||logic: sann||``. (Kofferten er jo på...)

Vi skal også legge inn en liten ``||basic: pause||`` på 100ms i bunn av ``||basic: gjenta for alltid||`` for å gi koden litt pusterom.


```blocks
basic.forever(function on_forever() {
    SelfStatus = true
    if (pins.digitalReadPin(DigitalPin.P1) == 1) {
        ArmStatus = true
    } else {
        ArmStatus = false
    } 
    if (Oppskytning) {
        Launch()
    } 
    basic.pause(100)
})
function Launch () { 
    Oppskytning = false
    if (ArmStatus) {
        pins.digitalWritePin(DigitalPin.P16, 1)
        basic.pause(500)
        pins.digitalWritePin(DigitalPin.P16, 0)
        while (pins.digitalReadPin(DigitalPin.P1) == 1) {
            pins.digitalWritePin(DigitalPin.P8, 1)
            basic.pause(500)
            pins.digitalWritePin(DigitalPin.P8, 0)
            basic.pause(500)
        }
    }
}
```

## Del 2.3: @unplugged

### Bør: Sjekke Linkstatus mellom rakettkoffertene

For å kunne sjekke link mellom koffertene må bruke noe som kalles for «active sensing». 

Begge koffertene sender en radiomelding mellom seg med gitte intervaller som sier «Hei, jeg er ikke avskrudd». Men vi trenger noe som sier ifra hvis det har gått for lang tid siden sist vi fikk en radiomelding fra den andre kofferten.

![Radio-mellom-rakettkoffertene.gif](https://i.postimg.cc/nL4Rtr4R/Radio-mellom-rakettkoffertene.gif)


## Del 2.4: 

### Bør: Lage en oppdateringsfrekvens

Lag en variabel som du kaller ``||variables: Oppdateringsfrekvens||``. Sett den inn under  ``||basic: ved start||``, og la den være 200ms.

```blocks
radio.setGroup(1)
radio.setTransmitPower(7)
let strip = neopixel.create(DigitalPin.P0, 4, NeoPixelMode.RGB)
pins.digitalWritePin(DigitalPin.P15, 1)
let Oppdateringsfrekvens = 200
```

## Del 2.5: 

### Bør: Motta radio-melding fra LaunchPAD

Lag en ny variabel: ``||variables: sistSettAktiv||``. 

Inni en ``||radio:når radio mottar receivedNumber||``: Hver gang vi mottar ``||radio: receivedNumber = 11||``,sett ``||variables: LinkStatus||`` til ``||logic: sann||`` og ``||variables: sistSettAktiv||`` til ``||input: kjøretid (ms)||``. 

I en micro:bit, kjører det en intern klokke som heter ``||input: kjøretid (ms)||``. Ved å sette ``||variables: sistSettAktiv||`` lik ``||input: kjøretid (ms)||`` ved gitte intervaller, kan vi lett se om tidsforskjellen mellom ``||variables: sistSettAktiv||`` og ``||input: kjøretid (ms)||`` blir større enn 3x gitt tidsinterval.

```blocks
radio.onReceivedNumber(function (receivedNumber) {
    if (receivedNumber == 42){
        Oppskytning = true
    }
    if (receivedNumber == 11){
        LinkStatus = true
        sistSettAktiv = input.runningTime()
    }
})
```

## Del 2.6: 

### Bør: Sjekke om den andre kofferten er skrudd av

For å finne ut om kofferten har sluttet å motta signal fra den andre kofferten, må vi lage en liten sjekk som vi setter inn i  ``||control: kjør i bakgrunnen||``. (Finner du i menuen: Styring)

Inni her skal vi bruke en  ``||loops: gjenta hvis sann-løkke||``. Denne løkken vil kjøre så lenge kofferten er på.

Inni ``||loops: gjenta hvis sann||`` skal vi send tallet 11 med radio. 

Nå skal vi sjekke om den ene av koffertene ikke mottar et signal på 3x ``||variables: Oppdateringsfrekvens||``. Hvis den er lengre enn det, kan den regne med at den andre kofferten er skrudd av.

Vi skal sjekke om ``||input: kjøretid (ms)||`` minus (-) ``||variables: sistSettAktiv||`` er større enn (>) 3x ``||variables: Oppdateringsfrekvens||``

Hvis dette er sant, skal: 

-  ``||variables: LinkStatus||`` settes til ``||logic: usann||``

Utenfor ``||logic: hvis-betingelsen||``, avslutt med en ``||basic: pause||`` lik ``||variables: Oppdateringsfrekvens||``.


```blocks
control.inBackground(function () {
    while (true) {
        radio.sendNumber(11)
        if (input.runningTime() - sistSettAktiv > 3 * Oppdateringsfrekvens) {
            LinkStatus = false
        }
    basic.pause(oppdateringsfrekvens)
    }
})
```

## Del 2.7:

### Bør: Teste om igniter er koblet til rakett

Inne i ``||basic: Gjenta for alltid||`` skal vi teste om igniteren er koblet riktig til raketten.

For å sjekke dette, må vi trykker ned knappen til System Status Check på kofferten. Når knappen trykkes ned, settes ``||pins: digital pin P5 ||`` til 0.

Inni ``||logic: hvis||`` ``||pins: digital pin P5 ||`` = 0:

- Start med å sette alle NeoPixelene til ``||neopixel: rød||``.
- For å kunne lese av ``||pins: digital pin P2||`` (Ignitersjekk), må vi skrive ``||pins: digital pin P14||`` (Ignitersjekk-sikkerhet) til HØY (1) i 200 ms før den skrives til LAV (0) igjen. Mens ``||pins: P14||`` er HØY skal vi lese av ``||pins: digital pin P2||``. 

Så hvis ``||pins: digital pin P2||`` er HØY (1), skal variabelen ``||variables: IgniterStatus||`` settes til ``||logic: sann||``. Ellers skal den settes til ``||logic: usann||``.


```blocks
let strip: neopixel.Strip = null
basic.forever(function on_forever() {
    SelfStatus = true
    if (pins.digitalReadPin(DigitalPin.P1) == 1) {
        ArmStatus = true
    } else {
        ArmStatus = false
    }
    if (pins.digitalReadPin(DigitalPin.P5) == 0) {
        strip.showColor(neopixel.colors(NeoPixelColors.Red))
        pins.digitalWritePin(DigitalPin.P14, 1)
        basic.pause(200)
        if (pins.digitalReadPin(DigitalPin.P2) == 1) {
            IgniterStatus = true
            }
        else {
            IgniterStatus = false
            }
        pins.digitalWritePin(DigitalPin.P14, 0)
    }
    if (Oppskytning) {
        Launch()
    } 
    basic.pause(100)
})
function Launch () {
    Oppskytning = false 
    if (ArmStatus) {
        pins.digitalWritePin(DigitalPin.P16, 1)
        basic.pause(500)
        pins.digitalWritePin(DigitalPin.P16, 0)
        while (pins.digitalReadPin(DigitalPin.P1) == 1) {
            pins.digitalWritePin(DigitalPin.P8, 1)
            basic.pause(500)
            pins.digitalWritePin(DigitalPin.P8, 0)
            basic.pause(500)
        }
    }
}
```

## Del 2.8:

### Bør: Sette opp status på NeoPixelene

Vi skal bruke en ny funksjon, ``||functions: NeoPixels||``, for å vise hvilken status de forskjellige systemene på kofferten har. 

Vi skal inidividuelt sjekke opp variablene: ``||variables: SelfStatus||``, ``||variables: LinkStatus||``, ``||variables: IgniterStatus||`` og ``||variables: ArmStatus||``.

Lag 4 ``||logic: Hvis-betingelser||`` med ``||logic: ellers||``, en for hver variabel. Hvis den er ``||logic: sann||``, skal Neopixel settes til grønn, ellers skal den være rød.

Avslutt med å vise lysene: ``||neopixel: show||``

Kjør ``||functions: NeoPixels||`` fra nederst i ``||basic: gjenta for alltid||``.

| Variabel ||||| NeoPixel nr. |
|:---------|||||:------:|
| SelfStatus ||||| 0 |
| LinkStatus ||||| 1 |
| IgniterStatus ||||| 2 |
| ArmStatus ||||| 3 |


```blocks
let strip: neopixel.Strip = null
function NeoPixels () {
    if (SelfStatus) {
        strip.setPixelColor(0, neopixel.colors(NeoPixelColors.Green))
    } else {
        strip.setPixelColor(0, neopixel.colors(NeoPixelColors.Red))
    }
    if (LinkStatus) {
        strip.setPixelColor(1, neopixel.colors(NeoPixelColors.Green))
    } else {
        strip.setPixelColor(1, neopixel.colors(NeoPixelColors.Red))
    }
    if (IgniterStatus) {
        strip.setPixelColor(2, neopixel.colors(NeoPixelColors.Green))
    } else {
        strip.setPixelColor(2, neopixel.colors(NeoPixelColors.Red))
    }
    if (ArmStatus) {
        strip.setPixelColor(3, neopixel.colors(NeoPixelColors.Green))
    } else {
        strip.setPixelColor(3, neopixel.colors(NeoPixelColors.Red))
    }
    strip.show()
}
basic.forever(function on_forever() {
    SelfStatus = true
    if (pins.digitalReadPin(DigitalPin.P1) == 1) {
        ArmStatus = true
    } else {
        ArmStatus = false
    }
    if (pins.digitalReadPin(DigitalPin.P5) == 0) {
        strip.showColor(neopixel.colors(NeoPixelColors.Red))
        pins.digitalWritePin(DigitalPin.P14, 1)
        basic.pause(200)
        if (pins.digitalReadPin(DigitalPin.P2) == 1) {
            IgniterStatus = true
            }
        else {
            IgniterStatus = false
            }
        pins.digitalWritePin(DigitalPin.P14, 0)
    }
    if (Oppskytning) {
        Launch()
    } 
    NeoPixels()
    basic.pause(100)
})
function Launch () { 
    Oppskytning = false
    if (ArmStatus) {
        pins.digitalWritePin(DigitalPin.P16, 1)
        basic.pause(500)
        pins.digitalWritePin(DigitalPin.P16, 0)
        while (pins.digitalReadPin(DigitalPin.P1) == 1) {
            pins.digitalWritePin(DigitalPin.P8, 1)
            basic.pause(500)
            pins.digitalWritePin(DigitalPin.P8, 0)
            basic.pause(500)
        }
    }
}
```

## Del 2.9:

### Teste Bør-koden til begge koffertene

``||math: Last ned||`` koden til begge koffertene på hver sin micro:bit. Test at alle funksjonene du har laget fungerer som den skal!


## Del 3: @unplugged

### Kjekt å ha-krav til rakettkoffertene

Sikkerheten og brukervennligheten til rakettkoffertene er ganske bra nå! Men for å få fullt utbytte av koffertene, skal vi legge til noen få funksjoner til i koden vår.

![Radio-mellom-rakettkoffertene.gif](https://i.postimg.cc/nL4Rtr4R/Radio-mellom-rakettkoffertene.gif)



## Del 3.1:

### Kjekt-å-ha: Sende status på Igniter og Arm til ControllerPAD

Vi skal sende status til ``||variables: IgniterStatus||`` og ``||variables: ArmStatus||`` over til ControllerPAD. Dette skal gjøres i ``||basic: gjenta for alltid||``:

- ``||logic: Hvis||`` ``||variables: IgniterStatus||`` er ``||logic: sann||``, skal vi sende en ``||radio: radio send tall = 21||`` til ControllerPAD. Ellers skal vi skal sende en ``||radio: radio send tall = 22||`` til ControllerPAD.
- Inni der vi sjekker om ``||pins: P1||`` er lik 1, skal vi sende en ``||radio: radio send tall = 31||`` til ControllerPAD når ``||variables: ArmStatus||`` er ``||logic: sann||``. Ellers skal vi sende en ``||radio: radio send tall = 32||`` til ControllerPAD når ``||variables: ArmStatus||`` er ``||logic: usann||``.


```blocks
basic.forever(function on_forever() {
    SelfStatus = true
    if (pins.digitalReadPin(DigitalPin.P1) == 1) {
        ArmStatus = true
        radio.sendNumber(21)
    } else {
        ArmStatus = false
        radio.sendNumber(22)
    }
    if (pins.digitalReadPin(DigitalPin.P5) == 0) {
        strip.showColor(neopixel.colors(NeoPixelColors.Red))
        pins.digitalWritePin(DigitalPin.P14, 1)
        basic.pause(200)
        if (pins.digitalReadPin(DigitalPin.P2) == 1) {
            IgniterStatus = true
            }
        else {
            IgniterStatus = false
            }
        pins.digitalWritePin(DigitalPin.P14, 0)
    }
    if (IgniterStatus) {
        radio.sendNumber(21)
    } else {
        radio.sendNumber(22)
    }
    if (Oppskytning) {
        Launch()
    } 
    NeoPixels()
    basic.pause(100)
})
function Launch () { 
    Oppskytning = false
    if (ArmStatus) {
        pins.digitalWritePin(DigitalPin.P16, 1)
        basic.pause(500)
        pins.digitalWritePin(DigitalPin.P16, 0)
        while (pins.digitalReadPin(DigitalPin.P1) == 1) {
            pins.digitalWritePin(DigitalPin.P8, 1)
            basic.pause(500)
            pins.digitalWritePin(DigitalPin.P8, 0)
            basic.pause(500)
        }
    }
}
let strip: neopixel.Strip = null
function NeoPixels () {
    if (SelfStatus) {
        strip.setPixelColor(0, neopixel.colors(NeoPixelColors.Green))
    } else {
        strip.setPixelColor(0, neopixel.colors(NeoPixelColors.Red))
    }
    if (LinkStatus) {
        strip.setPixelColor(1, neopixel.colors(NeoPixelColors.Green))
    } else {
        strip.setPixelColor(1, neopixel.colors(NeoPixelColors.Red))
    }
    if (IgniterStatus) {
        strip.setPixelColor(2, neopixel.colors(NeoPixelColors.Green))
    } else {
        strip.setPixelColor(2, neopixel.colors(NeoPixelColors.Red))
    }
    if (ArmStatus) {
        strip.setPixelColor(3, neopixel.colors(NeoPixelColors.Green))
    } else {
        strip.setPixelColor(3, neopixel.colors(NeoPixelColors.Red))
    }
    strip.show()
}
```

## Del 3.2:

### Kjekt-å-ha: Tillatt kun oppskytning hvis alle systemene fungerer

For å sjekke om alle systemene er klare for oppskytning, må vi lage en ``||logic: Hvis-betingelse||`` som sjekker om alle statusene vår er lik ``||logic: sann||``. Den skal settes inn i ``||basic: gjenta for alltid||``.

Vi skal sjekke: ``||variables: SelfStatus||``, ``||variables: LinkStatus||``, ``||variables: IgniterStatus||`` og ``||variables: ArmStatus||``.

Hvis alle statusene over er lik ``||logic: sann||``, da skal den nye variabelen ``||variables: Klar||`` settes lik ``||logic: sann||``. Ellers settes lik ``||logic: usann||``. Legg ved et bilde på skjermen til micro:biten for hvert at tilfellene som viser om ``||variables: Klar||`` er ``||logic: sann||`` eller ikke.

Vi må også endre i funksjonen ``||functions: Launch ||`` at raketten kun får bli skutt opp hvis ``||variables: Klar||`` er ``||logic: sann||``.

```blocks
basic.forever(function on_forever() {
    SelfStatus = true
    if (pins.digitalReadPin(DigitalPin.P1) == 1) {
        ArmStatus = true
        radio.sendNumber(21)
    } else {
        ArmStatus = false
        radio.sendNumber(22)
    }
    if (pins.digitalReadPin(DigitalPin.P5) == 0) {
        strip.showColor(neopixel.colors(NeoPixelColors.Red))
        pins.digitalWritePin(DigitalPin.P14, 1)
        basic.pause(200)
        if (pins.digitalReadPin(DigitalPin.P2) == 1) {
            IgniterStatus = true
            }
        else {
            IgniterStatus = false
            }
        pins.digitalWritePin(DigitalPin.P14, 0)
    }
    if (IgniterStatus) {
        radio.sendNumber(21)
    } else {
        radio.sendNumber(22)
    }
    if (SelvStatus && LinkStatus && IgniterStatus && ArmStatus) {
        Klar = true
        basic.showLeds(`
            # . . . #
            . # . # .
            . . # . .
            . # . # .
            # . . . #
            `)
    } else {
        Klar = false
        basic.showLeds(`
            # . . . #
            # # . # #
            # . # . #
            # . . . #
            # . . . #
            `)	
    }
    if (Oppskytning) {
        Launch()
    } 
    NeoPixels()
    basic.pause(100)
})
function Launch () { 
    Oppskytning = false
    if (Klar) {
        pins.digitalWritePin(DigitalPin.P16, 1)
        basic.pause(500)
        pins.digitalWritePin(DigitalPin.P16, 0)
        while (pins.digitalReadPin(DigitalPin.P1) == 1) {
            pins.digitalWritePin(DigitalPin.P8, 1)
            basic.pause(500)
            pins.digitalWritePin(DigitalPin.P8, 0)
            basic.pause(500)
        }
    }
}
let strip: neopixel.Strip = null
function NeoPixels () {
    if (SelfStatus) {
        strip.setPixelColor(0, neopixel.colors(NeoPixelColors.Green))
    } else {
        strip.setPixelColor(0, neopixel.colors(NeoPixelColors.Red))
    }
    if (LinkStatus) {
        strip.setPixelColor(1, neopixel.colors(NeoPixelColors.Green))
    } else {
        strip.setPixelColor(1, neopixel.colors(NeoPixelColors.Red))
    }
    if (IgniterStatus) {
        strip.setPixelColor(2, neopixel.colors(NeoPixelColors.Green))
    } else {
        strip.setPixelColor(2, neopixel.colors(NeoPixelColors.Red))
    }
    if (ArmStatus) {
        strip.setPixelColor(3, neopixel.colors(NeoPixelColors.Green))
    } else {
        strip.setPixelColor(3, neopixel.colors(NeoPixelColors.Red))
    }
    strip.show()
}
```

## Del 3.3:

### Kjekt-å-ha: Forbedre visualisering av Launch

Vis på skjermen til micro:biten når raketten blir skutt opp. Bruk en ``||basic: vis skjerm||`` for å gjøre dette. 

```blocks
function Launch () { 
    Oppskytning = false
    if (Klar) {
        basic.showLeds(`
            . . # . .
            . # # # .
            # . # . #
            . . # . .
            . . # . .
            `)
        pins.digitalWritePin(DigitalPin.P16, 1)
        basic.pause(500)
        pins.digitalWritePin(DigitalPin.P16, 0)
        while (pins.digitalReadPin(DigitalPin.P1) == 1) {
            pins.digitalWritePin(DigitalPin.P8, 1)
            basic.pause(500)
            pins.digitalWritePin(DigitalPin.P8, 0)
            basic.pause(500)
        }
    }
}
```


## Del 3.4:

### Kjekt-å-ha: Forbedre visualisering av rearm

For å tydeligere vise at kofferten er låst i rearm, skal vi skru av neopixel-lysene på kofferten.

Dette gjør vi ved å bruke blokkene ``||neopixel: strip clear||`` og så ``||neopixel: strip show||``. Disse skal plasseres rett etter at raketten er skutt opp og før koden går inn i ``||loops: gjenta hvis sann||``.

Vi skal også få skjermen til microbiten til å blinke med å bruke to ``||basic: vis skjerm ||``, som ser forskjellig ut. Disse skal blinke så lenge ``||pins: les digital P1 = 1 ||``. Fjern også ``||basic: pausene||`` inne i ``||loops: gjenta hvis sann||``.

```blocks
function Launch () { 
    Oppskytning = false
    if (Klar) {
        basic.showLeds(`
            . . # . .
            . # # # .
            # . # . #
            . . # . .
            . . # . .
            `)
        pins.digitalWritePin(DigitalPin.P16, 1)
        basic.pause(500)
        pins.digitalWritePin(DigitalPin.P16, 0)
        strip.clear()
        strip.show()
        while (pins.digitalReadPin(DigitalPin.P1) == 1) {
            pins.digitalWritePin(DigitalPin.P8, 1)
            basic.showLeds(`
            . . . . .
            . # # # .
            . # # # .
            . # # # .
            . . . . .
            `)
            pins.digitalWritePin(DigitalPin.P8, 0)
            basic.showLeds(`
            . . . . .
            . # # # .
            . # . # .
            . # # # .
            . . . . .
            `)
        }
    }
}
let strip: neopixel.Strip = null
```


## Del 3.5:

### Kjekt-å-ha: Oppsett og reset av kofferten

Vi skal lage en funksjon for kofferten som heter ``||functions: Initialize||``. 

Funksjonen skal kjøres hver gang vi skrur på LaunchPAD og hver gang vi rearmerer kofferten etter en oppskytning. 

``||functions: Initialize||`` sin oppgave er å nullstille rakettkofferten slik at alle systemer stilles tilbake eller skrus av.

``||functions: Initialize||`` skal kalles opp både fra brunn av ``||basic: ved start||`` og i bunn av ``||functions: Launch||`` (nederst inne i ``||logic: hvis-betingelsen||``).

```blocks
radio.setGroup(1)
radio.setTransmitPower(7)
pins.digitalWritePin(DigitalPin.P15, 1)
let strip = neopixel.create(DigitalPin.P0, 4, NeoPixelMode.RGB)
Initialize()
function Initialize () {
	
}
function Launch () { 
    Oppskytning = false
    if (Klar) {
        basic.showLeds(`
            . . # . .
            . # # # .
            # . # . #
            . . # . .
            . . # . .
            `)
        pins.digitalWritePin(DigitalPin.P16, 1)
        basic.pause(500)
        pins.digitalWritePin(DigitalPin.P16, 0)
        strip.clear()
        strip.show()
        while (pins.digitalReadPin(DigitalPin.P1) == 1) {
            pins.digitalWritePin(DigitalPin.P8, 1)
            basic.showLeds(`
            . . . . .
            . # # # .
            . # # # .
            . # # # .
            . . . . .
            `)
            pins.digitalWritePin(DigitalPin.P8, 0)
            basic.showLeds(`
            . . . . .
            . # # # .
            . # . # .
            . # # # .
            . . . . .
            `)
        }
        Initialize()
    }
}
```

## Del 3.6: 

### Kjekt-å-ha: Initialize funksjon

Det første vi må gjøre inne i ``||functions: Initialize||`` er å sette å sette alle variablene til ``||logic: usann||``: ``||variables: SelfStatus||``, ``||variables: LinkStatus||``, ``||variables: IgniterStatus||``, ``||variables: ArmStatus||`` og ``||variables: Klar||``.

Videre inne i ``||functions: Initialize||`` skal vi teste lysene på NeoPixel strip. Sett alle lysene til å lyse lilla (``||neopixel: Purple||``).


```blocks
let strip: neopixel.Strip = null
function Initialize () {
	SelfStatus = false
    LinkStatus = false
    IgniterStatus = false
    ArmStatus = false
    Klar = false
    strip.showColor(neopixel.colors(NeoPixelColors.Purple))
}
```


## Del 3.7: 

### Kjekt-å-ha: Initialize - animasjon

Vi lager en liten animasjon på skjermen til micro:bit under oppstartsekvensen. Du kan velge selv hvordan den skal se ut. (Se hint: Dette er kun et forslag.)

Etter animasjonen skal alle lysene settes til å lyse Rødt (``||neopixel: Rød||``)

Avslutt med en liten ``||basic: pause||`` på 200 ms.

```blocks
let strip: neopixel.Strip = null
function Initialize () {
	SelfStatus = false
    LinkStatus = false
    IgniterStatus = false
    ArmStatus = false
    Klar = false
    strip.showColor(neopixel.colors(NeoPixelColors.Purple)) 
    basic.showLeds(`
    . . . . .
    . . . . .
    . . # . .
    . . . . .
    . . . . .
    `)
    basic.showLeds(`
    . . . . .
    . # # # .
    . # . # .
    . # # # .
    . . . . .
    `)
    basic.showLeds(`
    # # # # #
    # . . . #
    # . . . #
    # . . . #
    # # # # #
    `)
    basic.showLeds(`
    # . . . #
    # # . # #
    # . # . #
    # . . . #
    # . . . #
    `)
    strip.showColor(neopixel.colors(NeoPixelColors.Red))
    basic.pause(200)
}
```

## Del 3.8: @unplugged

### Bytte Tutorial

Gratulerer! Vi er nå ferdige med koden til LaunchPAD!

Bytt veiledning, og fullfør koden til ControllerPAD!

![Controller-PAD.jpg](https://i.postimg.cc/VLM3HRrK/Controller-PAD.jpg)



```package
neopixel=github:microsoft/pxt-neopixel#v0.7.3
```
# LaunchPAD med Kravspec i riktig rekkefølge

### @diffs true

## Del 1: @unplugged

### Programmere LaunchPAD @unplugged

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

Får å få til dette, må vi lage en ``||logic: hvis-betingelse ||`` inni ``||radio: radio mottar receivedNumber ||``. Her skal vi vi sjekke om ``||variables: receivedNumber||`` = 42.

Hvis den er det, skal vi kalle opp den nye funksjonen ``||functions: Launch ||``.

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

### Oppskytningssekvensen @diffs true

Inni ``||functions: Launch ||`` skal vi utføre oppskytningssekvensen. Men den får fun lov til å bli utført ``||logic: hvis ||`` ``||variables: ArmStatus ||`` er ``||logic: sann ||``.

Så ``||logic: hvis ||`` ``||variables: ArmStatus ||`` er ``||logic: sann ||``: 

- ``||pins: Skriv digital til P16 ||`` til HØY (1)
- Lag en ``||basic: pause ||`` på 500ms.
- ``||pins: Skriv digital til P16 ||`` til LAV (0)

```blocks
function Launch () { 
    if (ArmStatus) {
        pins.digitalWritePin(DigitalPin.P16, 1)
        basic.pause(500)
        pins.digitalWritePin(DigitalPin.P16, 0)
    }
}
```

## Del 1.4:

### Må: Rearmere kofferten før ny oppskytning @diffs true

Kofferten får ikke skyte opp en ny rakett før den er rearmert. (Armeringsbryteren må skrus AV.)

Så for å "låse" kofferten etter at Launch-kommandoen er sendt, bruk en ``||loops: gjenta hvis sann ||``. Bruk to ``||basic: vis skjerm ||``, som ser forskjellig ut, til å blinke så lenge ``||pins: les digital P1 = 1 ||``.

```blocks
function Launch () { 
    if (ArmStatus) {
        pins.digitalWritePin(DigitalPin.P16, 1)
        basic.pause(500)
        pins.digitalWritePin(DigitalPin.P16, 0)
        while (pins.digitalReadPin(DigitalPin.P1) == 1) {
            basic.showLeds(`
                . . . . .
                . # # # .
                . # # # .
                . # # # .
                . . . . .
            `)
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
```

## Del 1.5:

### Teste Oppskytningssekvensen!

``||math: Last ned||`` koden til begge koffertene og sjekk at oppstartsekvensen fungerer som den skal.


## Del 1.6: @unplugged

### Bytte Tutorial

Gratulerer! Vi er nå ferdige med å lage MÅ-koden på ControllerPAD!

Bytt veiledning, og start å lage koden til LaunchPAD-kofferten!

![Controller-PAD.jpg](https://i.postimg.cc/VLM3HRrK/Controller-PAD.jpg)


## Del 2.1:

### Bør: Sette opp lysene på kofferten

For å vise status på systemene på kofferten, bruker vi NeoPixels. Det skal brukes 4 NeoPixels på ``||pins: digital pin P0||``.

Skriv også at ``||pins: digital pin P15 = 1||``. Dette er lyset på Status Check-knappen på kofferten.

```blocks
radio.setGroup(1)
radio.setTransmitPower(7)
let strip = neopixel.create(DigitalPin.P0, 4, NeoPixelMode.RGB)
pins.digitalWritePin(DigitalPin.P15, 1)
```

## Del 2.2:

### Bør: SelfStatus

Øverst i ``||basic: gjenta for alltid||``.

Her skal vi sette en ny variabel ``||variables: SelfStatus||`` til ``||logic: sann||``. (Kofferten er jo på...)


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
})
function Launch () { 
    if (ArmStatus) {
        pins.digitalWritePin(DigitalPin.P16, 1)
        basic.pause(500)
        pins.digitalWritePin(DigitalPin.P16, 0)
        while (pins.digitalReadPin(DigitalPin.P1) == 1) {
            basic.showLeds(`
                . . . . .
                . # # # .
                . # # # .
                . # # # .
                . . . . .
            `)
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
```

## Del 2.3: @unplugged

### Sjekke Linkstatus mellom rakettkoffertene

For å kunne sjekke link mellom koffertene må bruke noe som kalles for «active sensing». 

Begge koffertene sender en radiomelding mellom seg med gitte intervaller som sier «Hei, jeg er ikke avskrudd». Men vi trenger noe som sier ifra hvis det har gått for lang tid siden sist vi fikk en radiomelding fra den andre kofferten.

![Radio-mellom-rakettkoffertene.gif](https://i.postimg.cc/nL4Rtr4R/Radio-mellom-rakettkoffertene.gif)


## Del 2.4: 

### Lage en oppdateringsfrekvens

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
})
function Launch () { 
    if (ArmStatus) {
        pins.digitalWritePin(DigitalPin.P16, 1)
        basic.pause(500)
        pins.digitalWritePin(DigitalPin.P16, 0)
        while (pins.digitalReadPin(DigitalPin.P1) == 1) {
            basic.showLeds(`
                . . . . .
                . # # # .
                . # # # .
                . # # # .
                . . . . .
            `)
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
```

## Del 2.8:

### Bør: Sette opp status på NeoPixelene

Vi skal bruke en ny funksjon, ``||functions: NeoPixels||``, for å vise hvilken status de forskjellige systemene på kofferten har. 

Vi skal inidividuelt sjekke opp variablene: ``||variables: SelfStatus||``, ``||variables: LinkStatus||``, ``||variables: IgniterStatus||`` og ``||variables: ArmStatus||``.

Lag 4 ``||logic: Hvis-betingelser||`` med ``||logic: Ellers hvis||``, en for hver variabel. Hvis den er ``||logic: sann||``, skal Neopixel settes til grønn, ellers skal den være rød.

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
})
function Launch () { 
    if (ArmStatus) {
        pins.digitalWritePin(DigitalPin.P16, 1)
        basic.pause(500)
        pins.digitalWritePin(DigitalPin.P16, 0)
        while (pins.digitalReadPin(DigitalPin.P1) == 1) {
            basic.showLeds(`
                . . . . .
                . # # # .
                . # # # .
                . # # # .
                . . . . .
            `)
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
```

## Del 3.1:

### Kjekt-å-ha: Sende status på Igniter og Arm til ControllerPAD

Videre skal vi finne ut status på ``||variables: IgniterStatus||`` og ``||variables: ArmStatus||``. Dette skal legges inn i funksjonen ``||functions: gjenta for alltid||``

- Lag to ``||logic: Hvis-betingelse||``. Den ene skal sjekke om ``||variables: IgniterStatus||`` er ``||logic: sann||``, mens den andre skal lese av ``||pins: P1||`` (Arm-knappen). 
- Hvis ``||variables: IgniterStatus||`` er ``||logic: sann||``, skal vi sende en ``||radio: radio send tall = 21||`` til ControllerPAD. Ellers skal vi skal sende en ``||radio: radio send tall = 22||`` til ControllerPAD.
- Hvis ``||pins: P1||`` er lik 1, skal ``||variables: ArmStatus||`` settes til ``||logic: sann||`` og vi skal sende en ``||radio: radio send tall = 31||`` til ControllerPAD. Ellers skal ``||variables: ArmStatus||`` settes til ``||logic: usann||`` og vi skal sende en ``||radio: radio send tall = 32||`` til ControllerPAD.

```blocks
function StatusCheck () {
	SelfStatus = true
    if (IgniterStatus) {
        radio.sendNumber(21)
    } else {
        radio.sendNumber(22)
    }
    if (pins.digitalReadPin(DigitalPin.P1) == 1) {
        ArmStatus = true
        radio.sendNumber(31)
    } else {
        ArmStatus = false
        radio.sendNumber(32)
    }
    NeoPixels()
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



```package
neopixel=github:microsoft/pxt-neopixel#v0.7.3
```
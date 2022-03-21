# LaunchPAD Tutorial

### @diffs true


## Del 1: @unplugged

### Programmere LaunchPAD @unplugged

I denne veiledningen skal vi gå igjennom hvordan man programmerer LaunchPAD-kofferten til RocketLink oppskytningssystem. 

Vi skal sette opp radiokommunikasjon for å snakke med ControllerPAD, for å si ifra om det er klart til å skyte opp raketten. Hvis alt er klart, kan kofferten motta signal om å skyte opp raketten!

Vi kommer til å hoppe mellom veiledningen for å programmere LaunchPAD og ControllerPAD. Du får beskjed når du skal bytte mellom veiledningene og når du skal teste koden underveis.

Lykke til!

![Launch-PAD.jpg](https://i.postimg.cc/Sxhw0Mck/Launch-PAD.jpg)


## Del 1.1: 

### Ved start - radio:

I blokken ``||basic: ved start||`` skal vi sette opp de funksjonene som kun skal brukes når kofferten skrus på.

Det første vi skal sette opp er en ``||radio: radiogruppe||``. (Vi skal bruke radiogruppe nr. 1 på koffertene.) 
 
Sett også opp at ``||radio: sendereffekt||`` skal være lik 7. (Gir oss sterkere radiosender.)

```blocks
radio.setGroup(1)
radio.setTransmitPower(7)
```

## Del 1.2: 

### Ved start - resten:

For å vise status på systemene på kofferten, bruker vi NeoPixels. Det skal brukes 4 NeoPixels på ``||neopixel: digital pin P0||``.

Skriv også at ``||pins: digital pin P15 = 1||``. Dette er lyset på Status Check-knappen på kofferten.

```blocks
radio.setGroup(1)
radio.setTransmitPower(7)
pins.digitalWritePin(DigitalPin.P15, 1)
let strip = neopixel.create(DigitalPin.P0, 4, NeoPixelMode.RGB)
```

## Del 2: @unplugged

### Initialize

Vi skal lage en funksjon for kofferten som heter ``||functions: Initialize||``. 

Funksjonen skal kjøres hver gang vi skrur på LaunchPAD og hver gang vi rearmerer kofferten etter en oppskytning. 

``||functions: Initialize||`` sin oppgave er å nullstille rakettkofferten slik at alle systemer stilles tilbake eller skrus av.

![Initialize.gif](https://i.postimg.cc/rFfWbdvN/Initialize.gif)

## Del 2.1: 

### Initialize oppsett:

For å kjøre oppstartsekvensen til RocketLink-M, må vi lage en funksjon som vi kaller: ``||functions: Initialize||``.

Det er flere steder i koden vår vi skal kalle opp ``||functions: Initialize||``. Den første er i bunn av blokken: ``||basic: ved start||``.

```blocks
radio.setGroup(1)
radio.setTransmitPower(7)
pins.digitalWritePin(DigitalPin.P15, 1)
let strip = neopixel.create(DigitalPin.P0, 4, NeoPixelMode.RGB)
Initialize()
function Initialize () {
	
}
```

## Del 2.2: 

### Initialize funksjon:

Det første vi må gjøre inne i ``||functions: Initialize||`` er å lage 6 variabler: ``||variables: SelfStatus||``, ``||variables: LinkStatus||``, ``||variables: IgniterStatus||``, ``||variables: ArmStatus||`` og ``||variables: Klar||``.

Disse variablene utfører, og skal settes til:

- ``||variables: SelfStatus||`` sjekker om kofferten er på. Settes til ``||logic: usann||``
- ``||variables: LinkStatus||`` sjekker om det er kontakt med den andre kofferten. Settes til ``||logic: usann||``
- ``||variables: IgniterStatus||`` viser om ledningene er riktig koblet til tenneren i raketten. Settes til ``||logic: usann||``
- ``||variables: ArmStatus||`` sjekker om Arm-knappen er skrudd på. Settes til ``||logic: usann||``
- ``||variables: Klar||`` sjekker om alle systemene på kofferten er på. Settes til ``||logic: usann||``

| Variables ||||| Status |
|:---------|||||:------:|
| SelfStatus ||||| Usann |
| LinkStatus ||||| Usann |
| IgniterStatus ||||| Usann |
| ArmStatus ||||| Usann |
| Klar ||||| Usann |

```blocks
function Initialize () {
	SelfStatus = false
    LinkStatus = false
    IgniterStatus = false
    ArmStatus = false
    Klar = false
}
```

## Del 2.3: 

### Initialize - NeoPixels:

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

## Del 2.4: 

### Initialize - animasjon:

Vi lager en liten animasjon på skjermen til micro:bit under oppstartsekvensen. Du kan velge selv hvordan den skal se ut. (Hint: Dette er kun et forslag.)

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

## Del 2.5:

### Teste Initialize!

``||math: Last ned||`` koden på en micro:bit og sjekk at oppstartssekvensen fungerer som den skal på LaunchPAD-kofferten.


## Del 2.6: @unplugged

### Bytte Tutorial

Vi skal nå bytte veiledning, og fortsette å lage koden til ControllerPAD-kofferten!

![Controller-PAD.jpg](https://i.postimg.cc/VLM3HRrK/Controller-PAD.jpg)


## Del 3: @unplugged 

### StatusCheck NeoPixels

Vi skal fortsette på koden som styrer LaunchPAD.

Vi skal lage en funksjon for kofferten som heter ``||functions: StatusCheck||``. Funksjonen skal sjekke statusen til de forskjellige systemene på LaunchPAD. 

Vi skal lage koden for ett og ett system av gangen, og teste hvert system når det er ferdig. 

![Status-Check-500px.gif](https://i.postimg.cc/nLr2C5W8/Status-Check-500px.gif)


## Del 3.1: 

### Sette opp NeoPixels

For å benytte oss av NeoPixelene, for å vise om hvert av systemene er på eller av, må vi lage en ny funksjon: ``||functions: NeoPixels||``. 

I denne skal vi individuelt sjekke opp variablene: ``||variables: SelfStatus||``, ``||variables: LinkStatus||``, ``||variables: IgniterStatus||`` og ``||variables: ArmStatus||``.

Lag 4 ``||logic: Hvis-betingelser||``, en for hver variabel. Hvis den er ``||logic: sann||``, skal Neopixel settes til grønn, ellers skal den være rød.

Avslutt med å vise lysene: ``||neopixel: show||``

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
```


## Del 3.2: 

### StatusCheck oppsett med SelfStatus

For at systemet vårt hele tiden skal sjekke om det skjer noen status-endringer, trenger vi å lage en ny funksjon: ``||functions: StatusCheck||``.

Denne funksjonen skal kalles opp fra ``||basic: gjenta for alltid||``.

Når vi kjører ``||functions: StatusCheck||``, vet vi at ``||variables: SelfStatus||`` er ``||logic: sann||``. (Kofferten er jo på...)

For å gi blokken ``||basic: Gjenta for alltid||`` litt pusterom når den jobber, legg inn en liten ``||basic: pause ||`` på 100ms på slutten.

I bunnen av ``||functions: StatusCheck||`` skal kalles opp ``||functions: NeoPixels||`` for å oppdatere lysene på kofferten.


```blocks
basic.forever(function () {
    StatusCheck()
    basic.pause(100)
})
function StatusCheck () {
	SelfStatus = true
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

## Del 3.3: @unplugged

### Bytte Tutorial

Vi skal nå bytte veiledning, og fortsette å lage koden til ControllerPAD-kofferten!

![Controller-PAD.jpg](https://i.postimg.cc/VLM3HRrK/Controller-PAD.jpg)


## Del 4: @unplugged

### Sjekke Linkstatus mellom rakettkoffertene:

For å kunne sjekke link mellom koffertene må bruke noe som kalles for «active sensing». 

Begge koffertene sender en radiomelding mellom seg med gitte intervaller som sier «Hei, jeg er ikke avskrudd». Men vi trenger noe som sier ifra hvis det har gått for lang tid siden sist vi fikk en radiomelding fra den andre kofferten.

![Radio-mellom-rakettkoffertene.gif](https://i.postimg.cc/nL4Rtr4R/Radio-mellom-rakettkoffertene.gif)


## Del 4.1: 

### Sjekke om radio-kommunikasjon funker:

Lag en ny variabel: ``||variables: sistSettAktiv||``. 

Inni en ``||radio:når radio mottar receivedNumber||``: Hver gang vi mottar ``||radio: receivedNumber = 11||``,sett ``||variables: LinkStatus||`` til ``||logic: sann||`` og ``||variables: sistSettAktiv||`` til ``||input: kjøretid (ms)||``. 

I en micro:bit, kjører det en intern klokke som heter ``||input: kjøretid (ms)||``. Ved å sette ``||variables: sistSettAktiv||`` lik ``||input: kjøretid (ms)||`` ved gitte intervaller, kan vi lett se om tidsforskjellen mellom ``||variables: sistSettAktiv||`` og ``||input: kjøretid (ms)||`` blir større enn 3x gitt tidsinterval.

```blocks
radio.onReceivedNumber(function (receivedNumber) {
    if (receivedNumber == 11){
        LinkStatus = true
        sistSettAktiv = input.runningTime()
    }
})
```

## Del 4.2: 

### Lage en oppdateringsfrekvens:

Lag en variabel som du kaller ``||variables: Oppdateringsfrekvens||``. Sett den inn under  ``||basic: ved start||``, og la den være 200 ms.

```blocks
let strip = neopixel.create(DigitalPin.P0, 4, NeoPixelMode.RGB)
radio.setGroup(1)
radio.setTransmitPower(7)
pins.digitalWritePin(DigitalPin.P15, 1)
let Oppdateringsfrekvens = 200
Initialize()
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

## Del 4.3: 

### Sjekke om den andre kofferten er skrudd av: 

For å finne ut om kofferten har sluttet å motta signal fra den andre kofferten, må vi lage en liten funksjon som vi setter inn i  ``||control: kjør i bakgrunnen||``. (Finner du i menuen: Styring)

Inni her skal vi lage en  ``||loops: gjenta hvias sann-løkke||``. Denne løkken vil kjøre så lenge kofferten er på.

Inni ``||loops: løkken||`` skal vi send tallet 11 med radio. 

Nå skal vi sjekke om den ene av koffertene ikke mottar et signal på 3x ``||variables: Oppdateringsfrekvens||``. Hvis den er lengre enn det, kan den regne med at den andre kofferten er skrudd av.

Vi skal sjekke om ``||input: kjøretid (ms)||`` minus (-) ``||variables: sistSettAktiv||`` er større enn (>) 3x ``||variables: Oppdateringsfrekvens||``

Hvis dette er sant, skal ``||variables: LinkStatus||`` settes til ``||logic: usann||``

Utenfor ``||logic: hvis-betingelsen||``, avslutt med en ``||basic: pause||`` lik ``||variables: Oppdateringsfrekvens||``.


```blocks
control.inBackground(function () {
    while (true) {
        radio.sendNumber(11)
        if (input.runningTime() - sistSettAktiv > 3 * Oppdateringsfrekvens) {
            LinkStatus = false
        }
    basic.pause(Oppdateringsfrekvens)
    }
})
```

## Del 4.4:

### Teste LinkStatus

Nå som vi er ferdig med å sette opp funksjonen for ``||variables: LinkStatus||`` på begge koffertene, er vi klare for å teste den!

``||math: Last ned||`` kodene fra begge tutorialene på hver sin micro:bit og sjekk at ``||variables: LinkStatus||`` fungerer på koffertene.


## Del 5: @unplugged

### IgniterStatus og ArmStatus

LaunchPAD skal sende statusen til igniteren og arm-knappen til ControllerPAD. I denne delen skal vi lese av verdiene til igniter og arm, og sende de over radio til den andre kofferten.

![Radio-mellom-rakettkoffertene.gif](https://i.postimg.cc/nL4Rtr4R/Radio-mellom-rakettkoffertene.gif)


## Del 5.1: 

### StatusCheck - IgniterStatus og ArmStatus:

Videre skal vi finne ut status på ``||variables: IgniterStatus||`` og ``||variables: ArmStatus||``. Dette skal legges inn i funksjonen ``||functions: StatusCheck||``

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

## Del 5.2: 

### Teste om igniter er koblet til rakett

Inne i ``||basic: Gjenta for alltid||`` skal vi lage funksjonen som tester om igniteren er koblet riktig til raketten.

For å sjekke dette, må vi trykker ned knappen til System Status Check på kofferten. Når knappen trykkes ned, settes ``||pins: digital pin P5 ||`` til 0.

Inni ``||logic: hvis||`` ``||pins: digital pin P5 ||`` = 0:

- Start med å sette alle NeoPixelene til ``||neopixel: rød||``.
- For å kunne lese av ``||pins: digital pin P2||`` (Ignitersjekk), må vi skrive ``||pins: digital pin P14||`` (Ignitersjekk-sikkerhet) til HØY (1) i 200 ms før den skrives til LAV (0) igjen. Mens ``||pins: P14||`` er HØY skal vi lese av ``||pins: digital pin P2||``. 

Så hvis ``||pins: digital pin P2||`` er HØY (1), skal ``||variables: IgniterStatus||`` settes til ``||logic: sann||``. Ellers skal den settes til ``||logic: usann||``.


```blocks
let strip: neopixel.Strip = null
basic.forever(function () {
    StatusCheck()
    if (pins.digitalReadPin(DigitalPin.P5) == 0) {
        strip.showColor(neopixel.colors(NeoPixelColors.Red))
        pins.digitalWritePin(DigitalPin.P14, 1)
        if (pins.digitalReadPin(DigitalPin.P2) == 1) {
            IgniterStatus = true
            }
        else {
            IgniterStatus = false
            }
        basic.pause(200)
        pins.digitalWritePin(DigitalPin.P14, 0)
    }
    basic.pause(100)
})
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

## Del 5.3: 

### Sjekke om LaunchPAD er klar for oppskytning

For å sjekke om alle systemene er klare for oppskytning, må vi lage en ``||logic: Hvis-betingelse||`` som sjekker om alle statusene vår er lik ``||logic: sann||``. Den skal settes inn i funksjonen ``||functions: StatusCheck||``.

Vi skal sjekke: ``||variables: SelfStatus||``, ``||variables: LinkStatus||``, ``||variables: IgniterStatus||`` og ``||variables: ArmStatus||``.

Hvis alle statusene over er lik``||logic: sann||``, da skal variabelen ``||variables: Klar||`` settes lik ``||logic: sann||``. Ellers settes lik ``||logic: usann||``. Legg ved et bilde på hvert at tilfellene for se om ``||variables: Klar||`` er ``||logic: sann||`` eller ikke.

```blocks
function StatusCheck () {
	SelfStatus = true
    if (IgniterStatus) {
         radio.sendNumber(21)
    } else {
        radio.sendNumber(22)
    }
    if (pins.digitalReadPin(DigitalPin.P1) == 0) {
        ArmStatus = true
        radio.sendNumber(31)
    } else {
        ArmStatus = false
        radio.sendNumber(32)
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

## Del 5.4:

### Teste Initialize!

``||math: Last ned||`` koden på en micro:bit og sjekk at alle statusene fungerer som den skal på LaunchPAD-kofferten.


## Del 5.5: @unplugged

### Bytte Tutorial

Vi skal nå bytte veiledning, og fortsette å lage koden til ControllerPAD-kofferten!

![Controller-PAD.jpg](https://i.postimg.cc/VLM3HRrK/Controller-PAD.jpg)


## Del 6: @unplugged

### Oppskytning av raketten

Nå skal alle systemene på begge koffertene fungere, og vi er klare for å bygge funksjonene for å kunne skyte opp raketten!

![Oppskytning-rakett-4.gif](https://i.postimg.cc/CxV5Qx0C/Oppskytning-rakett-4.gif)


## Del 6.1:

### Oppsett av Launch-funksjon

For å kunne skyte opp raketten, trenger vi å lage en ny funksjonen som vi kaller ``||functions: Launch||``. 

- Vi trenger også en ny variabel: ``||variables: Oppskytning||``.
- I ``||radio: når radio mottar receivedNumber||`` skal vi lage en ``||logic: Hvis-betingelse||`` som sier at ``||variables: Oppskytning||`` er ``||logic: sann||`` hvis ``||radio: receivedNumber = 42||``.
- I ``||basic: gjenta for alltid||`` skal vi sjekke om ``||variables: Oppskytning||`` er ``||logic: sann||`` eller ikke. Hvis ``||variables: Oppskytning||`` er ``||logic: sann||``, skal vi kalle opp funksjonen ``||functions: Launch||``.

```blocks
radio.onReceivedNumber(function (receivedNumber) {
    if (receivedNumber == 11) {
        LinkStatus = true
        sistSettAktiv = input.runningTime()
    }
    if (receivedNumber == 42) {
        Oppskytning = true
    }
})
let strip: neopixel.Strip = null
basic.forever(function () {
    StatusCheck()
    if (pins.digitalReadPin(DigitalPin.P5) == 0) {
        strip.showColor(neopixel.colors(NeoPixelColors.Red))
        pins.digitalWritePin(DigitalPin.P14, 1)
        if (pins.digitalReadPin(DigitalPin.P2) == 1) {
            IgniterStatus = true
            }
        else {
            IgniterStatus = false
            }
        basic.pause(200)
        pins.digitalWritePin(DigitalPin.P14, 0)
        }
    if (Oppskytning) {
        Launch()
    }
    basic.pause(100)
})
function Launch () {
	
}
function StatusCheck () {
	SelfStatus = true
    if (IgniterStatus) {
         radio.sendNumber(21)
    } else {
        radio.sendNumber(22)
    }
    if (pins.digitalReadPin(DigitalPin.P1) == 0) {
        ArmStatus = true
        radio.sendNumber(31)
    } else {
        ArmStatus = false
        radio.sendNumber(32)
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
    NeoPixels()
}   
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


## Del 6.2: 

### Sende opp raketten!

Koden inne i ``||functions: Launch||`` skal kun få lov til å bli utført hvis ``||variables: Klar||`` er ``||logic: sann||``.

Lag en pil på skjermen til microbiten for å vise at raketten blir skutt opp.

Skriv ``||pins: digital pin P16||`` til HØY (1) i 500 ms. før den skrives LAV (0) igjen.

Helt til slutt skal vi sette ``||variables: Oppskytning||`` til å være ``||logic: usann||``. Den skal plasseres inn i funksjonen ``||functions: Initialize ||``.

```blocks
let strip: neopixel.Strip = null
function Launch () {
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
    }
}
function Initialize () {
	SelfStatus = false
    LinkStatus = false
    IgniterStatus = false
    ArmStatus = false
    Klar = false
    Oppskytning = false
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

## Del 6.3: 

### Rearm av kofferten:

Når vi har sendt opp raketten vår, skal vi ikke få lov til å sende opp en ny rakett før arm-bryteren er skrudd av igjen, og systemet er resatt. 

For å gjøre dette må vi lage en ny funksjon: ``||functions: Rearm||``. Den skal kalles opp fra bunnen i ``||functions: Launch||`` (inne i ``||logic: Hvis-betingelsen||``). 

Start med å skru av lysene til NeoPixels. Så skal vi bruke en ``||loops: gjenta hvis sann-løkke||`` som er aktiv så lange  ``||pins: digital pin P1 = 1||``.

Inni denne ``||loops: løkken||`` skal vi få skjermen på microbiten til å blinke for å vise at kofferten må rearmeres.

```blocks
let strip: neopixel.Strip = null
function Rearm () {
    strip.clear()
    strip.show()
    while (pins.digitalReadPin(DigitalPin.P1) == 1) {
        basic.showLeds(`
            . . . . .
            . # # # .
            . # . # .
            . # # # .
            . . . . .
            `)
        basic.showLeds(`
            . . . . .
            . # # # .
            . # # # .
            . # # # .
            . . . . .
            `)
    }
}
function Launch () {
    if (Klar) {
	    basic.showLeds(`
        . . # . .
        . # # # .
        # . # . #
        . . # . .
        . . # . .
        `)
        radio.sendNumber(42)
        Klar = false
        Rearm()
    }
}
```


## Del 7.2: 

### Rearm-funksjonen etter rearmeringen:

Etter at arm-knappen er skrudd av, vil vi hoppe ut av ``||loops: gjenta hvis sann-løkken||``. 

Sett Neopixels til ``||neopixel: Purple||`` og ``||basic: tøm skjermen||`` til microbiten.

Avslutt med en ``||basic: pause||`` i 200 ms. før vi kaller opp funksjonen ``||functions: Initialize||`` for å restarte rakett-kofferten.

```blocks
let strip: neopixel.Strip = null
function Rearm () {
    strip.clear()
    strip.show()
    while (pins.digitalReadPin(DigitalPin.P1) == 0) {
        basic.showLeds(`
            . . . . .
            . # # # .
            . # . # .
            . # # # .
            . . . . .
            `)
        basic.showLeds(`
            . . . . .
            . # # # .
            . # # # .
            . # # # .
            . . . . .
            `)
    }
    strip.showColor(neopixel.colors(NeoPixelColors.Purple))
    basic.showLeds(`
        . . . . .
        . . . . .
        . . . . .
        . . . . .
        . . . . .
        `)
    basic.pause(200)
    Initialize()
}
function Initialize () {
	SelfStatus = false
    LinkStatus = false
    IgniterStatus = false
    ArmStatus = false
    Klar = false
    Oppskytning = false
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
## Del 8:

### Gratulerer! Du er nå ferdig med å programmere LaunchPAD-Kofferten!

``||math: Last ned||`` kodene for både ControllerPAD og LaunchPAD på hver sin micro:bit, og sjekk at de fungerer som de skal. 

```blocks
let Klar = false
let ArmStatus = false
let IgniterStatus = false
let SelfStatus = false
let Oppskytning = false
let sistSettAktiv = 0
let LinkStatus = false
let strip: neopixel.Strip = null
strip = neopixel.create(DigitalPin.P0, 4, NeoPixelMode.RGB)
radio.setGroup(1)
radio.setTransmitPower(7)
pins.digitalWritePin(DigitalPin.P15, 1)
let oppdateringsfrekvens = 200
Initialize()
function Initialize () {
    SelfStatus = false
    LinkStatus = false
    IgniterStatus = false
    ArmStatus = false
    Oppskytning = false
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
basic.forever(function () {
    StatusCheck()
    if (pins.digitalReadPin(DigitalPin.P5) == 0) {
        strip.showColor(neopixel.colors(NeoPixelColors.Red))
        pins.digitalWritePin(DigitalPin.P14, 1)
        if (pins.digitalReadPin(DigitalPin.P2) == 1) {
            IgniterStatus = true
            }
        else {
            IgniterStatus = false
            }
        basic.pause(200)
        pins.digitalWritePin(DigitalPin.P14, 0)
    }
    if (Oppskytning) {
        Launch()
    }
    basic.pause(100)
})
radio.onReceivedNumber(function (receivedNumber) {
    if (receivedNumber == 11) {
        LinkStatus = true
        sistSettAktiv = input.runningTime()
    }
    if (receivedNumber == 42) {
        Oppskytning = true
    }
})
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
    if (SelfStatus && LinkStatus && IgniterStatus && ArmStatus) {
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
    NeoPixels()
}
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
function Launch () {
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
        Rearm()
    }
}
function Rearm () {
    strip.clear()
    strip.show()
    while (pins.digitalReadPin(DigitalPin.P1) == 1) {
        basic.showLeds(`
            . . . . .
            . # # # .
            . # . # .
            . # # # .
            . . . . .
            `)
        basic.showLeds(`
            . . . . .
            . # # # .
            . # # # .
            . # # # .
            . . . . .
            `)
    }
    strip.showColor(neopixel.colors(NeoPixelColors.Purple))
    basic.showLeds(`
        . . . . .
        . . . . .
        . . . . .
        . . . . .
        . . . . .
        `)
    basic.pause(100)
    Initialize()
}
control.inBackground(function () {
    while (true) {
        radio.sendNumber(11)
        if (input.runningTime() - sistSettAktiv > 3 * oppdateringsfrekvens) {
            LinkStatus = false
        }
        basic.pause(oppdateringsfrekvens)
    }
})
```

```package
neopixel=github:microsoft/pxt-neopixel#v0.7.3
```

gulp testtutorials
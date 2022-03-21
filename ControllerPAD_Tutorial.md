# ControllerPAD Tutorial

### @diffs true


## Del 1: @unplugged

### Programmere ControllerPAD @unplugged

I denne veiledningen skal vi gå igjennom hvordan man programmerer ControllerPAD-kofferten til RocketLink oppskytningssystem. 

Vi skal sette opp radiokommunikasjon for å snakke med LauchPAD, for å finne ut om det er klart til å skyte opp raketten. Hvis alt er klart skal vi sende signal om å skyte opp raketten!

Vi kommer til å hoppe mellom veiledningen for å programmere LaunchPAD og ControllerPAD. Du får beskjed når du skal bytte mellom veiledningene og når du skal teste koden underveis.

Lykke til!

![Controller-PAD.jpg](https://i.postimg.cc/VLM3HRrK/Controller-PAD.jpg)


## Del 1.1: 

### Ved start - radio

I blokken ``||basic: ved start||`` skal vi sette opp de funksjonene som kun skal brukes når kofferten skrus på.

Det første vi skal sette opp er en ``||radio: radiogruppe||``. (Vi skal bruke radiogruppe nr. 1 på koffertene.)

Sett også opp at ``||radio: sendereffekt||`` skal være lik 7. (Gir oss sterkere radiosender.)

```blocks
radio.setGroup(1)
radio.setTransmitPower(7)
```

## Del 1.2: 

### Ved start - resten

For å vise status på systemene på kofferten, bruker vi NeoPixels. Det skal brukes 5 NeoPixels på ``||neopixel: digital pin P0||``.

Skriv også at ``||pins: digital pin P15 = 1||``. Dette er lyset på Status Check-knappen på kofferten.

```blocks
radio.setGroup(1)
radio.setTransmitPower(7)
let strip = neopixel.create(DigitalPin.P0, 5, NeoPixelMode.RGB)
pins.digitalWritePin(DigitalPin.P15, 1)
```

## Del 2: @unplugged

### Initialize

Vi skal lage en funksjon for kofferten som heter ``||functions: Initialize||``. 

Funksjonen skal kjøres hver gang vi skrur på ControllerPAD og hver gang vi rearmerer kofferten etter en oppskytning. 

``||functions: Initialize||`` sin oppgave er å nullstille rakettkofferten slik at alle systemer stilles tilbake eller skrus av.

![Initialize.gif](https://i.postimg.cc/rFfWbdvN/Initialize.gif)


## Del 2.1

### Initialize oppsett: 

For å kjøre oppstartsekvensen til RocketLink-M, må vi lage en funksjon som vi kaller: ``||functions: Initialize||``.

Det er flere steder i koden vår vi skal kalle opp ``||functions: Initialize||``. Den første er i ``||basic: ved start||``.

```blocks
radio.setGroup(1)
radio.setTransmitPower(7)
let strip = neopixel.create(DigitalPin.P0, 5, NeoPixelMode.RGB)
pins.digitalWritePin(DigitalPin.P15, 1)
Initialize()
function Initialize () {
	
}
```

## Del 2.2: 

### Initialize funksjon:

Det første vi må gjøre inne i ``||functions: Initialize||`` er å lage 6 variabler: ``||variables: SelfStatus||``, ``||variables: LinkStatus||``, ``||variables: ArmStatus||``, ``||variables: IgniterStatusLP||``, ``||variables: ArmStatusLP||`` og ``||variables: Klar||``.

``||variables: SelfStatus||`` sjekker om kofferten er på, ``||variables: LinkStatus||`` sjekker om det er kontakt med LaunchPAD, ``||variables: ArmStatus||`` sjekker om Arm-knappen er skrudd på, ``||variables: IgniterStatusLP||`` sjekker om raketten er koblet riktig til LaunchPAD, ``||variables: ArmStatusLP||`` sjekker om Arm-knappen er aktivert på LaunchPAD og ``||variables: Klar||`` sjekker om alle systemene på koffertene er på.

Sett alle disse 6 variablene til er være ``||logic: usann||``. 

| Variabel ||||| Status |
|:---------|||||:------:|
| SelfStatus ||||| Usann |
| LinkStatus ||||| Usann |
| ArmStatus ||||| Usann |
| IgniterStatusLP ||||| Usann |
| ArmStatusLP ||||| Usann |
| Klar ||||| Usann |


```blocks
let SelfStatus = false
let LinkStatus = false
let ArmStatus = false
let IgniterStatusLP = false
let ArmStatusLP = false
let Klar = false
function Initialize () {
	SelfStatus = false
    LinkStatus = false
    ArmStatus = false
    IgniterStatusLP = false
    ArmStatusLP = false
    Klar = false
}
```

## Del 2.3: 

### Initialize - NeoPixels:

Videre inne i ``||functions: Initialize||`` skal vi teste lysene på NeoPixel strip. Sett alle lysene til å lyse lilla (``||neopixel: Purple||``).

```blocks
let strip: neopixel.Strip = null
let SelfStatus = false
let LinkStatus = false
let ArmStatus = false
let IgniterStatusLP = false
let ArmStatusLP = false
let Klar = false
function Initialize () {
	SelfStatus = false
    LinkStatus = false
    ArmStatus = false
    IgniterStatusLP = false
    ArmStatusLP = false
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
let SelfStatus = false
let LinkStatus = false
let ArmStatus = false
let IgniterStatusLP = false
let ArmStatusLP = false
let Klar = false
function Initialize () {
	SelfStatus = false
    LinkStatus = false
    ArmStatus = false
    IgniterStatusLP = false
    ArmStatusLP = false
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

``||math: Last ned||`` koden på en micro:bit og sjekk at oppstartsekvensen kjøres på ControllerPAD-kofferten.

## Del 2.6: @unplugged

### Bytte Tutorial

Vi skal nå bytte veiledning, og starte å lage koden til LaunchPAD-kofferten!

![Launch-PAD.jpg](https://i.postimg.cc/Sxhw0Mck/Launch-PAD.jpg)


## Del 3: @unplugged 

### StatusCheck med NeoPixels

Vi skal fortsette på koden som styrer ControllerPAD.

Lage en funksjon for kofferten som heter ``||functions: StatusCheck||``. 

Funksjonen skal sjekke statusen til de forskjellige systemene på ControllerPAD. 

Den skal sjekke ett og ett system av gangen, og teste hvert system når det er ferdig. For vise status på systemene skal vi bruke NeoPixels i kofferten.

![System-Status-Controller-PAD.gif](https://i.postimg.cc/Rh2GqPLT/System-Status-Controller-PAD.gif)


## Del 3.1: 

### Sette opp NeoPixels

Vi skal bruke en ny funksjon, ``||functions: NeoPixels||``, for å vise hvilken status de forskjellige systemene på kofferten har. 

Vi skal inidividuelt sjekke opp variablene: ``||variables: SelfStatus||``, ``||variables: LinkStatus||``, ``||variables: IgniterStatusLP||``, ``||variables: ArmStatusLP||`` og ``||variables: Klar||``.

Lag 5 ``||logic: Hvis-betingelser||`` med ``||logic: Ellers hvis||``, en for hver variabel. Hvis den er ``||logic: sann||``, skal Neopixel settes til grønn, ellers skal den være rød.

Avslutt med å vise lysene: ``||neopixel: show||``

| Variabel ||||| NeoPixel nr. |
|:---------|||||:------:|
| SelfStatus ||||| 0 |
| LinkStatus ||||| 1 |
| IgniterStatusLP ||||| 2 |
| ArmStatusLP ||||| 3 |
| Klar ||||| 4 |

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
    if (IgniterStatusLP) {
        strip.setPixelColor(2, neopixel.colors(NeoPixelColors.Green))
    } else {
        strip.setPixelColor(2, neopixel.colors(NeoPixelColors.Red))
    }
    if (ArmStatusLP) {
        strip.setPixelColor(3, neopixel.colors(NeoPixelColors.Green))
    } else {
        strip.setPixelColor(3, neopixel.colors(NeoPixelColors.Red))
    }
    if (Klar) {
        strip.setPixelColor(4, neopixel.colors(NeoPixelColors.Green))
    } else {
        strip.setPixelColor(4, neopixel.colors(NeoPixelColors.Red))
    }
    strip.show()
}
```

## Del 3.2:

### StatusCheck oppsett med SelfStatus

For at systemet vårt hele tiden skal sjekke om det skjer noen status-endringer, trenger vi å lage en ny funksjon: ``||functions: StatusCheck||``.

Denne funksjonen skal kalles opp fra ``||basic: gjenta for alltid||``.

Når vi kjører gjennom ``||functions: StatusCheck||``, vet vi at variabelen ``||variables: SelfStatus||`` er ``||logic: sann||``. (Kofferten er jo på...)

I bunnen av ``||functions: StatusCheck||`` skal kalles opp ``||functions: NeoPixels||`` for å oppdatere lysene på kofferten.




```blocks
basic.forever(function () {
    StatusCheck()
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
    if (IgniterStatusLP) {
        strip.setPixelColor(2, neopixel.colors(NeoPixelColors.Green))
    } else {
        strip.setPixelColor(2, neopixel.colors(NeoPixelColors.Red))
    }
    if (ArmStatusLP) {
        strip.setPixelColor(3, neopixel.colors(NeoPixelColors.Green))
    } else {
        strip.setPixelColor(3, neopixel.colors(NeoPixelColors.Red))
    }
    if (Klar) {
        strip.setPixelColor(4, neopixel.colors(NeoPixelColors.Green))
    } else {
        strip.setPixelColor(4, neopixel.colors(NeoPixelColors.Red))
    }
    strip.show()
}

```

## Del 3.3:

### Teste Initialize!

``||math: Last ned||`` koden på en micro:bit og sjekk at status-lysene funker som de skal på ControllerPAD-kofferten.


## Del 3.4: @unplugged

### Bytte Tutorial

Vi skal nå bytte veiledning, og fortsette å lage koden til LaunchPAD-kofferten!

![Launch-PAD.jpg](https://i.postimg.cc/Sxhw0Mck/Launch-PAD.jpg)


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

Lag en variabel som du kaller ``||variables: Oppdateringsfrekvens||``. Sett den inn under  ``||basic: ved start||``, og la den være 200ms.

```blocks
radio.setGroup(1)
radio.setTransmitPower(7)
let strip = neopixel.create(DigitalPin.P0, 5, NeoPixelMode.RGB)
pins.digitalWritePin(DigitalPin.P15, 1)
let Oppdateringsfrekvens = 200
Initialize()
function Initialize () {
	SelfStatus = false
    LinkStatus = false
    ArmStatus = false
    IgniterStatusLP = false
    ArmStatusLP = false
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

Inni her skal vi bruke en  ``||loops: gjenta hvis sann-løkke||``. Denne løkken vil kjøre så lenge kofferten er på.

Inni ``||loops: gjenta hvis sann||`` skal vi send tallet 11 med radio. 

Nå skal vi sjekke om den ene av koffertene ikke mottar et signal på 3x ``||variables: Oppdateringsfrekvens||``. Hvis den er lengre enn det, kan den regne med at den andre kofferten er skrudd av.

Vi skal sjekke om ``||input: kjøretid (ms)||`` minus (-) ``||variables: sistSettAktiv||`` er større enn (>) 3x ``||variables: Oppdateringsfrekvens||``

Hvis dette er sant, skal: 

-  ``||variables: LinkStatus||`` settes til ``||logic: usann||``
-  ``||variables: IgniterStatusLP||`` settes til ``||logic: usann||``
-  ``||variables: ArmStatusLP||`` settes til ``||logic: usann||``

Utenfor ``||logic: hvis-betingelsen||``, avslutt med en ``||basic: pause||`` lik ``||variables: Oppdateringsfrekvens||``.


```blocks
control.inBackground(function () {
    while (true) {
        radio.sendNumber(11)
        if (input.runningTime() - sistSettAktiv > 3 * Oppdateringsfrekvens) {
            LinkStatus = false
            IgniterStatusLP = false
            ArmStatusLP = false
        }
    basic.pause(oppdateringsfrekvens)
    }
})
```

## Del 4.4: @unplugged

### Bytte Tutorial

Vi må bygge funksjonen for ``||variables: LinkStatus||`` på LaunchPAD for å kunne teste den.

Bytt veiledning, og fortsett å lage koden til LaunchPAD-kofferten!

![Launch-PAD.jpg](https://i.postimg.cc/Sxhw0Mck/Launch-PAD.jpg)


## Del 5: @unplugged

### Motta status fra LaunchPAD-kofferten og sette opp StatusCheck

Det er to til statuser vi får fra LaunchPAD: ``||variables: IgniterStatusLP||`` og ``||variables: ArmStatusLP||``. (Disse to sjekker om igniterne er koblet ordentlig til raketten, og om Arm-knappen på LaunchPAD-kofferten er skrudd på.)

Vi skal nå sette opp hva som skal skje når vi mottar disse statusoppdateringene. 

Vi skal også sette opp resten av funksjonene i ``||functions: StatusCheck||``.

![Radio-mellom-rakettkoffertene.gif](https://i.postimg.cc/nL4Rtr4R/Radio-mellom-rakettkoffertene.gif)


## Del 5.1: 

### Motta status på Igniter og Arm via radio

Vi må lage to ``||logic: Hvis-betingelse||`` med ``||logic: ellers hvis||`` som skal sjekket hver av disse statusene. 

Disse settes inn i ``||radio: når radio mottar receivedNumber||``:

- Hvis ``||radio: receivedNumber = 21||``, er ``||variabel: IgniterStatusLP||`` ``||logic: sann||``. Ellers hvis ``||radio: receivedNumber = 22||``, er ``||variabel: IgniterStatusLP||`` ``||logic: usann||``.
- Hvis ``||radio: receivedNumber = 31||``, er ``||variabel: ArmStatusLP||`` ``||logic: sann||``. Ellers hvis ``||radio: receivedNumber = 32||``, er ``||variabel: ArmStatusLP||`` ``||logic: usann||``.

| Variabel | Radio Sann | Radio Usann |
|:---------|:----------:|:-----------:|
| IgniterStatusLP | 21 | 22 |
| ArmStatusLP | 31 | 32 |

```blocks
radio.onReceivedNumber(function (receivedNumber) {
    if (receivedNumber == 11){
        LinkStatus = true
        sistSettAktiv = input.runningTime()
    }
    if (receivedNumber == 21) {
        IgniterStatusLP = true
    } else if (receivedNumber == 22) {
        IgniterStatusLP = false
    }
    if (receivedNumber == 31) {
        ArmStatusLP = true
    } else if (receivedNumber == 32) {
        ArmStatusLP = false
    }
})
```

## Del 5.2: 

### StatusCheck - ArmStatus:

Videre inni ``||functions: StatusCheck||``, lag en ``||logic: hvis-betingelse||`` som skal lese av ``||pins: P1||`` (Arm-knappen). 

- Hvis ``||pins: P1||`` er lik 0, skal ``||variables: ArmStatus||`` settes til ``||logic: sann||``. Ellers skal ``||variables: ArmStatus||`` settes til ``||logic: usann||``.

```blocks
function StatusCheck () {
	SelfStatus = true
    if (pins.digitalReadPin(DigitalPin.P1) == 0) {
        ArmStatus = true
    } else {
        ArmStatus = false
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
    if (IgniterStatusLP) {
        strip.setPixelColor(2, neopixel.colors(NeoPixelColors.Green))
    } else {
        strip.setPixelColor(2, neopixel.colors(NeoPixelColors.Red))
    }
    if (ArmStatusLP) {
        strip.setPixelColor(3, neopixel.colors(NeoPixelColors.Green))
    } else {
        strip.setPixelColor(3, neopixel.colors(NeoPixelColors.Red))
    }
    if (Klar) {
        strip.setPixelColor(4, neopixel.colors(NeoPixelColors.Green))
    } else {
        strip.setPixelColor(4, neopixel.colors(NeoPixelColors.Red))
    }
    strip.show()
}
```

## Del 5.3: 

### Fullføre StatusCheck

For å sjekke om alle systemene er klare for oppskytning, må vi lage en ``||logic: Hvis-betingelse||`` som sjekker om alle statusene vår er lik ``||logic: sann||``. Den skal settes inn i funksjonen ``||functions: StatusCheck||``.

Vi skal sjekke: ``||variables: SelfStatus||``, ``||variables: LinkStatus||``, ``||variables: ArmStatus||``, ``||variables: IgniterStatusLP||`` og ``||variables: ArmStatusLP||``.

Hvis alle statusene over er lik``||logic: sann||``, da skal ``||variables: Klar||`` settes lik ``||logic: sann||``. Eller lik ``||logic: usann||``. Legg ved et bilde på hvert at tilfellene for se om ``||variables: Klar||`` er ``||logic: sann||`` eller ``||logic: usann||``.

```blocks
function StatusCheck () {
	SelfStatus = true
    if (pins.digitalReadPin(DigitalPin.P1) == 0) {
        ArmStatus = true
    } else {
        ArmStatus = false
    }
    if (SelvStatus && LinkStatus && ArmStatus && IgniterStatusLP && ArmStatusLP) {
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
    if (IgniterStatusLP) {
        strip.setPixelColor(2, neopixel.colors(NeoPixelColors.Green))
    } else {
        strip.setPixelColor(2, neopixel.colors(NeoPixelColors.Red))
    }
    if (ArmStatusLP) {
        strip.setPixelColor(3, neopixel.colors(NeoPixelColors.Green))
    } else {
        strip.setPixelColor(3, neopixel.colors(NeoPixelColors.Red))
    }
    if (Klar) {
        strip.setPixelColor(4, neopixel.colors(NeoPixelColors.Green))
    } else {
        strip.setPixelColor(4, neopixel.colors(NeoPixelColors.Red))
    }
    strip.show()
}
```

## Del 5.4: 

### Oppdatere StatusCheck

Inni ``||basic: Gjenta for alltid||``:

Vi ønsker å kjøre en systemsjekk hver gang vi trykker ned knappen til System Status Check på kofferten. Når knappen trykkes ned, kan vi ``|pins: lese digitalverdi fra  P5||`` lik 0. 

Så hvis ``||pins: P5 = 0||``, skal alle NeoPixels bli ``||neopixel: røde||`` i 100 ms, og så skal vi kalle opp funksjonen ``||functions: StatusCheck||``.

For å gi blokken ``||basic: Gjenta for alltid||`` litt pusterom når den jobber, legg inn en liten ``||basic: pause ||`` på 100ms på slutten.

```blocks
let strip: neopixel.Strip = null
basic.forever(function () {
    StatusCheck()
    if (pins.digitalReadPin(DigitalPin.P5) == 0) {
        strip.showColor(neopixel.colors(NeoPixelColors.Red))
        basic.pause(100)
        StatusCheck()
    }
    basic.pause(100)
})
function StatusCheck () {
	SelfStatus = true
    if (pins.digitalReadPin(DigitalPin.P1) == 0) {
        ArmStatus = true
    } else {
        ArmStatus = false
    }
    if (SelvStatus && LinkStatus && ArmStatus && IgniterStatusLP && ArmStatusLP) {
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
    if (IgniterStatusLP) {
        strip.setPixelColor(2, neopixel.colors(NeoPixelColors.Green))
    } else {
        strip.setPixelColor(2, neopixel.colors(NeoPixelColors.Red))
    }
    if (ArmStatusLP) {
        strip.setPixelColor(3, neopixel.colors(NeoPixelColors.Green))
    } else {
        strip.setPixelColor(3, neopixel.colors(NeoPixelColors.Red))
    }
    if (Klar) {
        strip.setPixelColor(4, neopixel.colors(NeoPixelColors.Green))
    } else {
        strip.setPixelColor(4, neopixel.colors(NeoPixelColors.Red))
    }
    strip.show()
}
```

## Del 6: @unplugged

### Oppskytning av raketten

Nå skal alle systemene på begge koffertene fungere, og vi er klare for å bygge funksjonene for å kunne skyte opp raketten!

![Oppskytning-rakett-4.gif](https://i.postimg.cc/CxV5Qx0C/Oppskytning-rakett-4.gif)


## Del 6.1: 

### Sette opp Launch-funksjon

For å kunne skyte opp raketten, trenger vi å lage en ny funksjonen som vi kaller ``||functions: Launch||``. 

I ``||basic: gjenta for alltid||`` skal vi lage en ``||logic: Hvis-betingelse||`` som sier at vi skal kalle opp funksjonen ``||functions: Launch||`` hvis ``||pins: skriver digital pin P11 = 0||``(Launch-knappen). 

```blocks
let strip: neopixel.Strip = null
basic.forever(function () {
    StatusCheck()
    if (pins.digitalReadPin(DigitalPin.P5) == 0) {
        strip.showColor(neopixel.colors(NeoPixelColors.Red))
        basic.pause(100)
        StatusCheck()
    }
    if (pins.digitalReadPin(DigitalPin.P11) == 0) {
        Launch()
    }
    basic.pause(100)
})
function Launch () {
	
}
function StatusCheck () {
	SelfStatus = true
    if (pins.digitalReadPin(DigitalPin.P1) == 0) {
        ArmStatus = true
    } else {
        ArmStatus = false
    }
    if (SelvStatus && LinkStatus && ArmStatus && IgniterStatusLP && ArmStatusLP) {
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
    if (IgniterStatusLP) {
        strip.setPixelColor(2, neopixel.colors(NeoPixelColors.Green))
    } else {
        strip.setPixelColor(2, neopixel.colors(NeoPixelColors.Red))
    }
    if (ArmStatusLP) {
        strip.setPixelColor(3, neopixel.colors(NeoPixelColors.Green))
    } else {
        strip.setPixelColor(3, neopixel.colors(NeoPixelColors.Red))
    }
    if (Klar) {
        strip.setPixelColor(4, neopixel.colors(NeoPixelColors.Green))
    } else {
        strip.setPixelColor(4, neopixel.colors(NeoPixelColors.Red))
    }
    strip.show()
}
```

## Del 6.2: 

### Sende Launch-kommando:

Når vi har trykket på Launch-knappen og satt ``||pins: skriver digital pin P11 = 0||``, hopper vi inn i ``||functions: Launch||``.

Launch-kommandoen inne i``||functions: Launch||`` skal kun få lov til å bli utført hvis ``||variables: Klar||`` er ``||logic: sann||``.

Lag en pil på skjermen til microbiten for å vise at raketten blir skutt opp. Du må også sende en egen ``||radio: radio send tall||`` til LaunchPAD-kofferten. Vel et tall som ikke kan misforstås. Til slutt skal ``||variables: Klar||`` settes til ``||logic: usann||``.

```blocks
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
    }
}
```

## Del 6.4: 

### Buzzer:

For å varsle at raketten er klar til å skytes opp, skal en buzzer bli skrudd på når alle systemene er på og armerte.

For å få til dette må vi lage en liten ``||logic: hvis-betingelse||`` inne i ``||basic: gjenta for alltid||`` som sier at ``||pins: P13||`` (Buzzer) og ``||pins: P14||`` (Launch Button LED) skal skrus på (settes til 1) når variabelen ``||variables: Klar||`` er ``||logic: sann||``. Eller skal de skrus av (settes til 0).

```blocks
let strip: neopixel.Strip = null
basic.forever(function () {
    StatusCheck()
    if (pins.digitalReadPin(DigitalPin.P5) == 0) {
        strip.showColor(neopixel.colors(NeoPixelColors.Red))
        basic.pause(100)
        StatusCheck()
    }
    if (pins.digitalReadPin(DigitalPin.P11) == 0) {
        Launch()
    }
    if (Klar) {
        pins.digitalWritePin(DigitalPin.P13, 1)
        pins.digitalWritePin(DigitalPin.P14, 1) 
    } else {
        pins.digitalWritePin(DigitalPin.P13, 0)
        pins.digitalWritePin(DigitalPin.P14, 0)
    }
    basic.pause(100)
})
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
    }
}
function StatusCheck () {
	SelfStatus = true
    if (pins.digitalReadPin(DigitalPin.P1) == 0) {
        ArmStatus = true
    } else {
        ArmStatus = false
    }
    if (SelvStatus && LinkStatus && ArmStatus && IgniterStatusLP && ArmStatusLP) {
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
    if (IgniterStatusLP) {
        strip.setPixelColor(2, neopixel.colors(NeoPixelColors.Green))
    } else {
        strip.setPixelColor(2, neopixel.colors(NeoPixelColors.Red))
    }
    if (ArmStatusLP) {
        strip.setPixelColor(3, neopixel.colors(NeoPixelColors.Green))
    } else {
        strip.setPixelColor(3, neopixel.colors(NeoPixelColors.Red))
    }
    if (Klar) {
        strip.setPixelColor(4, neopixel.colors(NeoPixelColors.Green))
    } else {
        strip.setPixelColor(4, neopixel.colors(NeoPixelColors.Red))
    }
    strip.show()
}
```

## Del 6.5: 

### BuzzerBlink-funksjon:

Når raketten skytes opp, skal vi få ``||pins: P13||`` (Buzzer) og ``||pins: P14||`` (Launch Button LED) til å tute og blinke 3 ganger.

Lag en ny funksjon: ``||functions: BuzzerBlink||``. Denne funksjonen skal kalles opp først inne i ``||functions: Launch||`` hvis ``||variables: Klar||`` er ``||logic: sann||``.

Sett ``||pins: P13||`` og ``||pins: P14||`` til HØY (1) og LAV (0) 3 ganger, med 100ms ``||basic: pause||`` mellom hver HØY og LAV.

```blocks
function BuzzerBlink () {
    pins.digitalWritePin(DigitalPin.P13, 1)
    pins.digitalWritePin(DigitalPin.P14, 1)
    basic.pause(100)
    pins.digitalWritePin(DigitalPin.P13, 0)
    pins.digitalWritePin(DigitalPin.P14, 0)
    basic.pause(100)
    pins.digitalWritePin(DigitalPin.P13, 1)
    pins.digitalWritePin(DigitalPin.P14, 1)
    basic.pause(100)
    pins.digitalWritePin(DigitalPin.P13, 0)
    pins.digitalWritePin(DigitalPin.P14, 0)
    basic.pause(100)
    pins.digitalWritePin(DigitalPin.P13, 1)
    pins.digitalWritePin(DigitalPin.P14, 1)
    basic.pause(100)
    pins.digitalWritePin(DigitalPin.P13, 0)
    pins.digitalWritePin(DigitalPin.P14, 0)
}
function Launch () {
    if (Klar) {
        BuzzerBlink()
        basic.showLeds(`
            . . # . .
            . # # # .
            # . # . #
            . . # . .
            . . # . .
            `)
        radio.sendNumber(42)
        Klar = false
    }
}
```

## Del 6.6: 

### Rearm av kofferten:

Når vi har sendt opp raketten vår, skal vi ikke få lov til å sende opp en ny rakett før arm-bryteren er skrudd av igjen og systemet er resatt. 

For å gjøre dette må vi lage en ny funksjon: ``||functions: Rearm||``. Den skal kalles opp fra bunnen i ``||functions: Launch||``, inni ``||logic: hvis-betingelsen||``.

Start med å skru av lysene til NeoPixels. Så skal vi bruke en ``||loops: gjenta hvis sann-løkke||`` som er aktiv så lange  ``||pins: digital pin P1 = 0||``.

Inni denne ``||loops: løkken||`` skal vi få skjermen på microbiten og Rearm LED til å blinke. Dette gjør vi ved å stru ``||pins: P8||`` AV (0) og PÅ (1), og få et leddlys på microbitens skjerm til å skru seg AV og PÅ.

```blocks
let strip: neopixel.Strip = null
function Rearm () {
    strip.clear()
    strip.show()
    while (pins.digitalReadPin(DigitalPin.P1) == 0) {
        pins.digitalWritePin(DigitalPin.P8, 0)
        basic.showLeds(`
            . . . . .
            . # # # .
            . # . # .
            . # # # .
            . . . . .
            `)
        pins.digitalWritePin(DigitalPin.P8, 1)
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
        BuzzerBlink()
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
function BuzzerBlink () {
    pins.digitalWritePin(DigitalPin.P13, 1)
    pins.digitalWritePin(DigitalPin.P14, 1)
    basic.pause(100)
    pins.digitalWritePin(DigitalPin.P13, 0)
    pins.digitalWritePin(DigitalPin.P14, 0)
    basic.pause(100)
    pins.digitalWritePin(DigitalPin.P13, 1)
    pins.digitalWritePin(DigitalPin.P14, 1)
    basic.pause(100)
    pins.digitalWritePin(DigitalPin.P13, 0)
    pins.digitalWritePin(DigitalPin.P14, 0)
    basic.pause(100)
    pins.digitalWritePin(DigitalPin.P13, 1)
    pins.digitalWritePin(DigitalPin.P14, 1)
    basic.pause(100)
    pins.digitalWritePin(DigitalPin.P13, 0)
    pins.digitalWritePin(DigitalPin.P14, 0)
}
```


## Del 6.7: 

### Rearm-funksjonen etter rearmeringen:

Etter at arm-knappen er skrudd av, vil vi hoppe ut av ``||loops: gjenta hvis sann-løkken||``. 

Skriv ``||pins:digital pin P8 = 0||``, sett NeoPixels til ``||neopixel: Purple||`` og ``||basic: tøm skjermen||`` til microbiten.

Avslutt med en ``||basic: pause||`` i 200 ms. før vi kaller opp funksjonen ``||functions: Initialize||`` for å restarte rakett-kofferten.

```blocks
let strip: neopixel.Strip = null
function Rearm () {
    strip.clear()
    strip.show()
    while (pins.digitalReadPin(DigitalPin.P1) == 0) {
        pins.digitalWritePin(DigitalPin.P8, 0)
        basic.showLeds(`
            . . . . .
            . # # # .
            . # . # .
            . # # # .
            . . . . .
            `)
        pins.digitalWritePin(DigitalPin.P8, 1)
        basic.showLeds(`
            . . . . .
            . # # # .
            . # # # .
            . # # # .
            . . . . .
            `)
    }
    pins.digitalWritePin(DigitalPin.P8, 0)
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
let strip: neopixel.Strip = null
let SelfStatus = false
let LinkStatus = false
let ArmStatus = false
let IgniterStatusLP = false
let ArmStatusLP = false
let Klar = false
function Initialize () {
	SelfStatus = false
    LinkStatus = false
    ArmStatus = false
    IgniterStatusLP = false
    ArmStatusLP = false
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

## Del 7: @unplugged

### Bytte Tutorial

ControllerPAD er ferdig programmert!

Bytt veiledning, og fullfør koden til LaunchPAD-kofferten!

![Launch-PAD.jpg](https://i.postimg.cc/Sxhw0Mck/Launch-PAD.jpg)

## Del 7:

### Gratulerer! Du er nå ferdig å programmere ControllerPAD! 

``||math: Last ned||`` koden på en micro:bit og sjekk at den fungerer som den skal.

```blocks
let Klar = false
let ArmStatus = false
let SelfStatus = false
let ArmStatusLP = false
let IgniterStatusLP = false
let sistSettAktiv = 0
let LinkStatus = false
let strip: neopixel.Strip = null
strip = neopixel.create(DigitalPin.P0, 5, NeoPixelMode.RGB)
radio.setGroup(1)
radio.setTransmitPower(7)
pins.digitalWritePin(DigitalPin.P15, 1)
let oppdateringsfrekvens = 200
Initialize()
function Initialize () {
    SelfStatus = false
    LinkStatus = false
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
basic.forever(function () {
    StatusCheck()
    if (pins.digitalReadPin(DigitalPin.P5) == 0) {
        strip.showColor(neopixel.colors(NeoPixelColors.Red))
        basic.pause(100)
        StatusCheck()
    }
    if (pins.digitalReadPin(DigitalPin.P11) == 0) {
        Launch()
    }
    if (Klar) {
        pins.digitalWritePin(DigitalPin.P13, 1)
        pins.digitalWritePin(DigitalPin.P14, 1)
    } else {
        pins.digitalWritePin(DigitalPin.P13, 0)
        pins.digitalWritePin(DigitalPin.P14, 0)
    }
    basic.pause(100)
})
function StatusCheck () {
    SelfStatus = true
    if (pins.digitalReadPin(DigitalPin.P1) == 0) {
        ArmStatus = true
    } else {
        ArmStatus = false
    }
    if (SelfStatus && LinkStatus && IgniterStatusLP && ArmStatusLP && ArmStatus) {
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
    if (IgniterStatusLP) {
        strip.setPixelColor(2, neopixel.colors(NeoPixelColors.Green))
    } else {
        strip.setPixelColor(2, neopixel.colors(NeoPixelColors.Red))
    }
    if (ArmStatusLP) {
        strip.setPixelColor(3, neopixel.colors(NeoPixelColors.Green))
    } else {
        strip.setPixelColor(3, neopixel.colors(NeoPixelColors.Red))
    }
    if (Klar) {
        strip.setPixelColor(4, neopixel.colors(NeoPixelColors.Green))
    } else {
        strip.setPixelColor(4, neopixel.colors(NeoPixelColors.Red))
    }
    strip.show()
}
radio.onReceivedNumber(function (receivedNumber) {
    if (receivedNumber == 11) {
        LinkStatus = true
        sistSettAktiv = input.runningTime()
    }
    if (receivedNumber == 21) {
        IgniterStatusLP = true
    } else if (receivedNumber == 22) {
        IgniterStatusLP = false
    }
    if (receivedNumber == 31) {
        ArmStatusLP = true
    } else if (receivedNumber == 32) {
        ArmStatusLP = false
    }
})
function Launch () {
    if (Klar) {
        BuzzerBlink()
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
function Rearm () {
    strip.clear()
    strip.show()
    while (pins.digitalReadPin(DigitalPin.P1) == 0) {
        pins.digitalWritePin(DigitalPin.P8, 0)
        basic.showLeds(`
            . . . . .
            . # # # .
            . # . # .
            . # # # .
            . . . . .
            `)
        pins.digitalWritePin(DigitalPin.P8, 1)
        basic.showLeds(`
            . . . . .
            . # # # .
            . # # # .
            . # # # .
            . . . . .
            `)
    }
    pins.digitalWritePin(DigitalPin.P8, 0)
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
function BuzzerBlink () {
    pins.digitalWritePin(DigitalPin.P13, 1)
    pins.digitalWritePin(DigitalPin.P14, 1)
    basic.pause(100)
    pins.digitalWritePin(DigitalPin.P13, 0)
    pins.digitalWritePin(DigitalPin.P14, 0)
    basic.pause(100)
    pins.digitalWritePin(DigitalPin.P13, 1)
    pins.digitalWritePin(DigitalPin.P14, 1)
    basic.pause(100)
    pins.digitalWritePin(DigitalPin.P13, 0)
    pins.digitalWritePin(DigitalPin.P14, 0)
    basic.pause(100)
    pins.digitalWritePin(DigitalPin.P13, 1)
    pins.digitalWritePin(DigitalPin.P14, 1)
    basic.pause(100)
    pins.digitalWritePin(DigitalPin.P13, 0)
    pins.digitalWritePin(DigitalPin.P14, 0)
}
control.inBackground(function () {
    while (true) {
        radio.sendNumber(11)
        if (input.runningTime() - sistSettAktiv > 3 * oppdateringsfrekvens) {
            LinkStatus = false
            IgniterStatusLP = false
            ArmStatusLP = false
        }
        basic.pause(oppdateringsfrekvens)
    }
})
```


```package
neopixel=github:microsoft/pxt-neopixel#v0.7.3
```

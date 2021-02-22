# ControllerPAD Tutorial del 1

## Del 1: Ved start

I blokken ``||basic: ved start||`` skal si sette opp de funksjonene som kun skal settes i gang når kofferten skrus på.

## Del 1.1

Det første vi skal sette opp er en ``||radio: radiogruppe||``. Den skal være et tall mellom 0 og 255. (Bruker gr. 1 som eksempel her.)

Sett også opp at ``||radio: sendereffekt||`` skal være lik 7. (Gir oss sterkere radiosender.)

```blocks
radio.setGroup(1)
radio.setTransmitPower(7)
```
## Del 1.2

Sett opp NeoPixels for kofferten. Skal være 5 NeoPixels.

Sett at digital pin (``||pins: P15||``) skal skrives til 1. 

```blocks
let strip = neopixel.create(DigitalPin.P0, 5, NeoPixelMode.RGB)
radio.setGroup(1)
radio.setTransmitPower(7)
pins.digitalWritePin(DigitalPin.P15, 1)
```

## Del 2: Initialize

For å visualisere oppstartsekvensen til RocketLink-M, må vi lage en funksjon som vi kaller: ``||functions: Initialize||``.

Det er flere steder i koden vår vi skal kalle opp ``||functions: Initialize||``. Den første er i ``||basic: ved start||``.

```blocks
let strip = neopixel.create(DigitalPin.P0, 5, NeoPixelMode.RGB)
radio.setGroup(1)
radio.setTransmitPower(7)
pins.digitalWritePin(DigitalPin.P15, 1)
Initialize()
function Initialize () {
	
}
```

## Del 2.1

Det første vi må gjøre her er å lage 4 variabler: ``||variable: SelfStatus||``, ``||variable: LinkStatus||``, ``||variable: ArmStatus||`` og ``||variable: Klar||``.

SelfStatus sjekker om kofferten er på, LinkStatus sjekker om det er kontakt med den andre kofferten, ArmStatus sjekker om Arm-knappen er skrudd på, og Ready sjekker om alle systemene på kofferten er på.

Sett alle disse 4 variablene til er være ``||logic: usann||``

```blocks
let SelfStatus = false
let LinkStatus = false
let ArmStatus = false
let Klar = false
function Initialize () {
	SelfStatus = false
    LinkStatus = false
    ArmStatus = false
    Klar = false
}
```

## Del 2.2

Vi skal teste lysene på NeoPixel strip, hvor vi skal sette alle lysene til å lyse lilla (``||neopixel: Purple||``).

```blocks
let strip: neopixel.Strip = null
let SelfStatus = false
let LinkStatus = false
let ArmStatus = false
let Klar = false
function Initialize () {
	SelfStatus = false
    LinkStatus = false
    ArmStatus = false
    Klar = false
    strip.showColor(neopixel.colors(NeoPixelColors.Purple))
}
```

## Del 2.3

Vi lager en liten animasjon på skjermen til micro:bit under oppstartsekvensen. Du kan velge selv hvordan den skal se ut. Dette er kun et forslag.

Avslutt med en liten ``||basic: pause||`` på 200 ms.

```blocks
let strip: neopixel.Strip = null
let SelfStatus = false
let LinkStatus = false
let ArmStatus = false
let Klar = false
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
    basic.pause(200)
}
```

## Del 3: StatusCheck

For at systemet vårt skal hele tiden sjekke om det skjer noen status-endringer, trenger vi å lage en ny funksjon: ``||functions: StatusCheck||``.

Denne funskjonen skal kalles opp fra en ``||basic: gjenta for alltid||``.

```blocks
basic.forever(function () {
    StatusCheck()
})
function StatusCheck () {
	
}
```

## Del 3.1

Når vi kjører en ``||variable: StatusCheck||``, vet vi at ``||variable: SelfStatus||`` er ``||logic: sann||``.

Lag en ``||logic: Hvis-betingelse||`` som skal lese av ``||pins: P1||``. Hvis ``||pins: P1||`` er lik 0, skal ``||variable: ArmStatus||`` settes til ``||logic: sann||``. Hvis ikke, skal ``||variable: ArmStatus||`` settes til ``||logic: usann||``.

```blocks
function StatusCheck () {
	SelfStatus = true
    if (pins.digitalReadPin(DigitalPin.P1) == 0) {
        ArmStatus = true
    } else {
        ArmStatus = false
    }
}
```

## Del 3.2

Vi er ikke ferdig med ``||functions: StatusCheck||``. Vi må fikse et par ting før vi kommer tilbake hit.


## Del 4: Sjekke Linkstatus til rakettkoffertene

For å kunne sjekke link mellom koffertene må bruke noe som kalles for «active sensing». 
Begge koffertene sender en radiomelding mellom seg med gitte intervaller som sier «Hei, jeg er ikke avskrudd». 


## Del 4.1: Sjekke at man mottar signal fra den andre kofferten

Lag to variabler: ``||variable: LinkStatus||`` og ``||variable: sistSettAktiv||``. 

Inni en ``||radio:når radio mottar receivedNumber||``: Sett ``||variable: LinkStatus||`` til ``||logic: sann||`` og sett ``||variable: sistSettAktiv||`` til ``||input: kjøretid (ms)||``.

```blocks
radio.onReceivedNumber(function (receivedNumber) {
    LinkStatus = true
    sistSettAktiv = input.runningTime()
})
```

## Del 4.2: Lage en oppdateringsfrekvens

Lag en variabel som du kaller ``||variable: oppdateringsfrekvens||``. Sett den inn under  ``||basic: ved start||``, og la den være 200 ms.

```blocks
let strip = neopixel.create(DigitalPin.P0, 5, NeoPixelMode.RGB)
radio.setGroup(1)
radio.setTransmitPower(7)
pins.digitalWritePin(DigitalPin.P15, 1)
let opppdateringsfrekvens = 200
```

## Del 4.3: Sjekke om man slutter å motta signal fra den andre kofferten 

For å finne ut om kofferten har sluttet å motta signal fra den andre kofferten, må vi lage en liten funksjon som vi setter inn i  ``||controll: run in Background||``. (Finner du i menuen: Kontroll)

Inni her skal lage en  ``||loops: while-løkke||``. Denne løkken vil kjøre så lenge kofferten er på.

Send tallet 10 med radio. 

Nå skal vi sjekke om den ene av koffertene ikke mottar et signal på 3x ``||variabel: oppdateringsfrekvens||``. Hvis den er lengre enn det, kan den regne med at den andre kofferten er skrudd av.

Vi skal sjekke om ``||input: kjøretid (ms)||`` minus (-) ``||variabel: sistSettAktiv||`` er større enn (>) 3x ``||variabel: oppdateringsfrekvens||``

Hvis dette er sant, skal ``||variabel: LinkStatus||`` settes til ``||logic: usann||``

Avslutt med en ``||basic: pause||`` lik ``||variabel: oppdateringsfrekvens||``.


```blocks
control.inBackground(function () {
    while (true) {
        radio.sendNumber(10)
        if (input.runningTime() - sistSettAktiv > 3 * oppdateringsfrekvens) {
            LinkStatus = false
        }
    }
    basic.pause(oppdateringsfrekvens)
})
```

## Del 5: Motta statusoppdatering fra LaunchPAD-kofferten

Det er to til statuser vi får fra LaunchPAD: ``||variabel: IgniterStatusLP||`` og ``||variabel: ArmStatusLP||``. Disse to sjekker om igniterne er koblet ordentlig til raketten, og om Arm-knappen på LaunchPAD-kofferten er skrudd på.

Vi må lage to ``||logic: Hvis-betingelse||`` som skal sjekket hver sin status. 

Hvis ``||radio: receivedNumber = 21||``, er ``||variabel: IgniterStatusLP||`` ``||logic: sann||``. Ellers er den ``||logic: usann||``.

Hvis ``||radio: receivedNumber = 31||``, er ``||variabel: ArmStatusLP||`` ``||logic: sann||``. Ellers er den ``||logic: usann||``.

```blocks
radio.onReceivedNumber(function (receivedNumber) {
    LinkStatus = true
    sistSettAktiv = input.runningTime()
    if (receivedNumber == 21) {
        IgniterStatusLP = true
    } else {
        IgniterStatusLP = false
    }
    if (receivedNumber == 31) {
        ArmStatusLP = true
    } else {
        ArmStatusLP = false
    }
})
```

## Del 6: Fullføre StatusCheck

For å sjekke om alle systemene er klare for oppskytning, må vi lage en ``||logic: Hvis-betingelse||`` som sjekker om alle statusene vår er lik ``||logic: sann||``. Den skal settes inn i funksjonen ``||functions: StatusCheck||``.

Vi skal sjekke: ``||variable: SelfStatus||``, ``||variable: LinkStatus||``, ``||variable: ArmStatus||``, ``||variable: IgniterStatusLP||`` og ``||variable: ArmStatusLP||``.

Lag en ny variable: ``||variable: Klar||``. Hvis alle statusene over er lik``||logic: sann||``, da skal ``||variable: Klar||`` settes lik ``||logic: sann||``. Eller lik ``||logic: usann||``. 

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
    } else {
        Klar = false	
    }
}
```
## Del 7: Forever-løkken

``||basic: Gjenta for alltid||`` er den blokken som til en hver tid står og jobber, og sjekker opp statusen til systemet vårt. Vi skal nå gjøre den ferdig.

Vi ønsker å kjøre en systemsjekk hver gang vi trykker ned knappen til System Status Check på kofferten. Når knappen trykkes ned, settes ``|pins: digital pin P5 ||`` til 0. 

Så hvis ``||pins: P5 = 0||``, skal alle NeoPixels bli ``||neopixel: røde||`` i 100 ms, og så skal vi kalle opp funskjonen ``||functions: StatusCheck||``.

```blocks
let strip: neopixel.Strip = null
basic.forever(function () {
    StatusCheck()
    if (pins.digitalReadPin(DigitalPin.P5) == 0) {
        strip.showColor(neopixel.colors(NeoPixelColors.Red))
        basic.pause(100)
        StatusCheck()
    }
})
function StatusCheck () {
	
}
```
## Del 8: Launch-funksjonen

For å kunne skyte opp raketten, trenger vi å lage en ny funksjonen som vi kaller ``||functions: Launch||``. 

I ``||basic: gjenta for alltid||`` skal vi lage en ``||logic: Hvis-betingelse||`` som sier at vi skal kalle opp funksjonen ``||functions: Launch||`` når vi ``||pins: skriver digital pin P11 = 0||``. 

Inne i 


```package
neopixel=github:microsoft/pxt-neopixel#v0.7.3
```
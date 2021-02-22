# ControllerPAD Tutorial del 1

## Del 1: Ved start

I blokken ``||basic: ved start||`` skal si sette opp de funksjonene som kun skal settes i gang når kofferten skrus på.

## Del 1.1

Det første vi skal sette opp er en ``||radio: radiogruppe||``. Dere kan velge et tall mellom 0 og 255. (Bruker gr. 1 som eksempel her.)

Sett også opp at ``||radio: sendereffekt||`` skal være lik 7. (Gir oss sterkere radiosender.)

```blocks
radio.setGroup(1)
radio.setTransmitPower(7)
```
## Del 1.2

Sett opp NeoPixels for kofferten. Skal være 5 NeoPixels på ``||pins: P0||``.

Sett at digital pin (``||pins: P15||``) skal skrives til 1. (Dette er lyset på Status Check-knappen på kofferten.)

```blocks
let strip = neopixel.create(DigitalPin.P0, 5, NeoPixelMode.RGB)
radio.setGroup(1)
radio.setTransmitPower(7)
pins.digitalWritePin(DigitalPin.P15, 1)
```

## Del 2: Initialize

For å kjøre oppstartsekvensen til RocketLink-M, må vi lage en funksjon som vi kaller: ``||functions: Initialize||``.

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

## Del 2.1: Initialize function

Det første vi må gjøre inne i ``||functions: Initialize||`` er å lage 4 variabler: ``||variable: SelfStatus||``, ``||variable: LinkStatus||``, ``||variable: ArmStatus||`` og ``||variable: Klar||``.

``||variable: SelfStatus||`` sjekker om kofferten er på, ``||variable: LinkStatus||`` sjekker om det er kontakt med den andre kofferten, ``||variable: ArmStatus||`` sjekker om Arm-knappen er skrudd på, og ``||variable: Klar||`` sjekker om alle systemene på kofferten er på.

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

Videre inne i ``||functions: Initialize||`` skal vi teste lysene på NeoPixel strip. Sett alle lysene til å lyse lilla (``||neopixel: Purple||``).

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

For at systemet vårt hele tiden skal sjekke om det skjer noen status-endringer, trenger vi å lage en ny funksjon: ``||functions: StatusCheck||``.

Denne funksjonen skal kalles opp fra en ``||basic: gjenta for alltid||``.

```blocks
basic.forever(function () {
    StatusCheck()
})
function StatusCheck () {
	
}
```

## Del 3.1

Når vi kjører en ``||variable: StatusCheck||``, vet vi at ``||variable: SelfStatus||`` er ``||logic: sann||``. (Kofferten er jo på...)

Videre, lag en ``||logic: Hvis-betingelse||`` som skal lese av ``||pins: P1||`` (Arm-knappen). Hvis ``||pins: P1||`` er lik 0, skal ``||variable: ArmStatus||`` settes til ``||logic: sann||``. Ellers skal ``||variable: ArmStatus||`` settes til ``||logic: usann||``.

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

Begge koffertene sender en radiomelding mellom seg med gitte intervaller som sier «Hei, jeg er ikke avskrudd». Men vi trenger noe som sier ifra hvis det har gått for lang tid siden sist vi fikk en radiomelding fra den andre kofferten.


## Del 4.1: Sjekke at man mottar signal fra den andre kofferten

Lag to variabler: ``||variable: LinkStatus||`` og ``||variable: sistSettAktiv||``. 

Inni en ``||radio:når radio mottar receivedNumber||``: Sett ``||variable: LinkStatus||`` til ``||logic: sann||`` og sett ``||variable: sistSettAktiv||`` til ``||input: kjøretid (ms)||``. 

I en micro:bit, kjører det en intern klokke som heter ``||input: kjøretid (ms)||``. Ved å sette ``||variable: sistSettAktiv||`` lik ``||input: kjøretid (ms)||`` ved gitte intervaller, kan vi lett se om tidsforskjellen mellom ``||variable: sistSettAktiv||`` og ``||input: kjøretid (ms)||`` blir større enn 3x gitt tidsinterval.

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

For å finne ut om kofferten har sluttet å motta signal fra den andre kofferten, må vi lage en liten funksjon som vi setter inn i  ``||controll: run in Background||``. (Finner du i menuen: Styring)

Inni her skal vi lage en  ``||loops: while-løkke||``. Denne løkken vil kjøre så lenge kofferten er på.

Inni ``||loops: while-løkken||`` skal vi send tallet 10 med radio. 

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

Det er to til statuser vi får fra LaunchPAD: ``||variabel: IgniterStatusLP||`` og ``||variabel: ArmStatusLP||``. (Lag disse to variablene.) Disse to sjekker om igniterne er koblet ordentlig til raketten, og om Arm-knappen på LaunchPAD-kofferten er skrudd på.

Vi må lage to ``||logic: Hvis-betingelse||`` som skal sjekket hver av disse statusene. 

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

Hvis alle statusene over er lik``||logic: sann||``, da skal ``||variable: Klar||`` settes lik ``||logic: sann||``. Eller lik ``||logic: usann||``. Legg ved et bilde på hvert at tilfellene for se om ``||variabel: Klar||`` er ``||logic: sann||`` eller ikke.

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
## Del 8.1: Launch-funksjonen

For å kunne skyte opp raketten, trenger vi å lage en ny funksjonen som vi kaller ``||functions: Launch||``. 

I ``||basic: gjenta for alltid||`` skal vi lage en ``||logic: Hvis-betingelse||`` som sier at vi skal kalle opp funksjonen ``||functions: Launch||`` hvis ``||pins: skriver digital pin P11 = 0||``. 

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
})
function StatusCheck () {
	
}
function Launch () {
	
}
```

## Del 8.2: Launch-funksjon

Når vi har trykket på Launch-knappen og satt ``||pins: skriver digital pin P11 = 0||``, hopper vi inn i ``||functions: Launch||``.

Launch-kommandoen inne i``||functions: Launch||`` skal kun få lov til å bli utført hvis ``||variabel: Klar||`` er ``||logic: sann||``.

Lag en pil på skjermen til microbiten for å vise at raketten blir skutt opp. Du må også sende en egen ``||radio: radio send tall||`` til LaunchPAD-kofferten. Vel et tall som ikke kan misforstås. Til slutt skal ``||variabel: Klar||`` settes til ``||logic: usann||``.

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

## Del 9.1: Rearm av kofferten

Når vi har sendt opp raketten vår, skal vi ikke få lov til å sende opp en ny rakett før arm-bryteren er skrudd av igjen, og systemet er resatt. 

For å gjøre dette må vi lage en ny funksjon: ``||functions: Rearm||``. Den skal kalles opp fra bunnen i ``||functions: Launch||``).

Start med å skru av lysene til NeoPixels. Så skal vi lage en ``||loops: while-løkke||`` som er aktiv så lange  ``||pins: digital pin P1 = 0||``.

Inni denne ``||loops: while-løkke||`` skal vi få skjermen på microbiten og Rearm LED til å blinke ved å stru ``||pins: P8||`` av og på.

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


## Del 9.2

Etter at arm-knappen er skrudd av, vil vi hoppe ut av ``||loops: while-løkken||``. 

Skru av ``||pins: P8||``, sett NeoPixels til ``||neopixel: Purple||`` og ``||basic: tøm skjermen||`` til microbiten.

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
	
}
```
## Del 10: Sette NeoPixelColors

For å benytte oss av NeoPixelene for å vise om hvert av systemene er på eller av, må vi lage en ny funksjon: ``||functions: NeoPixels||``. Denne skal kalles opp fra bunn av ``||functions: StatusCheck||`` 

I denne skal vi inidividuelt sjekke om opp variablene: ``||variable: SelfStatus||``, ``||variable: LinkStatus||``, ``||variable: IgniterStatusLP||``, ``||variable: ArmStatusLP||`` og ``||variable: Klar||``.

Hvis variabelen er ``||logic: sann||``, skal Neopixel settes til grønn, eller skal den være rød.

Avslutt med å vise lysene: ``||neopixel: show||``

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
function StatusCheck () {
    NeoPixels()
}
```


## Del 11: Buzzer

For å varsle at raketten skal skytes opp, skal en buzzer bli skrudd på når vi alle systemene er på og armerte.

For å få til dette må vi lage en liten ``||logic: Hvis-betingelse||`` som sier at ``||pins: P13||`` (Buzzer) og ``||pins: P14||`` (Launch Button LED) skal skrus på (settes til 1) når variabelen ``||variabel: Klar||`` er ``||logic: sann||``. Eller skal de skrus av (settes til 0).

```blocks
let strip: neopixel.Strip = null
basic.forever(function () {
    StatusCheck()
    if (pins.digitalReadPin(DigitalPin.P5) == 0) {
        strip.showColor(neopixel.colors(NeoPixelColors.Red))
        basic.pause(100)
        StatusCheck()
    }
    if (Klar) {
        pins.digitalWritePin(DigitalPin.P13, 1)
        pins.digitalWritePin(DigitalPin.P14, 1) 
    } else {
        pins.digitalWritePin(DigitalPin.P13, 0)
        pins.digitalWritePin(DigitalPin.P14, 0)
    }
})
function StatusCheck () {
	
}
```

## Del 12: BuzzerBlink funksjon

Etter at raketten er skutt opp, skal vi få ``||pins: P13||`` (Buzzer) og ``||pins: P14||`` (Launch Button LED) til å tute og blinke 3 ganger.

Lag en ny funksjon: ``||functions: BuzzerBlink||``. Denne funksjonen skal kalles opp først inne i ``||functions: Launch||`` hvis ``||variabel: Klar||`` er ``||logic: sann||``.

Sett ``||pins: P13||`` og ``||pins: P14||`` til høy (1) og lav (0) 3 ganger, med 100 ms ``||basic: pause||`` mellom hver høy og lav.

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
        Rearm()
    }
}
```


```package
neopixel=github:microsoft/pxt-neopixel#v0.7.3
```
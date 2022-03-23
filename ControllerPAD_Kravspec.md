# ControllerPAD med Kravspec i riktig rekkefølge


## Del 1: @unplugged

### Programmere ControllerPAD @unplugged

I denne veiledningen skal vi gå igjennom hvordan man programmerer ControllerPAD-kofferten til RocketLink oppskytningssystem. 

Veiledningen er delt i 3 deler:
- Må-krav             (Absolutt nødvendig funksjonalitet for at systemet skal fungere trygt)
- Bør-krav            (Gir økt sikkerhet og en bedre opplevelse)
- «Kjekt-å-ha»-krav   (For fine-schmeckere som ønsker maks ut av RocketLink)

Vi kommer til å hoppe mellom veiledningen for å programmere ControllerPAD og LaunchPAD. Du får beskjed når du skal bytte mellom veiledningene og når du skal teste koden underveis.

Lykke til!

![Controller-PAD.jpg](https://i.postimg.cc/VLM3HRrK/Controller-PAD.jpg)


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

Sett opp en ``||logic: hvis-betingelse ||`` som sjekker om Software Arm Switch (``||pins: les digitalverdi P1 ||``) er 0. 

Hvis ``||pins: P1 = 0 ||``, sett variabelen ``||variables: ArmStatus ||`` til ``||logic: sann||``, ellers sett ``||variables: ArmStatus ||`` til ``||logic: usann||``.

```blocks
basic.forever(function on_forever() {
    if (pins.digitalReadPin(DigitalPin.P1) == 0) {
        ArmStatus = true
    } else {
        ArmStatus = false
    } 
})
```

## Del 1.3:

### Må: Sjekke om Launch-knappen er trykket ned

Lag en ny ``||logic: hvis-betingelse ||`` som sjekker om Launch-button (``||pins: les digitalverdi P11 ||``) er 0. 

Hvis den er det, kjør funksjonen ``||functions: Launch||``.

```blocks
basic.forever(function on_forever() {
    if (pins.digitalReadPin(DigitalPin.P1) == 0) {
        ArmStatus = true
    } else {
        ArmStatus = false
    } 
    if (pins.digitalReadPin(DigitalPin.P11) == 0) {
        Launch()
    }
})
function Launch () {
	
}
```

## Del 1.4:

### Må: Launch-kommando kun ved armering

Launch-kommandoen skal kun få bli sendt hvis ``||variables: ArmStatus ||`` er ``||logic: Sann ||``.

Kommandoen skal sendes med radio til LaunchPAD. Send ``||radio: radio send tall 42 ||``.

```blocks
function Launch () {
	if (ArmStatus) {
        radio.sendNumber(42)
    }
}
```

## Del 1.5:

### Må: Buzzer som varsler om oppskytning

For å si ifra til omgivelsene at systemet er armert, skal vi skru PÅ Buzzer (``||pins: skriv digital til P13 = 1 ||``) når ``||variables: ArmStatus ||`` er ``||logic: sann ||``, og AV når ``||variables: ArmStatus ||`` er ``||logic: usann ||``. 

Skru AV Buzzer (``||pins: skriv digital til P13 = 0 ||``) etter du har sendt Launch-kommandoen.

```blocks
basic.forever(function on_forever() {
    if (pins.digitalReadPin(DigitalPin.P1) == 0) {
        ArmStatus = true
        pins.digitalWritePin(DigitalPin.P13, 1)
    } else {
        ArmStatus = false
        pins.digitalWritePin(DigitalPin.P13, 0)
    } 
    if (pins.digitalReadPin(DigitalPin.P11) == 0) {
        Launch()
    }
})
function Launch () {
	if (ArmStatus) {
        radio.sendNumber(42)
        pins.digitalWritePin(DigitalPin.P13, 0)   
    }
}
```

## Del 1.6:

### Må: Rearmere kofferten før ny oppskytning

Kofferten får ikke skyte opp en ny rakett før den er rearmert. (Armeringsbryteren må skrus AV.)

Så for å "låse" kofferten etter at Launch-kommandoen er sendt, bruk en ``||loops: gjenta hvis sann ||``, og sett rearm-LED ``||pins: skriv digital til P8 ||`` til å blinke så lenge ``||pins: les digital P1 = 0 ||``.

Bruk en ``||basic: pause ||`` på 500 ms mellom hver gang ``||pins: P8 ||`` er 1 og 0.

```blocks
function Launch () {
	if (ArmStatus) {
        radio.sendNumber(42)
        pins.digitalWritePin(DigitalPin.P13, 0)
        while (pins.digitalReadPin(DigitalPin.P1) == 0) {
            pins.digitalWritePin(DigitalPin.P8, 1)
            basic.pause(500)
            pins.digitalWritePin(DigitalPin.P8, 0)
            basic.pause(500)
        }
    }
}
```

## Del 1.7: @unplugged

### Bytte Tutorial

Gratulerer! Vi er nå ferdige med å lage MÅ-koden på ControllerPAD!

Bytt veiledning, og start å lage koden til LaunchPAD-kofferten!

![Launch-PAD.jpg](https://i.postimg.cc/Sxhw0Mck/Launch-PAD.jpg)


## Del 2: @unplugged

### Bør-krav til koffertene

Raketten vil nå kunne bli skutt opp, men for å øke sikkerheten og brukervennligheten til rakettkoffertene, skal vi legge til noen flere funksjoner i koden vår.

![Radio-mellom-rakettkoffertene.gif](https://i.postimg.cc/nL4Rtr4R/Radio-mellom-rakettkoffertene.gif)


## Del 2.1:

### Bør: Sette opp lysene på kofferten

Inni ``||basic: ved start||``:

For å vise status på systemene på kofferten, bruker vi NeoPixels. Det er koblet til 5 NeoPixels på ``||pins: digital pin P0||``. (I menyen ``||neopixel: Neopixel||`` finner du ``||variables: sett strip til||`` ``||neopixel: NeoPixel at pin P0 with 5 leds||``.)

Skriv også at ``||pins: digital pin P15 = 1||``. Dette er lyset på Status Check-knappen på kofferten.

```blocks
radio.setGroup(1)
radio.setTransmitPower(7)
let strip = neopixel.create(DigitalPin.P0, 5, NeoPixelMode.RGB)
pins.digitalWritePin(DigitalPin.P15, 1)
```

## Del 2.2:

### Bør: SelfStatus

Øverst i ``||basic: gjenta for alltid||`` skal vi sette en ny variabel ``||variables: SelfStatus||`` til ``||logic: sann||``. (Kofferten er jo på...)

Vi skal også legge inn en liten ``||basic: pause||`` på 100ms i bunn av ``||basic: gjenta for alltid||`` for å gi koden litt pusterom.

```blocks
basic.forever(function on_forever() {
    SelfStatus = true
    if (pins.digitalReadPin(DigitalPin.P1) == 0) {
        ArmStatus = true
        pins.digitalWritePin(DigitalPin.P13, 1)
    } else {
        ArmStatus = false
        pins.digitalWritePin(DigitalPin.P13, 0)
    } 
    if (pins.digitalReadPin(DigitalPin.P11) == 0) {
        Launch()
    }
    basic.pause(100)
})
function Launch () {
    if (ArmStatus) {
        radio.sendNumber(42)
        pins.digitalWritePin(DigitalPin.P13, 0)
        while (pins.digitalReadPin(DigitalPin.P1) == 0) {
            pins.digitalWritePin(DigitalPin.P8, 1)
            basic.pause(500)
            pins.digitalWritePin(DigitalPin.P8, 0)
            basic.pause(500)
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
let strip = neopixel.create(DigitalPin.P0, 5, NeoPixelMode.RGB)
pins.digitalWritePin(DigitalPin.P15, 1)
let Oppdateringsfrekvens = 200
```

## Del 2.5: 

### Bør: Motta radio-melding fra LaunchPAD

Lag en ny variabel: ``||variables: sistSettAktiv||``. 

Inni en ``||radio:når radio mottar receivedNumber||``: Hver gang vi mottar ``||radio: receivedNumber = 11||``, sett ``||variables: LinkStatus||`` til ``||logic: sann||`` og ``||variables: sistSettAktiv||`` til ``||input: kjøretid (ms)||``. 

I en micro:bit, kjører det en intern klokke som heter ``||input: kjøretid (ms)||``. Ved å sette ``||variables: sistSettAktiv||`` lik ``||input: kjøretid (ms)||`` ved gitte intervaller, kan vi lett se om tidsforskjellen mellom ``||variables: sistSettAktiv||`` og ``||input: kjøretid (ms)||`` blir større enn 3x gitt tidsinterval.

```blocks
radio.onReceivedNumber(function (receivedNumber) {
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

### Bør: Sette opp status på NeoPixelene

Vi skal bruke en ny funksjon, ``||functions: NeoPixels||``, for å vise hvilken status de forskjellige systemene på kofferten har. 

Vi skal inidividuelt sjekke opp variablene: ``||variables: SelfStatus||``, ``||variables: LinkStatus||`` og ``||variables: ArmStatus||``.

Lag 3 ``||logic: Hvis-betingelser||`` med ``||logic: Ellers hvis||``, en for hver variabel. Hvis den er ``||logic: sann||``, skal Neopixel settes til grønn, ellers skal den være rød.

Avslutt med å vise lysene: ``||neopixel: show||``

Kjør ``||functions: NeoPixels||`` fra nederst i ``||basic: gjenta for alltid||``.

| Variabel ||||| NeoPixel nr. |
|:---------|||||:------:|
| SelfStatus ||||| 0 |
| LinkStatus ||||| 1 |
| ArmStatus ||||| 4 |


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
    if (ArmStatus) {
        strip.setPixelColor(4, neopixel.colors(NeoPixelColors.Green))
    } else {
        strip.setPixelColor(4, neopixel.colors(NeoPixelColors.Red))
    }
}
basic.forever(function () {
    SelfStatus = true
    if (pins.digitalReadPin(DigitalPin.P1) == 0) {
        ArmStatus = true
        pins.digitalWritePin(DigitalPin.P13, 1)
    } else {
        ArmStatus = false
        pins.digitalWritePin(DigitalPin.P13, 0)
    }
    if (pins.digitalReadPin(DigitalPin.P11) == 0) {
        Launch()
    }
    NeoPixels()
    basic.pause(100)
})
function Launch () {
    if (ArmStatus) {
        radio.sendNumber(42)
        pins.digitalWritePin(DigitalPin.P13, 0)
        while (pins.digitalReadPin(DigitalPin.P1) == 0) {
            pins.digitalWritePin(DigitalPin.P8, 1)
            basic.pause(500)
            pins.digitalWritePin(DigitalPin.P8, 0)
            basic.pause(500)
        }
    }
}
```

## Del 3: @unplugged

### Bytte Tutorial

Gratulerer! Vi er nå ferdige med å lage Bør-koden på ControllerPAD!

Bytt veiledning, og lage bør-koden til LaunchPAD-kofferten!

![Launch-PAD.jpg](https://i.postimg.cc/Sxhw0Mck/Launch-PAD.jpg)


## Del 3: @unplugged

### Fullføre "Kjekt å ha"-kravene til rakettkoffertene 

Sikkerheten og brukervennligheten til rakettkoffertene er ganske bra nå! Men for å få fullt utbytte av koffertene, skal vi legge til noen få funksjoner til i koden vår.

![Radio-mellom-rakettkoffertene.gif](https://i.postimg.cc/nL4Rtr4R/Radio-mellom-rakettkoffertene.gif)



## Del 3.1:

### Kjekt-å-ha: Motta status på Igniter og Arm fra LaunchPAD

Vi mottar status til ``||variables: IgniterStatusLP||`` og ``||variables: ArmStatusLP||`` fra LaunchPAD i ``||radio: når radio mottar receivedNumber||``: (Lag de nye variablene.)

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

## Del 3.2:

### Oppdatere NeoPixels for ``||variables: IgniterStatusLP||`` og ``||variables: ArmStatusLP||``

Nå som vi kan motta info om status til ``||variables: IgniterStatusLP||`` og ``||variables: ArmStatusLP||`` fra LaunchPAD, skal vi vise disse statusene på NeoPixels-stripen på kofferten.

Inni ``||functions: NeoPixels||``, lag to nye ``||logic: hvis-betingelser||``, en for hver av de to variablene. ``||logic: Hvis||`` variabelen er ``||logic: sann||`` skal Neopixel settes til grønn, ellers skal den være rød.

| Variabel ||||| NeoPixel nr. |
|:---------|||||:------:|
| IgniterStatusLP ||||| 2 |
| ArmStatusLP ||||| 3 |

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
    if (ArmStatus) {
        strip.setPixelColor(4, neopixel.colors(NeoPixelColors.Green))
    } else {
        strip.setPixelColor(4, neopixel.colors(NeoPixelColors.Red))
    }
}
```


## Del 3.3:

### Kjekt-å-ha: Tillatt kun oppskytning hvis alle systemene fungerer

For å sjekke om alle systemene er klare for oppskytning, må vi lage en ``||logic: Hvis-betingelse||`` som sjekker om alle statusene vår er lik ``||logic: sann||``. Den skal settes inn i ``||basic: gjenta for alltid||``.

Vi skal sjekke: ``||variables: SelfStatus||``, ``||variables: LinkStatus||``, ``||variables: IgniterStatusLP||``, ``||variables: ArmStatusLP||`` og ``||variables: ArmStatus||``.

Hvis alle statusene over er lik ``||logic: sann||``, da skal den nye variabelen ``||variables: Klar||`` settes lik ``||logic: sann||``. Ellers settes lik ``||logic: usann||``. Legg ved et bilde på skjermen til micro:biten for hvert at tilfellene som viser om ``||variables: Klar||`` er ``||logic: sann||`` eller ikke.

Vi må også endre i funksjonene ``||functions: Launch||`` og ``||functions: NeoPixels||``:

- I ``||functions: Launch ||`` får raketten kun bli skutt opp hvis ``||variables: Klar||`` er ``||logic: sann||``.
- I ``||functions: NeoPixels||`` skal neopixel nr. 4 endres med status til variabelen ``||variables: Klar||``.

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
}
basic.forever(function () {
    SelfStatus = true
    if (pins.digitalReadPin(DigitalPin.P1) == 0) {
        ArmStatus = true
        pins.digitalWritePin(DigitalPin.P13, 1)
    } else {
        ArmStatus = false
        pins.digitalWritePin(DigitalPin.P13, 0)
    }
    if (pins.digitalReadPin(DigitalPin.P11) == 0) {
        Launch()
    }
    if (SelvStatus && LinkStatus && IgniterStatusLP && ArmStatusLP && ArmStatus) {
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
    basic.pause(100)
})
function Launch () {
    if (Klar) {
        radio.sendNumber(42)
        pins.digitalWritePin(DigitalPin.P13, 0)
        while (pins.digitalReadPin(DigitalPin.P1) == 0) {
            pins.digitalWritePin(DigitalPin.P8, 1)
            basic.pause(500)
            pins.digitalWritePin(DigitalPin.P8, 0)
            basic.pause(500)
        }
    }
}
```

## Del 3.4:

### Forbedre visualisering av rearm

For å tydeligere vise at kofferten er låst i rearm, skal vi skru av neopixel-lysene på kofferten.

Dette gjør vi ved å bruke blokkene ``||neopixel: strip clear||`` og så ``||neopixel: strip show||``. Disse skal plasseres rett etter at raketten er skutt opp og før koden går inn i ``||loops: gjenta hvis sann||``.

Vi skal også få skjermen til microbiten til å blinke med å bruke to ``||basic: vis skjerm ||``, som ser forskjellig ut. Disse skal blinke så lenge ``||pins: les digital P1 = 0 ||``. Fjern også ``||basic: pausene||`` inne i ``||loops: gjenta hvis sann||``.

```blocks
let strip: neopixel.Strip = null
function Launch () {
    if (Klar) {
        radio.sendNumber(42)
        pins.digitalWritePin(DigitalPin.P13, 0)
        strip.clear()
        strip.show()
        while (pins.digitalReadPin(DigitalPin.P1) == 0) {
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
```


## Del 3.5:

### Oppsett og reset av kofferten

Vi skal lage en funksjon for kofferten som heter ``||functions: Initialize||``. 

Funksjonen skal kjøres hver gang vi skrur på LaunchPAD og hver gang vi rearmerer kofferten etter en oppskytning. 

``||functions: Initialize||`` sin oppgave er å nullstille rakettkofferten slik at alle systemer stilles tilbake eller skrus av.

``||functions: Initialize||`` skal kalles opp både fra brunn av ``||basic: ved start||`` og i bunn av ``||functions: Launch||`` (nederst inne i ``||logic: hvis-betingelsen||``).

```blocks
radio.setGroup(1)
radio.setTransmitPower(7)
pins.digitalWritePin(DigitalPin.P15, 1)
let strip = neopixel.create(DigitalPin.P0, 5, NeoPixelMode.RGB)
Initialize()
function Initialize () {
	
}
function Launch () {
    if (Klar) {
        radio.sendNumber(42)
        pins.digitalWritePin(DigitalPin.P13, 0)
        strip.clear()
        strip.show()
        while (pins.digitalReadPin(DigitalPin.P1) == 0) {
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
let strip: neopixel.Strip = null
```

## Del 3.5: 

### Initialize funksjon:

Det første vi må gjøre inne i ``||functions: Initialize||`` er å sette å sette alle variablene til ``||logic: usann||``: ``||variables: SelfStatus||``, ``||variables: LinkStatus||``, ``||variables: IgniterStatusLP||``, ``||variables: ArmStatusLP||``, ``||variables: ArmStatus||`` og ``||variables: Klar||``.

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


## Del 3.6: 

### Initialize - animasjon:

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

## Del 3.7: @unplugged

### Ferdig med Tutorial!

Gratulerer! Vi er nå ferdige med koden til BEGGE koffertene!

``||math: Last ned||`` kodene til begge koffertene og test at de funker som de skal!

![Oppskytning-rakett-4.gif](https://i.postimg.cc/CxV5Qx0C/Oppskytning-rakett-4.gif)




```package
neopixel=github:microsoft/pxt-neopixel#v0.7.3
```

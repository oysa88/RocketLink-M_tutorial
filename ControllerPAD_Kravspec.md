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

### Må: Sjekke om Launch-knappen er trykket ned @diffs true

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

### Må: Launch-kommando kun ved armering @diffs true

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

### Må: Buzzer som varsler om oppskytning @diffs true

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

### Må: Rearmere kofferten før ny oppskytning @diffs true

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



## Del 2.1:

### Bør: Sette opp lysene på kofferten @diffs true

For å vise status på systemene på kofferten, bruker vi NeoPixels. Det skal brukes 5 NeoPixels på ``||pins: digital pin P0||``.

Skriv også at ``||pins: digital pin P15 = 1||``. Dette er lyset på Status Check-knappen på kofferten.

```blocks
radio.setGroup(1)
radio.setTransmitPower(7)
let strip = neopixel.create(DigitalPin.P0, 5, NeoPixelMode.RGB)
pins.digitalWritePin(DigitalPin.P15, 1)
```

## Del 2.2:

### Bør: StatusCheck

For at systemet vårt hele tiden skal sjekke om det skjer noen status-endringer, trenger vi å lage en ny funksjon: ``||functions: StatusCheck||``.

Denne funksjonen skal kalles opp fra ``||basic: gjenta for alltid||``.

Når vi kjører gjennom ``||functions: StatusCheck||``, vet vi at variabelen ``||variables: SelfStatus||`` er ``||logic: sann||``. (Kofferten er jo på...)


```blocks
basic.forever(function () {
    StatusCheck()
})
function StatusCheck () {
    SelfStatus = true
}
```





```package
neopixel=github:microsoft/pxt-neopixel#v0.7.3
```

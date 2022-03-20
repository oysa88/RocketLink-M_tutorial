# LaunchPAD med Kravspec i riktig rekkefølge


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

### Ved start - radio

I blokken ``||basic: ved start||`` skal vi sette opp de funksjonene som kun skal brukes når kofferten skrus på.

Det første vi skal sette opp er en ``||radio: radiogruppe||``. (Vi skal bruke radiogruppe nr. 1 på koffertene.)

Sett også opp at ``||radio: sendereffekt||`` skal være lik 7. (Gir oss sterkere radiosender.)

```blocks
radio.setGroup(1)
radio.setTransmitPower(7)
```

## Del 1.2:

### Sjekke ArmStatus på kofferten

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

### Skyte opp raketten! @diffs true

Raketten skal sendes opp når ``||radio: radio mottar receivedNumber = 42 ||``.

Får å få til dette, må vi lage en ``||logic: hvis-betingelse ||`` inni ``||radio: radio mottar receivedNumber ||``. Her skal vi vi sjekke om ``||variables: receivedNumber||`` = 42.

Hvis den er det, skal vi kalle opp den nye funksjonen ``||functions: Launch ||``.

```blocks
radio.onReceivedNumber(function (receivedNumber) {
    if (receivedNumber == 42) {
        Launch()
    }
})
function Launch () { 
    
}
```

## Del 1.3:

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

### Rearmere kofferten før ny oppskytning @diffs true

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

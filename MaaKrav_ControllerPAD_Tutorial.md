# Må Krav for ControllerPAD - Tutorial


## Del 1: Radiokanal

Sett opp en egen radiokanal (``||radio: radio sett gruppe||``) så koffertene kan snakke sammen. Bruk samme radiokanal som du satte opp på LaunchPAD.

```blocks
radio.setGroup(1)
```

## Del 2: 

Sett opp en ``||logic: Hvis-betingelse||`` som sjekker (``||pins: les digtalverdi||``) om Software Arm Switch (``||pins: P1||``) er 0.

```blocks
basic.forever(function () {
    if (pins.digitalReadPin(DigitalPin.P1) == 0) {
    }
})
```

## Del 3: 

Inni denne blokken setter dere en ny ``||logic: Hvis-betingelse||`` som sjekker (``||pins: les digtalverdi||``) om Launch-button (``||pins: P11||``) er 0.

```blocks
basic.forever(function () {
    if (pins.digitalReadPin(DigitalPin.P1) == 0) {
        if (pins.digitalReadPin(DigitalPin.P11) == 0) {
        }
    }
})
```

## Del 4:

Hvis Launch Button (``||pins: P11||``) er 0 skal du sende en kommando ((``||radio: send tall eller tekst||``)) til LaunchPAD som ikke kan misforstås.

```blocks
basic.forever(function () {
    if (pins.digitalReadPin(DigitalPin.P1) == 0) {
        if (pins.digitalReadPin(DigitalPin.P11) == 0) {
            radio.sendNumber(42)
        }
    }
})
```

## Del 5:

Utvid ``||logic: Hvis-betingelse||`` som sjekker Software Arm Switch (``||pins: P1||``) med en ``||logic: ellers-funksjon||``.

```blocks
basic.forever(function () {
    if (pins.digitalReadPin(DigitalPin.P1) == 0) {
        pins.digitalWritePin(DigitalPin.P13, 1)
        if (pins.digitalReadPin(DigitalPin.P11) == 0) {
            radio.sendNumber(42)
        }
    } else {
    }
})
```

## Del 6: 

Skru på Buzzer ((``||pins: P13 - skriv digital til||``) når Software Arm Switch (``||pins: P1||``) er 0 og av når Software Arm Switch (``||pins: P1||``) er 1. Skru av Buzzer (``||pins: P13||``) etter du har sendt Launch-kommando og legg til en (``||basic: pause||``) på minst 5000 ms etter kommandoen.

```blocks
basic.forever(function () {
    if (pins.digitalReadPin(DigitalPin.P1) == 0) {
        pins.digitalWritePin(DigitalPin.P13, 1)
        if (pins.digitalReadPin(DigitalPin.P11) == 0) {
            radio.sendNumber(42)
            pins.digitalWritePin(DigitalPin.P13, 0)
            basic.pause(5000)
        }
    } else {
        pins.digitalWritePin(DigitalPin.P13, 0)
    }
})
```

## Del 7: 

Last ned koden på en micro:bit og test om den funker på RocketLink-M ControllerPAD!

Lykke til!

PS: Sjekk hint for ferdig kode.

```blocks
radio.setGroup(1)
basic.forever(function () {
    if (pins.digitalReadPin(DigitalPin.P1) == 0) {
        pins.digitalWritePin(DigitalPin.P13, 1)
        if (pins.digitalReadPin(DigitalPin.P11) == 0) {
            radio.sendNumber(42)
            pins.digitalWritePin(DigitalPin.P13, 0)
            basic.pause(5000)
        }
    } else {
        pins.digitalWritePin(DigitalPin.P13, 0)
    }
})
```

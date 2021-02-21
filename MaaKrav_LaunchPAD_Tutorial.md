# MaaKrav_LaunchPAD_Tutorial


## Del 1: Radiokanal

Sett opp en egen radiokanal (``||radio: radio sett gruppe||``) så koffertene kan snakke sammen. Bruk samme radiokanal som du satte opp på ControllerPAD.

```blocks
radio.setGroup(1)
```


## Del 2:

Finn frem blokken ``||radio: når radio mottar recievedNumber||``. Sett opp inni her, en ``||logic: Hvis-betingelse||`` som sjekk om du har mottatt Launch-kommandoen (``||radio: 42||``) fra ControllerPAD. 

```blocks
radio.onReceivedNumber(function (receivedNumber) {
    if (receivedNumber == 42) {
    }
})
```

## Del 3: 

Hvis mottatt Launch-kommando: Sett igniter (``||pins: P16||``) til 1 i 500 ms.

```blocks
radio.onReceivedNumber(function (receivedNumber) {
    if (receivedNumber == 42) {
        pins.digitalWritePin(DigitalPin.P16, 1)
        basic.pause(500)
        pins.digitalWritePin(DigitalPin.P16, 0)
    }
})
```

## Del 4:

Last ned koden på en micro:bit og test om den funker på RocketLink-M LaunchPaAD!

Lykke til!

PS: Sjekk hint for ferdig kode.

```blocks
radio.setGroup(1)
radio.onReceivedNumber(function (receivedNumber) {
    if (receivedNumber == 42) {
        pins.digitalWritePin(DigitalPin.P16, 1)
        basic.pause(500)
        pins.digitalWritePin(DigitalPin.P16, 0)
    }
})
```
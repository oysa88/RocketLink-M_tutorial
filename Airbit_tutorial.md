# Air:bit - Veiledning

## Steg 1

### Throttle

Lag en variabel som heter ``||variabel: Throttle||``.

Vi skal bruke ``||input: knapp A||`` og ``||input: knapp B||`` til å endre ``||variabel: Throttle||``. 

- For ``||input: knapp A||``: Hvis ``||variabel: Throttle||`` er under eller lik 40, skal ``||variabel: Throttle||`` endres med -5. Ellers skal den endres med -1.

- For ``||input: knapp B||``: Hvis ``||variabel: Throttle||`` er under eller lik 40, skal ``||variabel: Throttle||`` endres med +5. Ellers skal den endres med +1.


```blocks
let Throttle = 0
input.onButtonPressed(Button.A, function () {
    if (Throttle <= 40) {
        Throttle += -5
    } else {
        Throttle += -1
    }
})
input.onButtonPressed(Button.B, function () {
    if (Throttle <= 40) {
        Throttle += 5
    } else {
        Throttle += 1
    }
})
```

## Steg 2

### Roll og Pitch

Lag to variabler: ``||variabel: Roll||`` og ``||variabel: Pitch||``.

Inni ``||basic: gjenta for alltid||``:

- Sett ``||variabel: Roll||`` til å lese av verdien fra ``||input: helningsvinkel: vinkel høyre/venstre||``

- Sett ``||variabel: Pitch||`` til å lese av verdien fra ``||input: helningsvinkel: tonehøyde||``

```blocks
basic.forever(function () {
    Roll = input.rotation(Rotation.Roll)
    Pitch = input.rotation(Rotation.Pitch)
})
```

## Steg 3



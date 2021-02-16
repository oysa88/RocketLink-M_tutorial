!### @flyoutOnly
!### @explicitHints

# Spille på flaskene fra mørkeste til lyseste tone
Du skal forsøke å spille på flaskene fra den mørkeste til den lyseste tonen. Følg instruksjonen under!

## Steg 1

Sett opp egen ``||radio:Radio sett gruppe||``. Den skal være den samme som posten du er på.

```blocks
radio.setGroup(4)
```

## Steg 2

Nå skal vi spille på flaskene i riktig rekkefølge. Bruk blokken``||radio: Send verdi||`` der radio: name = spill. 

Send én blokk per flaske, og sett verdien til å være nummeret til flasken du vil spille på. 

Sett blokkene inni en ``||input: Når knapp A trykkes||``.

```blocks
input.onButtonPressed(Button.A, function () {
    radio.sendValue("spill", )
    radio.sendValue("spill", )
    radio.sendValue("spill", )
    radio.sendValue("spill", )
})
```


## Step 3

For å kunne motta bokstaven når oppgaven er løst, må se sette opp at vi skal, når vi ``||radio: når radio mottar||`` ``||variable: receivedString||``, skal ``||basic: vis tekst||`` ``||variable: receivedString||``.

Trekk ``||variable: receivedString||`` ut fra ``||radio: når radio mottar||`` og sett den inn i ``||basic: vis tekst||``.

```blocks
radio.onReceivedString(function (receivedString) {
    basic.showString(receivedString)
})
```

## Steg 4

Last ned koden til din micro:bit og send den avgårde ved å trykke på knapp A på din micro:bit. 

Lykke til!
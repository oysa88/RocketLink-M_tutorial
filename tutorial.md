### @flyoutOnly
### @explicitHints

# Spille på flaskene frla lysest til mørkest tone


## Steg 1

Sett opp egen ``[radio.setGroup()]``. Den skal være den samme som posten du er på.

```blocks
radio.setGroup(4)```

## Steg 2

Nå skal vi spille på flaskene i riktig rekkefølge. Bruk blokken``[radio.sendValue("name", 0)]`` der name = spill. 

Send en blokk per flaske og sett verdien til å være den flasken du vil spille på. 

Sett blokkene inni en ``[input.onButtonPressed(Button.A, function ()]``

```blocks
input.onButtonPressed(Button.A, function () {
    radio.sendValue("spill", )
    radio.sendValue("spill", )
    radio.sendValue("spill", )
    radio.sendValue("spill", )
    ```

## Steg 3

Last ned koden til din microbit og send den avgårde ved å trykke på A. Lykke til!
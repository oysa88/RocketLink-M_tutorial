
> Åpne denne siden på [https://github.com/oysa88/RocketLink-M_tutorial/](https://github.com/oysa88/RocketLink-M_tutorial/)

## Use as Extension

This repository can be added as an **extension** in MakeCode.

* åpne [https://makecode.microbit.org/](https://makecode.microbit.org/)
* klikk på **Nytt prosjekt**
* klikk på **Utvidelser** i menyen under tannhjulet
* søk etter **https://github.com/oysa88/RocketLink-M_tutorial** og importér

## Rediger dette prosjektet ![Bygg statusmerke](https://github.com/oysa88/RocketLink-M_tutorial/workflows/MakeCode/badge.svg)

To edit this repository in MakeCode.

* åpne [https://makecode.microbit.org/](https://makecode.microbit.org/)
* klikk på **Importer** og så på **Importér URL**
* lim inn **https://github.com/oysa88/RocketLink-M_tutorial** og klikk på importér

## Forhåndsvisning av blokker

This image shows the blocks code from the last commit in master.
This image may take a few minutes to refresh.

![En opptegnet visning av blokkene](https://github.com/oysa88/RocketLink-M_tutorial/raw/master/.github/makecode/blocks.png)

#### Metadata (brukes for søk, visualisering)

* for PXT/microbit
<script src="https://makecode.com/gh-pages-embed.js"></script><script>makeCodeRender("{{ site.makecode.home_url }}", "{{ site.github.owner_name }}/{{ site.github.repository_name }}");</script>


### @flyoutOnly
### @explicitHints

# Spille på flaskene fra mørkeste til lyseste tone
Du skal forsøke å spille på flaskene fra den mørkeste til den lyseste tonen. Følg instruksjonen under!

## Steg 1

Sett opp egen ``[radio.setGroup()]``. Den skal være den samme som posten du er på.

```blocks
radio.setGroup(4)```

## Steg 2

Nå skal vi spille på flaskene i riktig rekkefølge. Bruk blokken``[radio.sendValue("name", 0)]`` der name = spill. 

Send én blokk per flaske, og sett verdien til å være nummeret til flasken du vil spille på. 

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

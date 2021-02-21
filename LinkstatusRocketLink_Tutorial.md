!### @flyoutOnly
!### @explicitHints

# Sjekke Linkstatus til rakettkoffertene

For å kunne sjekke link mellom koffertene må bruke noe som kalles for «active sensing». 
Begge koffertene sender en radiomelding mellom seg med gitte intervaller som sier «Hei, jeg er ikke avskrudd». 


## Del 1: Sjekke at man mottar signal fra den andre kofferten

Lag to variabler: ``||variable: LinkStatus||`` og ``||variable: sistSettAktiv||``. 

Inni en ``||radio:når radio mottar receivedNumber||``: Sett ``||variable: LinkStatus||`` til ``||logic: sann||`` og sett ``||variable: sistSettAktiv||`` til ``||input: kjøretid (ms)||``.

```blocks
radio.onReceivedNumber(function (receivedNumber) {
    LinkStatus = true
    sistSettAktiv = input.runningTime()
})
```

## Del 2: Lage en oppdateringsfrekvens

Lag en variabel som du kaller ``||variable: oppdateringsfrekvens||``. Sett den inn under  ``||basic: ved start||``

```block
let oppdateringsfrekvens = 200
```

## Del 3: Sjekke om man slutter å motta signal fra den andre kofferten 

For å finne ut om kofferten har sluttet å motta signal fra den andre kofferten, må vi lage en liten funksjon som vi setter inn i  ``||controll: run in Background||``. (Finner du i menuen: Kontroll)

Inni her skal lage en  ``||loops: while-løkke||``. Denne løkken vil kjøre så lenge kofferten er på.

Send tallet 11 med radio. 

Nå skal vi sjekke om den ene av koffertene ikke mottar et signal på 3x ``||variabel: oppdateringsfrekvens||``. Hvis den er lengre enn det, kan den regne med at den andre kofferten er skrudd av.

Vi skal sjekke om ``||input: kjøretid (ms)||`` minus (-) ``||variabel: sistSettAktiv||`` er større enn (>) 3x ``||variabel: oppdateringsfrekvens||``

Hvis dette er sant, skal ``||variabel: LinkStatus||`` settes til ``||logic: usann||``

Avslutt med en ``||basic: pause||`` lik ``||variabel: oppdateringsfrekvens||``.


```blocks
control.inBackground(function () {
    while (true) {
        radio.sendNumber(11)
        if (input.runningTime() - sistSettAktiv > 3 * oppdateringsfrekvens) {
            LinkStatus = false
        }
    }
    basic.pause(oppdateringsfrekvens)
})
```
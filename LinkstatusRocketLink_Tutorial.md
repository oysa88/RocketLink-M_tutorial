# Sjekke Linkstatus til rakettkoffertene

For å kunne sjekke link mellom koffertene må bruke noe som kalles for «active sensing». 
Begge koffertene sender en radiomelding mellom seg med gitte intervaller som sier «Hei, jeg er ikke avskrudd». 


## Del 1: Sjekke at man mottar signal fra den andre kofferten

Lag to variabler: ``||variable: LinkCheck||`` og ``||variable: lastSeenAlive||``. Inni en ``||radio:når radio mottar receivedNumber||``, sett at ``||variable: LinkCheck||`` er ``||logic: sann||`` og at ``||variable: lastSeenAlive||`` er ``||input: kjøretid (ms)||``.

```blocks
radio.onReceivedNumber(function (receivedNumber) {
    LinkCheck = true
    lastSeenAlive = input.runningTime()
})
```

## Del 2: Lage en oppdateringsfrekvens

Lag en variabel som du kaller ``||variable: updateFrequency||``. Sett den inn under  ``||basic: ved start||``

```block
let updateFrequency = 200
```

## Del 3: Sjekke om man slutter å motta signal fra den andre kofferten 

For å finne ut om kofferten har sluttet å motta signal fra den andre kofferten, må vi lage en liten funksjon som vi setter inn i  ``||controll: run in Background||``. (Finner du i menuen: Kontroll)

Inni her skal lage en  ``||loops: while-løkke||``. Denne løkken vil kjøre så lenge kofferten er på.

Send tallet 11 med radio. 

Nå skal vi sjekke om den ene av koffertene ikke mottar et signal på 3x ``||variabel: updateFrequency||``. Hvis den er lengre enn det, kan den regne med at den andre kofferten er skrudd av.

Vi skal sjekke om ``||input: kjøretid (ms)||`` minus (-) ``||variabel: lastSeenAlive||`` er større enn (>) 3x ``||variabel: updateFrequency||``

Hvis dette er sant, skal ``||variabel: LinkCheck||`` settes til ``||logic: usann||``

Avslutt med å med en ``||basic: pause||`` lik ``||variabel: updateFrequency||``.


```blocks
control.inBackground(function () {
    while (true) {
        radio.sendNumber(11)
        if (input.runningTime() - lastSeenAlive > 3 * updateFrequency) {
            LinkCheck = false
        }
    }
    basic.pause(updateFrequency)
})
```

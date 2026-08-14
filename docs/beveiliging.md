# Beveiligingsmodel

Dit patroon bestuurt echte apparaten in huis. De beveiliging is daarom bewust
gelaagd, met als belangrijkste principe: **niets van buiten je netwerk kan naar
Home Assistant schrijven.**

## De kern: het huis pullt, de cloud pusht niet

De apply gebeurt op de HA-hardware zelf. Een add-on daar pullt de gewenste staat
uit de (privé) Git-repo en past die lokaal toe. Er is dus geen server in de cloud
die het huis kan bereiken, en geen HA-account met schrijf- of adminrechten dat
buiten het thuisnetwerk bekend hoeft te zijn.

De add-on praat met Home Assistant via de **Supervisor-proxy**, waardoor hij
**helemaal geen HA-token** nodig heeft en "Protection mode" aan kan blijven.

De keerzijde hiervan — waarom we bewust géén cloud-runner gebruiken — staat
uitgewerkt op de [Apply-stap](runner-setup.md)-pagina.

## Wie kan wat

| Component | Sleutel | Kan schrijven naar HA? |
|-----------|---------|------------------------|
| Auteur / agent (voorstellen) | geen | Nee |
| GitHub (opslag + review) | je GitHub-account | Nee (bewaart alleen de gewenste staat) |
| apply-add-on (bij jou thuis) | alleen-lezen GitHub-token + Supervisor-proxy | Ja, lokaal, alleen na jouw knopdruk |
| Jij | je GitHub-account + de knop | Beslist wat en wanneer |

De enige sleutel buiten je netwerk is een **alleen-lezen GitHub-token** op de
HA-hardware, met toegang tot alleen die ene privé-repo.

## Branch protection

Het uitgangspunt is dat elke wijziging via een pull request loopt, met een
reviewbare diff. Waar het gekozen GitHub-plan het toelaat, wordt dat afgedwongen
met "require pull request + 1 approval" op `main`, zodat rechtstreeks pushen niet
kan. Los daarvan geldt: omdat de apply pas na een **handmatige knopdruk** gebeurt,
komt er nooit iets ongezien op Home Assistant terecht.

## Rollback

Elke wijziging is een Git-commit. Terugdraaien is: revert de commit, merge, en
druk opnieuw op de knop. De add-on maakt bovendien vóór elke apply een snapshot.

## Publieke vs. privé documentatie

Deze site is publiek en bevat bewust **geen** instance-specifieke gegevens (geen
URL's, geen entiteit-namen, geen apparaatlijsten). Die staan in een privé-repo en
zijn alleen voor de eigenaar zichtbaar. Links naar die privé-referentie geven
voor anderen een 404.

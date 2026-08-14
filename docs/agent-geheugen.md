# Agent-geheugen

Deze pagina beschrijft hoe het repo-gebaseerde geheugen van de Home Assistant Config Agent werkt, en waarom daarvoor een markdown-bestand wordt gebruikt in plaats van de native managed memory-functie van Azure.

## Waarom repo-gebaseerd geheugen?

In deze omgeving is Azure managed Memory niet beschikbaar. Er is dus geen externe geheugenservice die voorkeuren en langetermijninformatie voor de agent kan bewaren.

Het gekozen alternatief is geheugen via versiebeheer:

- Geheugen wordt vastgelegd in een bestand in een Git-repository.
- Elke wijziging loopt via een branch en Pull Request.
- Het geheugen is daarmee traceerbaar, reproduceerbaar en onder versiebeheer.

Zo blijft duidelijk welke voorkeuren wanneer zijn toegevoegd, en kan elke wijziging expliciet worden beoordeeld voordat die effect krijgt.

## Structuur van het geheugen

De agent heeft twee typen broninformatie:

1. **Vaste instructies**: systeem- en developerinstructies, die het algemene gedrag van de agent bepalen.
2. **Repo-gebaseerd geheugen**: een markdown-bestand in de private configuratierepo dat duurzame voorkeuren vastlegt.

De tweede bron is specifiek bedoeld voor langetermijnvoorkeuren die tijdens gebruik ontstaan, zoals communicatiestijl, documentatie-afspraken of Home Assistant-conventies.

## Het geheugenbestand

In de private repo `home-assistant-config` staat het bestand:

- `agent/voorkeuren.md`

Dit bestand fungeert als langetermijngeheugen:

- De agent leest het aan het begin van elke taak.
- Het bevat beknopte, geordende voorkeuren (communicatie, documentatie, stijl, enzovoort).
- De inhoud wordt gebruikt als aanvullende instructie bovenop de vaste systeem- en developerregels.

## Wijzigen van voorkeuren

Wanneer tijdens gebruik een **duurzame voorkeur** wordt genoemd (bedoeld om in toekomstige taken te blijven gelden), gaat dit als volgt:

- De agent bepaalt of het om een langetermijnvoorkeur gaat.
- Bij twijfel vraagt de agent kort om verduidelijking.
- Als het een duidelijke langetermijnvoorkeur is, stelt de agent een wijziging aan `agent/voorkeuren.md` voor.

Die wijziging verloopt via de configuratierepo:

- Er wordt een branch in `home-assistant-config` gebruikt.
- De commit past alleen het geheugenbestand (of een beperkt aantal gerelateerde regels) aan.
- Er wordt een Pull Request geopend met een korte Nederlandse titel en uitleg.

Pas na het mergen van de Pull Request gelden de nieuwe voorkeuren. Omdat de agent het bestand bij elke taak inleest, worden aangepaste voorkeuren automatisch meegenomen.

## Afbakening en privacy

Het repo-gebaseerde geheugen houdt rekening met de scheiding tussen publieke en private documentatie:

- Geheugen (voorkeuren en instance-specifieke details) staat in de private repo `home-assistant-config`.
- Persoonlijke of gevoelige gegevens komen niet terecht in de publieke docs-repo.
- Deze pagina beschrijft het mechanisme generiek, zonder het geheugenbestand zelf of concrete voorkeuren weer te geven.

## Samenvatting

- Azure managed Memory is in deze omgeving niet beschikbaar, daarom wordt geheugen repo-gebaseerd ingericht.
- Het geheugen leeft in een markdown-bestand in de private configuratierepo: `agent/voorkeuren.md`.
- De agent leest dit bestand bij elke taak in en gebruikt het als aanvullende bron van voorkeuren.
- Wijzigingen aan het geheugenbestand lopen altijd via branch en Pull Request, zodat alles onder versiebeheer blijft en expliciet kan worden beoordeeld.
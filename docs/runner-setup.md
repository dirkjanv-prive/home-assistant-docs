# Apply-stap: hoe de config in Home Assistant terechtkomt

De "apply" is de stap die de goedgekeurde configuratie uit Git daadwerkelijk op
Home Assistant toepast. Die stap draait bewust **op de HA-hardware zelf**, binnen
het thuisnetwerk. Deze pagina beschrijft hoe dat werkt en waarom er geen
cloud-runner aan te pas komt.

## Hoe het werkt

Een Home Assistant-add-on draait op het HA-apparaat en luistert op een knop. Bij
een druk op **Config toepassen** doorloopt de add-on deze stappen:

1. **Pullt** de laatste `main` uit de privé-config-repo, met een **alleen-lezen**
   GitHub-token.
2. **Snapshot**: bewaart eerst de huidige config lokaal, zodat terugdraaien altijd
   kan.
3. **Past toe**: schrijft alleen de gewijzigde dashboards en automations naar Home
   Assistant, via de **Supervisor-proxy** (dus zonder apart HA-token).
4. **Verifieert**: leest terug en controleert dat de live staat gelijk is aan
   `desired/`. Het resultaat verschijnt in een statusveld op het dashboard.

Wat dit betekent:

- De apply gebeurt **binnen het thuisnetwerk**; er is geen dienst buiten het huis
  die Home Assistant kan bereiken.
- Er is **geen HA-account met schrijf- of adminrechten** nodig buiten het netwerk.
- **Jij bepaalt het moment** (de knopdruk); er wordt niet constant gepolld.

De benodigde onderdelen in HA zijn een knop (`input_button`) en een tekstveld
(`input_text`) voor de status, samen op een dashboard.

## Waarom geen cloud-runners

Een voor de hand liggend alternatief is de apply in een GitHub Actions-workflow op
een cloud-runner draaien. Dat doen we bewust **niet**, om deze redenen:

- **Het doorbreekt het uitgangspunt.** Een cloud-runner zou Home Assistant vanaf
  buiten moeten kunnen bereiken én een schrijf-token nodig hebben. Dat is precies
  de externe schrijftoegang tot het huis die deze opzet wil vermijden.
- **Het vergt een bevoorrechte sleutel buiten het huis.** Een schrijf-token (en
  meestal een admin-account) zou dan in de cloud bewaard worden, in plaats van dat
  er alleen een alleen-lezen token binnen het thuisnetwerk staat.
- **Hosted runners zijn vaak niet beschikbaar.** In organisatie- of
  enterprise-omgevingen staan door GitHub gehoste runners regelmatig uit.
- **Een self-hosted runner is feitelijk hetzelfde als de add-on.** Wil je toch een
  runner, dan zet je die in je eigen netwerk — en dan heb je in de praktijk de
  add-on-aanpak van hierboven, maar dan omslachtiger.

Kortom: de apply hoort thuis op de HA-hardware. Een cloud-runner zou de
beveiligingswinst tenietdoen zonder iets wezenlijks toe te voegen.

## Export en token-rechten

Naast *apply* (Git → HA) kan de add-on ook *exporteren* (HA → Git): de live
HA-staat terugschrijven naar `desired/`, rechtstreeks naar `main` of naar een
branch met een Pull Request. Zie [Werkwijze](werkwijze.md).

Dit heeft gevolgen voor het GitHub-token op de HA-hardware:

- Alleen *apply* gebruiken? Dan volstaat een **alleen-lezen** token.
- Ook *exporteren*? Dan is een **read-write** token nodig (Contents: read/write,
  en Pull requests: read/write voor de PR-variant), gescoped op alleen deze repo.

Belangrijk: dit blijft een **GitHub**-token. Er komt geen schrijftoegang tot Home
Assistant van buiten je netwerk bij; export leest HA en schrijft naar GitHub.

## Lokale terugval

Naast de knop kan de apply ook handmatig lokaal draaien vanaf een PC met toegang
tot Home Assistant. Dat is handig bij het opzetten of debuggen. Zie
[Werkwijze](werkwijze.md) voor de commando's.

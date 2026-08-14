# De apply-stap draaien

De apply (het daadwerkelijk toepassen van `desired/` op Home Assistant) moet
ergens draaien. Omdat het beveiligingsuitgangspunt is dat **niets van buiten je
netwerk** naar HA mag schrijven, draait de apply het liefst op je eigen
HA-hardware.

## Aanbevolen: knop-add-on op de HA-hardware

Een Home Assistant-add-on draait op je HA-apparaat, luistert op een knop, en past
bij een druk de nieuwste config toe. Praat met HA via de Supervisor-proxy (geen
token nodig). De enige secret is een alleen-lezen GitHub-token.

Voordelen:

- Geen externe schrijftoegang tot HA.
- Geen HA-account met admin/schrijfrechten nodig.
- Jij bepaalt wanneer (knopdruk), geen constant pollen.

Benodigde helpers in HA:

- Een **knop** (`input_button`), bijv. "Config toepassen".
- Een **tekstveld** (`input_text`) voor de status.

Zet die met een knop-kaart en een entiteit-kaart op een dashboard.

## Alternatief: cloud-runner (afgeraden voor privé-HA)

Je kunt de apply ook in een GitHub Actions-workflow draaien op een runner. Dat
werkt technisch, maar vereist dat de runner je HA kan bereiken en een
schrijf-token heeft. Voor een privé-HA betekent dat schrijftoegang vanuit de
cloud, wat het beveiligingsvoordeel van het knop-model tenietdoet. Alleen
zinvol als je HA toch al bewust vanuit de cloud beheerd wordt.

Let op bij runners:

- **Hosted runners** kunnen door een organisatie/enterprise zijn uitgeschakeld.
- **Self-hosted runners** zijn gratis, maar geven de host toegang; die zet je dan
  liefst in je eigen netwerk (wat feitelijk neerkomt op de add-on hierboven).

## Lokale terugval

Zonder add-on of runner kun je de apply altijd handmatig lokaal draaien vanaf een
PC met toegang tot HA. Zie [werkwijze](werkwijze.md).

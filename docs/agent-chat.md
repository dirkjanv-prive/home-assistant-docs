# De agent en de chat-frontend

Naast handmatig bewerken kun je wijzigingen ook laten voorstellen door een AI-agent,
en met die agent praten via een eigen chat-webapp, ook vanaf je telefoon. De agent
verandert nooit rechtstreeks iets aan je huis: hij stelt wijzigingen voor als pull
requests, precies zoals je dat zelf zou doen. Jij reviewt, merget en drukt op
**Config toepassen**. Zo blijft dezelfde regel gelden: niets van buiten je netwerk
krijgt schrijftoegang tot Home Assistant.

## Wat de agent doet

- **Leest** je repo (dashboards, automations, de instance-referentie) om te weten
  hoe jouw installatie in elkaar zit.
- **Leest** desgewenst live sensorwaarden uit en kan apparaten aansturen.
- **Stelt configuratiewijzigingen voor als pull request**: een nieuwe branch, de
  aangepaste bestanden, en een uitleg. De agent pusht nooit naar `main` en drukt
  nooit zelf op de apply-knop.

De harde afspraak "alles via een PR" geldt dus ook voor de agent. Zie
[beveiliging](beveiliging.md) voor waarom dit veilig blijft.

## De chat-frontend

De playground van het AI-platform werkt, maar is niet fraai en niet aanpasbaar.
Daarom is er een eigen, gehoste chat-webapp met een rustige, donkere interface die
prettig werkt op desktop én mobiel.

### Twee vlakken: schone chat, apart activiteitenpaneel

Een vraag aan de agent kan tientallen seconden tot ruim een minuut duren, omdat hij
onderweg bestanden leest, je huis uitleest en een pull request voorbereidt. In
plaats van al die tijd naar een animatie te staren, splitst de interface het beeld:

- **De hoofdchat blijft schoon.** Je ziet alleen je vraag en het uiteindelijke
  antwoord. Het antwoord verschijnt bovendien direct woord-voor-woord (streaming),
  zodat je niet op het hele resultaat hoeft te wachten.
- **Het activiteitenpaneel toont live de stappen.** Ernaast (op mobiel: in een
  uitschuifbare lade) volg je op hoofdlijnen waar de agent mee bezig is, met een
  draaiend icoon op de lopende stap en een vinkje op afgeronde stappen. Na afloop
  klapt elke vraag samen tot een korte samenvatting ("Klaar · N stappen · Ns").

### Wat je in het paneel ziet

De ruwe, technische details blijven verborgen; je ziet leesbare stappen op
hoofdniveau, bijvoorbeeld:

- *Aan het nadenken* — de agent redeneert over je vraag.
- *GitHub: bestand lezen* / *bestanden pushen* / *pull request maken* — het werk in
  de repo.
- *HA: entiteit uitlezen* / *service aansturen* — live contact met je huis.
- *Antwoord schrijven* — het eindantwoord dat in de hoofdchat verschijnt.

Zo blijft het hoofdscherm overzichtelijk, terwijl je toch kunt meekijken en
begrijpen waarom een antwoord even duurt.

## Toegang en veiligheid

- **Afgeschermd met inlog.** De webapp staat achter authenticatie; alleen
  geautoriseerde accounts kunnen hem openen.
- **Geen sleutels of tokens in de app.** De verbinding met de agent loopt via een
  beheerde identiteit (managed identity); er staan geen geheimen in de code of in de
  browser.
- **Alleen voorstellen, nooit direct wijzigen.** Ook via de chat verloopt elke
  configuratiewijziging als pull request die jij goedkeurt en toepast.

## Hoe het technisch werkt (op hoofdlijnen)

De webapp is een kleine backend die je bericht doorgeeft aan de agent en het
antwoord terugstreamt. Tijdens het streamen vertaalt de backend de gebeurtenissen
van de agent (redeneren, tool-aanroepen, antwoordtekst) naar de leesbare stappen en
tekstfragmenten die je in het activiteitenpaneel en de hoofdchat ziet. De concrete
resourcenamen, adressen en identiteiten staan in de privé-referentie, niet hier.

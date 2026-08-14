# Beveiliging

## Uitgangspunten

De kern van het ontwerp:

- **Geen cloud-schrijftoegang tot je huis**: alleen de apply-add-on op de HA-hardware schrijft naar Home Assistant.
- **Git is de bron van waarheid**: alle configuratiewijzigingen gaan via pull requests in een privérepo.
- **Mens-in-de-loop**: je keurt elke wijziging goed en drukt zelf op Config toepassen.

Deze uitgangspunten blijven ook gelden als er nieuwe front-ends bijkomen (zoals een eigen webchat-app voor de agent).

## Grenzen tussen onderdelen

De architectuur houdt strikt onderscheid tussen:

- **Home Assistant**: draait thuis; alleen de apply-add-on schrijft configuratie.
- **Configrepo** (privé GitHub): opslag van gewenste dashboards/automations; alleen Git-clients schrijven hier.
- **Agents**: lezen en schrijven uitsluitend via de configrepo en bedienen Home Assistant alleen via de standaard HA-API.
- **Front-ends** (Foundry-playground, webchat-app): UI-laag voor mensagent-interactie.

Een nieuwe front-end verandert niets aan wie waar mag schrijven; hij biedt alleen een ander UI-kanaal naar dezelfde agent.

## Webchat-app voor de agent

Wanneer je een eigen webchat-app bovenop de agent zet, gelden extra aandachtspunten:

- **Authenticatie**: bescherm de webapp met een identitylaag (bijvoorbeeld Entra ID) en beperk toegang tot bekende gebruikers.
- **Geheimen**: laat de backend van de webapp de agent aanroepen via een veilige mechaniek zonder opgeslagen API-sleutels, bijvoorbeeld met een platform-identiteit in plaats van een hardcoded key.
- **Scope**: zorg dat de webapp alleen bij de agent kan, niet rechtstreeks bij Home Assistant of de apply-add-on.

Zo blijft de beveiligingsgrens uit de basisarchitectuur intact: alleen de apply-add-on raakt Home Assistant-configuratie, en alleen na merge en een handmatige knopdruk.

## Config-pipeline en knoppen

De config-pipeline wordt bestuurd vanuit Home Assistant zelf:

- **Config toepassen**: triggert de apply-add-on om `desired/` uit `main` toe te passen.
- **Export naar main/branch**: legt drift (wijzigingen direct in HA) vast in de repo, met of zonder PR.

Front-ends en agents mogen deze pipeline niet omzeilen:

- Front-ends sturen alleen opdrachten naar de agent.
- De agent mag nooit deze knoppen in HA zelf bedienen; dat blijft een menselijke handeling.

Zo blijft er een duidelijke scheiding tussen interactie (chat, UI) en configuratie-toepassing (add-on op de HA-hardware).

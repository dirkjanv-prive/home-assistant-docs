# Private webchat-app voor de agent

Deze omgeving voegt een extra front-end toe naast de Foundry-playground: een eigen webchat-app waarmee je de Home Assistant-agent kunt gebruiken op mobiel en desktop.

## Doel en plaats in de architectuur

De webchat-app is een losse client bovenop dezelfde agent en GitOps-architectuur:

- Je voert gesprekken met de agent via een browser.
- De agent doet precies hetzelfde als altijd: PRs voorstellen voor config, live sensoren uitlezen, apparaten bedienen op verzoek.
- De enige plek waar configuratie verandert, blijft de GitHub-configrepo; Home Assistant zelf wordt pas bijgewerkt na een merge en een knopdruk in HA.

Functioneel verandert er dus niets aan het pad wijziging  PR  merge  Config toepassen, alleen de gebruikersinterface naar de agent is anders.

## Hosting en beveiliging

De webchat-app is opgezet als een kleine webtoepassing:

- Draait als een web-app met een backend die verzoeken naar de agent doorzet.
- Maakt voor toegang tot de agent gebruik van een veilige mechaniek zonder lokaal opgeslagen API-sleutels (bijvoorbeeld via een platform-specifieke identiteit in plaats van een hardcoded token).
- Toegang tot de webapplicatie zelf is achter een identitylaag gezet; alleen vooraf toegestane gebruikers kunnen inloggen.

Zo blijft het aanvalsoppervlak beperkt: de webapp is een extra deur naar de agent, maar niet naar Home Assistant zelf of naar de config-pipeline.

## Interactie met Home Assistant

Belangrijk voor het beveiligings- en werkingsmodel:

- De agent kan vanuit de webchat, net als vanuit andere front-ends, live states lezen (bijvoorbeeld temperatuur, stroomverbruik) en apparatuur bedienen via de Home Assistant-API.
- Voor configuratie blijft GitHub de bron van waarheid: dashboards, automations en scripts worden alleen aangepast via pull requests op de configrepo.
- Apply naar Home Assistant gebeurt uitsluitend via de bestaande pipeline en de Config toepassen-knop in HA.

De webchat-app voegt dus geen nieuwe configuratiepaden toe, maar een nieuwe, gebruiksvriendelijke manier om met de agent te praten.

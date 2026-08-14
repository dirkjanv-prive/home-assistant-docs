# Agent-geheugen en voorkeuren

Deze documentatie beschrijft hoe de Home Assistant Config Agent omgaat met geheugen en voorkeuren, specifiek in deze omgeving waar de managed Memory-functie van Azure niet beschikbaar is.

## Overzicht

De agent heeft twee bronnen van "geheugen":

1. **Instructies in de systeem- en developerlaag**: Dit zijn vaste regels die het gedrag van de agent bepalen.
2. **Repo-gebaseerde personalisatie (Optie B)**: Een langetermijngeheugenbestand in de private configuratierepo, dat via Pull Requests wordt bijgewerkt.

De tweede optie is toegevoegd om duurzame voorkeuren van Dirk-Jan vast te leggen zonder gebruik te maken van een externe managed memory store.

## Repo-gebaseerde personalisatie (Optie B)

### Geheugenbestand

In de private repo `home-assistant-config` staat het bestand:

- `agent/voorkeuren.md`

Dit bestand functioneert als langetermijngeheugen voor de `ha-config-agent`:

- De agent leest dit bestand aan het begin van elke taak.
- Het bevat beknopte, geordende voorkeuren (communicatie, documentatie, stijl, enzovoort).
- De inhoud wordt als aanvullende instructie gebruikt naast de vaste systeem- en developerregels.

### Bijwerken van voorkeuren

Wanneer Dirk-Jan tijdens het gebruik een **duurzame voorkeur** uitspreekt (een regel die voor toekomstige taken moet blijven gelden):

- De agent interpreteert of dit een langetermijnvoorkeur is.
- Bij twijfel vraagt de agent kort om bevestiging.
- Vervolgens stelt de agent een wijziging aan `agent/voorkeuren.md` voor:
  - via een aparte branch in `home-assistant-config`,
  - met een commit die alleen dit bestand aanpast,
  - en een Pull Request met een korte Nederlandse titel en uitleg.

Na het mergen van de PR wordt de nieuwe voorkeur automatisch meegenomen bij volgende taken, omdat de agent het bestand telkens opnieuw inleest.

### Afbakening en privacy

- Repo-gebaseerde personalisatie staat **alleen** in de private repo `home-assistant-config`.
- Er worden geen persoonlijke voorkeuren of gevoelige details in de publieke docs-repo opgeslagen.
- De publieke documentatie (deze pagina) beschrijft het mechanisme generiek, zonder concrete voorkeuren of gevoelige data.

## Relatie met andere documentatie

- De algemene documentatieregels voor gevoelige gegevens blijven gelden: gevoelige of instance-specifieke details gaan naar `home-assistant-config/docs/referentie.md`.
- Dit geheugenmechanisme sluit aan op die aanpak: het is bewust repo-gebaseerd en volledig onder versiebeheer.

## Samenvatting

- Omdat Azure managed Memory niet beschikbaar is in deze regio gebruikt de agent een **repo-gebaseerd geheugenbestand** (`agent/voorkeuren.md`).
- De agent leest dit bestand bij elke taak in.
- Duurzame voorkeuren worden via een branch en Pull Request aan dit bestand toegevoegd of aangepast.
- Zo blijft het gedrag van de agent voorspelbaar, reproduceerbaar en onder versiebeheer, zonder externe geheugenservice.
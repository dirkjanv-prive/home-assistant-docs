# Werkwijze — dagelijks gebruik

Elke wijziging aan Home Assistant loopt via Git: je stelt hem voor in een pull
request, keurt hem goed (met een leesbare diff, ook op je telefoon), en past hem
daarna met een knop toe. Zo blijft alles zichtbaar, reviewbaar en terug te
draaien. Waarom die apply op de HA-hardware draait en niet in de cloud, staat op
de [Apply-stap](runner-setup.md)-pagina.

## Een wijziging maken en toepassen

1. **Branch + wijziging.** Maak een nieuwe branch en bewerk het betreffende
   bestand onder `desired/` (een dashboard- of automation-bestand).
2. **Pull request openen.** Push de branch en open een PR naar `main`.
3. **Reviewen.** Bekijk de diff op het tabblad *Files changed* (werkt prima in de
   GitHub mobiele app).
4. **Mergen.** Merge de PR.
5. **Toepassen.** Druk in Home Assistant op de knop **Config toepassen**. De
   add-on pullt en past toe; een statusveld toont het resultaat.

## Iets terugdraaien

Elke wijziging is een commit:

1. Open de gemergede PR (of de commit) op GitHub en klik **Revert**.
2. Merge de revert-PR.
3. Druk opnieuw op **Config toepassen**.

## Wijzigingen die je rechtstreeks in HA maakte (drift)

Pas je iets aan in de HA-interface zelf, dan loopt de repo achter. Een lokaal
**export**-script leest HA terug naar `desired/`; draai dat wanneer je zulke
wijzigingen wilt vastleggen, en commit het resultaat via een PR.

## Lokale terugval (zonder de knop)

Je kunt een gemergede wijziging ook lokaal toepassen vanaf een PC met toegang tot
HA:

```
git pull
python scripts/apply_ha.py --changed-since HEAD~1   # pas de laatste wijziging toe
python scripts/apply_ha.py --verify                 # check: live == desired
```

## Belangrijk om te onthouden

- **Altijd via een PR.** Zo houd je historie, review en rollback.
- **De apply raakt alleen `desired/`.** Aanpassingen aan docs of scripts raken je
  HA niet.
- **Vóór elke apply wordt een snapshot gemaakt**, zodat terugdraaien altijd kan.

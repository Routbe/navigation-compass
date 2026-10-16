# Admin-login: "De beveiligingscontrole is tijdelijk niet bereikbaar"

## Wat de code al laat zien
- De melding komt van de botcontrole (ALTCHA), niet van Better Auth zelf. Ze verschijnt nog vóór er ingelogd wordt.
- Bij elke e-maillogin slaat de server de opgeloste controle één keer op in de tabel `altcha_used` (tegen hergebruik). Lukt die opslag niet, dan weigert de server bewust met deze melding.
- Dus: de server kan op de admin-omgeving niet schrijven naar `altcha_used`. Mogelijke oorzaken (nog niet bevestigd): de tabel bestaat niet in de database van die omgeving, de databasegebruiker (`rout_app`) mist schrijfrechten, of de admin-omgeving wijst naar een andere database/branch dan de publieke site.
- Dit project heeft nog geen Neon-koppeling, dus ik kan de database nu niet inkijken. De oorzaak is daarom nog niet bevestigd.

## Stappen (eerst alleen kijken, niets wijzigen)
1. **Neon koppelen** via de koppelkaart (jij kiest/maakt de verbinding; ik zie het wachtwoord nooit).
2. **Alleen-lezen onderzoek** in de database:
   - bestaat `public.altcha_used`, welke rechten heeft de inlog-rol erop;
   - is migratie 54 geregistreerd in `schema_migrations`;
   - welke rol/database gebruikt de verbinding.
3. **Logs van de admin-omgeving** lezen op de regel `[ALTCHA Error]: replay store unavailable: ...` — die bevat de exacte databasefout.
4. Ik meld je de bevestigde oorzaak en de exacte SQL-opdracht vóór ik iets uitvoer.

## Herstel (pas na jouw akkoord, minimaal)
- Ontbreekt de tabel: migratie 54 (idempotent, alleen `create table if not exists` + rechten) toepassen. Dit raakt de klantenlogin niet negatief; die gebruikt dezelfde tabel en wordt er alleen betrouwbaarder door.
- Ontbreken rechten: alleen `select, insert, delete` op `altcha_used` voor de inlog-rol — geen bredere rechten.
- Verschillende database voor admin: je vertellen welke variabele in Vercel (admin-project) aangepast moet worden; geen codewijziging.
- Geen wijziging aan Better Auth-config, sessies, providers of de publieke loginroute. De botcontrole blijft "fail closed" (veiligheidsregel).

## Controle
- Daarna een echte e-maillogin op de admin-omgeving én op de publieke site testen, en `/api/public/health` op beide.
- Bestaande tests (auth, altcha, app-mode, admin-email-entry) opnieuw draaien.

## Daarna, apart: Neon veiliger maken
Met de koppeling kan ik een kort alleen-lezen veiligheidsrapport maken (rollen, wie superuser-achtige rechten heeft, ontbrekende migraties). Voorstellen daarvoor leg ik eerst aan je voor; ik voer geen rechtenwijzigingen uit zonder je bevestiging.

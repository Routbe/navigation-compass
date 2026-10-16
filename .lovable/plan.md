# Eén ROUT-maildesign voor alle e-mails

## Doel
Elke mail die ROUT verstuurt krijgt het design uit je voorbeelden: een lichte kaart met het ROUT-logo, "Sovereign Infrastructure" erboven, een statuslabel (bv. "• BETAALD"), een overzichtsblok, een afzenderblok (bv. "ROUT Billing Desk") en dezelfde footer met sociale iconen en links. Elk soort mail krijgt zijn eigen tekst, label, kleuraccent en afzenderblok. Ze zijn er in elke taal die de app ondersteunt, inhoudelijk volledig en correct.

## Wat er nu gebeurt
Mails komen uit drie bronnen met elk een eigen look:
1. Ontwerpen die in Brevo zelf zijn opgeslagen (login, reset, betalingen, verlenging, opzegging, terugbetaling, formulieren). Er zijn ongeveer 90 varianten per taal.
2. Mails die de app zelf opbouwt: verificatie, nieuwe aanmelding, app gekoppeld, subdomein, adminmeldingen, enz.
3. Een losse merk-wrapper rond meldingen.

## Stappen
1. **Back-up:** alle huidige Brevo-ontwerpen naar het project downloaden, zodat er altijd een weg terug is.
2. **Eén designbron:** je voorbeeld-HTML wordt de basisopmaak. Daarbinnen zijn vaste onderdelen beschikbaar: kop, aanhef, tekst, overzichtsblok, code-blok, knop, statuslabel, afzenderblok en footer.
3. **Een unieke mail per soort**, met eigen onderwerp, voorbeeldregel, label, accentkleur en afzender:
   - Logincode / magic link, wachtwoord reset, e-mailadres wijzigen, bevestiging, uitnodiging, herbevestiging, account verwijderen ("ROUT Security Desk")
   - Betaling ontvangen, betalingsbevestiging, betalingsprobleem, verlenging komt eraan, opzegging bevestigd, terugbetaling / creditnota ("ROUT Billing Desk")
   - Formulier / bericht ontvangen ("ROUT Support")
   - Subdomein aangevraagd of actief, verificatie ontvangen, goedgekeurd of afgewezen, nieuwe aanmelding, app gekoppeld, geheim vernieuwd, app-review ("ROUT Identity" of "ROUT Developer Console")
   - Admin- en systeemmeldingen ("ROUT Operations")
4. **Alle talen:** NL, EN, FR, DE, ES, IT, PT, PL en ZH voor elke soort. Talen die nu maar in vier varianten bestaan, krijgen ze er allemaal bij. Teksten worden per taal vertaald met dezelfde inhoud, niet letterlijk overgenomen.
5. **Naar Brevo uploaden:** de bestaande ontwerpnummers worden overschreven, zodat de app zonder andere wijzigingen meteen de nieuwe mails gebruikt. Nieuwe nummers komen er alleen bij voor talen die nog geen nummer hadden.
6. **Mails uit de app** (punt 2 en 3 hierboven) gebruiken voortaan dezelfde opmaak.
7. **Controle:** van elke soort één testmail naar hallo@rout.be in NL en EN, plus voorbeelden in de browser voor alle talen.

## Technische details
- Nieuwe module `src/emails/design/`: `layout.ts` (de HTML van de voorbeelden, tabelgebaseerd, Outlook-veilig, light-only) en `catalog.ts` (per categorie × taal: subject, preheader, badge, accent, desk, blocks, params). Brevo-placeholders blijven `{{ params.X | default: params.x | default: "…" }}`.
- `scripts/sync-brevo-templates.ts`: `GET /v3/smtp/templates` om een back-up te maken in `docs/brevo-backup/`, daarna `PUT /v3/smtp/templates/{id}` met htmlContent, subject en sender `hallo@rout.be`, en `POST` voor ontbrekende talen. De ID-map in `template-ids.ts` wordt bijgewerkt (FOUR → alle talen), met aangepaste tests.
- `kit.ts` en `brandHtml` gaan dezelfde `layout.ts` gebruiken, zodat inline-mails visueel identiek zijn. Escaping via `esc()` blijft.
- Param-namen per categorie blijven gelijk aan wat `sendLocalizedEmail` nu meestuurt. Dat controleer ik per aanroep, zodat geen enkele mail lege velden toont.
- De afbeeldingen (logo, sociale iconen) blijven de absolute URL's op rout.be uit je voorbeelden.

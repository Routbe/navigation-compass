# Login- en wachtwoordmails weer laten aankomen

## Oorzaak (bevestigd)
De app verstuurt alle mails (logincode, wachtwoord reset, uitnodigingen) rechtstreeks via Brevo. Daarvoor heeft hij de sleutel `BREVO_API_KEY` nodig. Die is na de import niet meegekomen: dit project heeft nu alleen `LOVABLE_API_KEY`, `LOVABLE_CRON_SECRET` en `NEON_API_KEY`. Zonder die sleutel slaat de app het versturen stil over, dus er komt nooit een mail aan.

## Stappen
1. Via het beveiligde formulier vragen om je Brevo-gegevens:
   - `BREVO_API_KEY` (verplicht). Te vinden in Brevo → rechtsboven je profiel → **SMTP & API** → **API Keys** → Generate. Het is een sleutel die begint met `xkeysib-`.
   - `BREVO_SENDER_EMAIL` en `BREVO_SENDER_NAME` (aanbevolen): een afzender die in Brevo onder **Senders, Domains & Dedicated IPs** al geverifieerd is, bv. `noreply@rout.be`.
2. Een logincode en een wachtwoord reset sturen naar je eigen adres en in de serverlogs nakijken wat Brevo terugmeldt (verstuurd, of de foutcode).
3. Als Brevo de mail weigert (afzender niet geverifieerd, ontbrekende template-ID, IP-blokkade), die specifieke oorzaak oplossen. Templates kunnen we, als het nodig is, met `BREVO_TEMPLATE_*` overschrijven.
4. Een waarschuwing toevoegen in het adminpaneel (Env health) wanneer mail niet geconfigureerd is, zodat dit niet meer onopgemerkt blijft. Ik kijk eerst na of die er al is.

## Technische details
- Het versturen gebeurt in `src/emails/send.server.ts` (`postToBrevo`, `isMailConfigured()` → `brevoKey()`). De aanroepen lopen via `sendLocalizedEmail` in `better-auth.server.ts` en `security-code.server.ts`.
- De code gaat direct naar `api.brevo.com` met de header `api-key`. Volgens de projectregels blijft dat zo: we schakelen niet over op een connector, dus de code verandert niet.
- Sommige Brevo-accounts laten de API alleen toe vanaf IP-adressen op een toegestane lijst. Als stap 2 een 401 "unrecognised IP" geeft, moet die beperking in Brevo → Security → Authorised IPs uit.

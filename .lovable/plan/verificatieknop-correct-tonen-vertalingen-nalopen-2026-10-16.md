# Verificatieknop correct tonen + vertalingen nalopen

## 1. "Vraag Verificatie aan" alleen voor niet-geverifieerden
- In Profiel Basisinformatie staat de knop nu altijd, ook bij geverifieerde leden.
- Nieuw: de studio gebruikt dezelfde verificatiestatus die ook het tabblad "Geverifieerd profiel" ontgrendelt.
  - Geverifieerd + actief: knop verdwijnt, in de plaats komt een klein label "Geverifieerd" met vinkje (klik toont info).
  - Aanvraag in behandeling: label "Verificatie in behandeling", geen nieuwe aanvraag.
  - Niet geverifieerd: knop blijft.
- Zelfde regel overal nalopen waar een verificatie-aanvraag kan starten (geverifieerd-pop-up, instellingen, tour), zodat een geverifieerd lid nergens nog een aanvraagknop ziet.

## 2. Vertalingen volledig maken (NL / EN / FR / DE)
- De vertaalbestanden zelf zijn compleet, maar veel teksten staan nog vast in de code (vooral in de Studio: knoppen zoals "Handle wijzigen", "Kopieer link", meldingen zoals "Link gekopieerd!", bijschriften, placeholders, tooltips en schermlezer-labels; ook gemengd Engels zoals "Profile Info", "Edit handle", "Copy link").
- Alle vaste teksten in de Studio, de pop-ups (component toevoegen, live integratie, verificatie), profielbadges en de publieke profielteksten omzetten naar vertaalsleutels met de vier talen.
- Publieke profielteksten krijgen ook Duits (nu alleen EN/NL/FR).
- Een controle-script toegevoegd dat vaste, onvertaalde teksten in de interface opspoort, zodat dit niet terugsluipt.

## Technische details
- `verifiedTabUnlocked()` (studio-access.ts) wordt de enige bron; `ProfileEditor` geeft de status (verified / pending / none) door aan `ProfileBasicInfoAccordion`. Test: geverifieerd+actief → geen aanvraagknop; pending → geen knop.
- Strings via `useI18n().t()` met sleutels in `src/locales/{nl,en,fr,de}.json`; `i18n-audit.ts` blijft groen.
- Nieuw `scripts/i18n-hardcoded.ts`: heuristische scan van JSX-tekst en `placeholder/aria-label/title`-attributen in `src/components` en `src/pages`.
- Ongebruikte `ProfileBasicInfoForm.tsx` (Engelse duplicaat) verwijderen indien nergens geïmporteerd.
- Geen wijziging aan login, database of serverregels.

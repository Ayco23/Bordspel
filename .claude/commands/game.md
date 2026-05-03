---
description: Bouw een self-contained HTML-game en geef een werkende iPhone-URL terug
argument-hint: [type spel, bv. "boter kaas en eieren" of "snake"]
---

# Bouw een speelbare game in 1 prompt

Bouw een **$ARGUMENTS** als één self-contained `index.html` en lever aan het eind een kale URL waarmee de gebruiker het spel direct op zijn iPhone kan spelen.

## Vereisten aan het spel

- Eén bestand: `index.html` met inline `<style>` en `<script>` — geen externe assets, geen build-stap.
- Mobile-first, geoptimaliseerd voor iPhone Safari:
  - `<meta name="viewport" content="width=device-width, initial-scale=1.0, viewport-fit=cover">`
  - `apple-mobile-web-app-capable` en `theme-color` meta-tags
  - `env(safe-area-inset-*)` voor notch/home-indicator
  - `touch-action: manipulation`, `-webkit-tap-highlight-color: transparent`, `user-select: none`
  - Grote touch-targets (min 44×44 px), `:active` states, `navigator.vibrate` voor haptic feedback
- Responsive layout (`100dvh`, `clamp()`, `aspect-ratio`).
- Polished UX: animaties, scoreboard waar logisch, reset-knop, duidelijke statusregel.
- Nederlandstalige UI tenzij anders gevraagd.
- Geen comments behalve waar de WHY niet voor de hand ligt.

## Git-workflow

1. Werk op de branch die in de systeeminstructies is opgegeven (anders een nieuwe `claude/build-<slug>` branch).
2. Maak het bestand aan, commit met een beschrijvende boodschap, push met `git push -u origin <branch>`.
3. Maak een **draft** pull request via de GitHub MCP-tools.

## URL teruggeven — kritieke regels

Externe preview-diensten (raw.githack, jsdelivr, statically) **kunnen geen privé-repos lezen**. Check daarom eerst of de repo publiek is:

```bash
curl -s -o /dev/null -w "%{http_code}" https://github.com/<owner>/<repo>
```

- **200** → publiek, ga door.
- **404** → privé. Vraag de gebruiker de repo op publiek te zetten via Settings → Danger Zone → Change visibility, en wacht op bevestiging voordat je een URL geeft.

Bouw de URL met de **commit-SHA**, niet met de branchnaam. Branches met een `/` erin (zoals `claude/foo-bar`) breken raw.githack omdat het de eerste segment als branch ziet:

```bash
SHA=$(git rev-parse HEAD)
echo "https://raw.githack.com/<owner>/<repo>/$SHA/index.html"
```

Verifieer met `WebFetch` dat de URL daadwerkelijk de game-HTML retourneert (geen 403/404).

## Output naar de gebruiker

Geef de URL **kaal** terug — niet in `**bold**`, niet tussen backticks, niet als markdown-link. Anders worden de opmaaktekens onderdeel van de URL als de gebruiker hem kopieert. Bijvoorbeeld:

Goed:
```
Open dit op je iPhone:

https://raw.githack.com/Owner/Repo/abc123.../index.html
```

Fout:
```
Open dit op je iPhone: **https://raw.githack.com/...**
Open dit op je iPhone: [link](https://raw.githack.com/...)
```

Voeg er één tip aan toe: deel-icoon in Safari → "Zet op beginscherm" voor full-screen.

## Checklist voor je klaar bent

- [ ] `index.html` werkt offline geopend in een browser
- [ ] Repo is publiek (geverifieerd met curl)
- [ ] Branch gepusht, draft PR aangemaakt
- [ ] URL gebouwd met commit-SHA, geverifieerd via WebFetch
- [ ] URL kaal teruggegeven, zonder markdown-wrapping

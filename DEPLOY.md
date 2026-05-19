# DEPLOY — Half-o-Fluent v2

> Build date: 2026-05-03
> Files changed: `index.html`, `style.css`, + 6 new sprites in `assets/`, + new `bem-vindo.html`

---

## STEP 1 — Preview locally (60 seconds)

Before you push anything to `main`, look at it.

Open this folder in Windows Explorer:

```
C:\Users\halfo\Obsidian-Vault\half-o-fluent-ai\Website Clone\teacher-halfo.github.io-main
```

Double-click `index.html`. It opens in your default browser as a local file (`file:///...`).

**What to check:**

- Hero terminal "boots" with typing animation, then headline appears
- Heitor (with rose) sprite floats next to dialogue bubble
- Scroll through: about → diagnosis (dark) → method → testimonials (dark) → biomes → trial Cal.com → FAQ → final CTA → socials → footer
- Click `[ COMPRAR ACESSO — R$300 ]` — opens Stripe checkout in new tab. **Test the full Stripe flow with a test card if you want.** When checkout succeeds, Stripe should redirect to `https://half-o-fluent.com/bem-vindo.html` (you may need to set this in your Stripe dashboard — see Step 4).
- Open `bem-vindo.html` directly to preview the post-payment landing page.

**Likely first impressions and quick fixes:**

| If you see... | Fix |
|---|---|
| Cal.com not loading | Normal in `file://` mode — works once deployed via HTTPS |
| Pixel font looks wrong | Check `fonts/pixel-font.ttf` is still in place |
| Pixel sprites look blurry | Browser zoom — `image-rendering: pixelated` should keep them sharp at 100% |
| Stripe redirects to a generic page after payment | Set the Stripe Payment Link's success URL — see Step 4 |

---

## STEP 2 — Set Stripe success URL (one-time)

In your Stripe Dashboard:

1. Go to **Payment Links** → find the Comunidade R$300/6mo link
2. Click **Edit** → **After payment** → **Don't show confirmation page** → **Redirect customers to your website**
3. URL: `https://half-o-fluent.com/bem-vindo.html`
4. Save

This wires Stripe's "thanks for paying" → your custom welcome page. Without this, paying members never see the onboarding.

---

## STEP 3 — Push to GitHub Pages

The folder you're working in (`teacher-halfo.github.io-main`) is the unzipped download — it has no `.git` directory. You have two paths:

### Option A — You have the repo cloned somewhere else (recommended)

If you already have a real `git clone` of `teacher-halfo.github.io` somewhere on your machine (e.g. `C:\Users\halfo\Documents\GitHub\teacher-halfo.github.io`):

```powershell
# Set these once
$BUILD = "C:\Users\halfo\Obsidian-Vault\half-o-fluent-ai\Website Clone\teacher-halfo.github.io-main"
$REPO  = "C:\Users\halfo\Documents\GitHub\teacher-halfo.github.io"   # ← change to your real path

# Copy build into repo (overwrites)
Copy-Item -Path "$BUILD\index.html"      -Destination "$REPO\index.html"      -Force
Copy-Item -Path "$BUILD\style.css"       -Destination "$REPO\style.css"       -Force
Copy-Item -Path "$BUILD\bem-vindo.html"  -Destination "$REPO\bem-vindo.html"  -Force
Copy-Item -Path "$BUILD\assets\heitor-*" -Destination "$REPO\assets\"         -Force

# Commit + push
cd $REPO
git add index.html style.css bem-vindo.html assets/heitor-*.png
git status                          # sanity check
git commit -m "v2: PT site, solarpunk terminal aesthetic, Stripe community gateway, /bem-vindo onboarding"
git push origin main                # GitHub Pages redeploys in ~30s
```

### Option B — Fresh clone right now

If you don't have a working git clone:

```powershell
cd C:\Users\halfo\Documents
git clone https://github.com/Teacher-halfo/teacher-halfo.github.io.git
$REPO = "C:\Users\halfo\Documents\teacher-halfo.github.io"
$BUILD = "C:\Users\halfo\Obsidian-Vault\half-o-fluent-ai\Website Clone\teacher-halfo.github.io-main"

Copy-Item -Path "$BUILD\index.html"      -Destination "$REPO\index.html"      -Force
Copy-Item -Path "$BUILD\style.css"       -Destination "$REPO\style.css"       -Force
Copy-Item -Path "$BUILD\bem-vindo.html"  -Destination "$REPO\bem-vindo.html"  -Force
Copy-Item -Path "$BUILD\assets\heitor-*" -Destination "$REPO\assets\"         -Force

cd $REPO
git add .
git commit -m "v2: PT site, solarpunk terminal aesthetic, Stripe community gateway, /bem-vindo onboarding"
git push origin main
```

GitHub Pages picks it up automatically. Live on `half-o-fluent.com` within 30–60 seconds.

---

## STEP 4 — After push: verify production

Open `https://half-o-fluent.com` in **incognito** (so you bypass cache).

Checklist:
- [ ] Hero loads, boot animation plays
- [ ] Cal.com embed renders (this is the one thing that needs HTTPS to fully work)
- [ ] Stripe button opens checkout in new tab
- [ ] Run a real test: pay with a Stripe test card → confirm redirect to `/bem-vindo.html`
- [ ] Check mobile (phone or DevTools responsive mode)

---

## ROLLBACK

If anything breaks in production, revert is one command:

```powershell
cd $REPO
git revert HEAD --no-edit
git push origin main
```

Site rolls back to the previous version in ~30s.

---

## What changed (for the curious)

| File | What |
|---|---|
| `index.html` | Full PT rewrite, restructured into 9 sections (hero, sobre, diagnóstico, método, prova, biomas, trial, FAQ, final CTA), terminal-window components, boot-screen typing JS |
| `style.css` | New solarpunk + hacker palette (forest/phosphor green, sun yellow, parchment cream, ember red), terminal-window component, scanline overlay, mountain silhouettes, biome cards, FAQ accordion, mobile-first responsive |
| `bem-vindo.html` | NEW. Stripe `success_url` destination. 3-step onboarding: Discord → orientation video placeholder → optional welcome call |
| `assets/heitor-mug.png` | NEW. Heitor with Michael Scott mug — used in About section |
| `assets/heitor-speaking.png` | NEW. Available for future use |
| `assets/heitor-intro-1..3.png` | NEW. Available for future use |
| `assets/heitor-intro-4-rose.png` | NEW. Heitor with rose — hero avatar |

Old files preserved in your existing repo's git history. Nothing destroyed.

---

## Aesthetic toggle: Stripe Buy Button vs Payment Link

Currently shipping with Payment Link wrapped in a custom pixel button (matches the rest of the site). If after seeing it live you want to A/B with the Stripe Buy Button widget instead, replace this block in `index.html`:

```html
<!-- in the Comunidade biome card -->
<a href="https://buy.stripe.com/fZu4gAd9Y3hodQjbEabfO04" class="btn-primary biome-cta pixel-font" target="_blank" rel="noopener">[ COMPRAR ACESSO — R$300 ]</a>
```

with:

```html
<script async src="https://js.stripe.com/v3/buy-button.js"></script>
<stripe-buy-button
  buy-button-id="buy_btn_1TStOYKkmlfo0I0pt1TBp4bo"
  publishable-key="pk_live_51QbHXJKkmlfo0I0pyd9jGGe112HPbE3CVmEpVfAbw3LcWo93CJUAgBenMLpTvfMNGRjqWU5Q1cn7Booiyl2o8xYL00e3AcEsoT">
</stripe-buy-button>
```

Same swap in the final CTA section. My recommendation is to ship Payment Link first — easier to read your audience's reaction to the cohesive pixel aesthetic, then tweak.

---

## Known limitations / future v2.1

- **Discord role gating is manual.** Members ping you in `#bem-vindo` → you assign the paid role. Auto-gating needs a Cloudflare Worker listening to a Stripe webhook (~half day of work). Worth doing once you hit ~20 members.
- **Orientation video is a placeholder.** Record a 90-sec Loom and replace the placeholder div in `bem-vindo.html`.
- **No analytics.** If you want, drop in Plausible or Umami (privacy-friendly) — one `<script>` tag, ~5 minutes.
- **No favicon update** — kept the existing one. Replace `assets/favicon.png` if you want a new one.

---

*Made in one session with Heitor. Conversa > gramática.*

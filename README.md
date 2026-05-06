# DSI Workshop

---

## Inhoud

- [Aan de slag](#aan-de-slag)
- [Projectstructuur](#projectstructuur)
- [SCSS-architectuur](#scss-architectuur)
- [BEM-methodologie](#bem-methodologie)
- [Variabelen en mixins](#variabelen-en-mixins)
- [Een nieuw component toevoegen](#een-nieuw-component-toevoegen)
- [De pagina](#de-pagina)

---

## Aan de slag

### Vereisten

Zorg dat je het volgende geïnstalleerd hebt:

- [Node.js](https://nodejs.org/) (versie 18 of hoger)
- [Yarn](https://yarnpkg.com/)

# 📋 Installatie

## Stap 1: Fork de repository
Met een **fork** zorg je ervoor dat je een eigen kopie van het project op jouw GitHub-account krijgt. Dit is jouw persoonlijke versie waar je vrij aan kunt werken zonder anderen te beïnvloeden.

-> Ga naar [github.com/RayelNabie/dsi_workshop](https://github.com/RayelNabie/dsi_workshop) en klik op de **Fork** knop (rechtsboven).

## Stap 2: Clone jouw fork
Een **clone** zorgt ervoor dat een kopie van de codebase op jouw persoonlijke computer belandt. Dit is het moment dat je de code lokaal gaat aanpassen.

Vervang `JOUW-GITHUB-USERNAME` met jouw eigen GitHub gebruikersnaam:

```bash
git clone https://github.com/JOUW-GITHUB-USERNAME/dsi_workshop.git
```

## Stap 3: Map openen en dependencies installeren
Navigeer naar de map die je net hebt gedownload:

```bash
cd dsi_workshop
```

Installeer alle benodigde packages met yarn:

```bash
yarn install
```

Dit kan even duren – yarn downloadt alle dependencies die nodig zijn om het project te laten draaien.

---

## Ontwikkelserver starten

Nu je alles hebt geïnstalleerd, kun je de ontwikkelserver starten:

```bash
yarn dev
```

Open vervolgens [http://localhost:3000](http://localhost:3000) in je browser. De pagina herlaadt automatisch zodra je een bestand opslaat.

### Bouwen voor productie

```bash
yarn build
```

---

## Projectstructuur

```
dsi_workshop/
├── src/
│   ├── app/
│   │   ├── layout.tsx        # Root layout — importeert de globale SCSS
│   │   └── page.tsx          # De enige pagina — hier staat alle HTML
│   │
│   └── styles/
│       ├── main.scss         # Hoofdbestand — importeert alle lagen
│       │
│       ├── abstracts/        # Geen CSS-uitvoer, alleen hulpmiddelen
│       │   ├── _variables.scss   # Kleuren, spacing, typografie, breakpoints
│       │   ├── _mixins.scss      # Herbruikbare CSS-patronen (respond-to, flex-center)
│       │   └── _index.scss       # Exporteert alles uit abstracts
│       │
│       ├── base/             # Globale HTML-stijlen
│       │   ├── _reset.scss       # Verwijdert browserstijlen
│       │   ├── _typography.scss  # Basislettertype, koppen
│       │   └── _index.scss       # Exporteert alles uit base
│       │
│       ├── components/       # BEM-componenten (hier ga jij werken)
│       │   └── _index.scss       # Importeer hier jouw componentbestanden
│       │
│       └── helpers/          # Enkeldoelige hulpklassen
│           └── _index.scss       # .container en .visually-hidden
│
├── public/                   # Statische bestanden (afbeeldingen, fonts)
├── next.config.ts            # Next.js configuratie (incl. SCSS-instellingen)
└── package.json
```

---

## SCSS-architectuur

Dit project gebruikt een vereenvoudigde versie van het **7-1 patroon** — een veelgebruikte manier om SCSS-bestanden te organiseren. Elke laag heeft een eigen verantwoordelijkheid.

### De lagen in volgorde

```
main.scss
│
├── base/        (1) Reset + basisstijlen — altijd als eerste
├── components/  (2) BEM-componenten
└── helpers/     (3) Hulpklassen — altijd als laatste
```

> **Waarom deze volgorde?**
> CSS is cascade-gebaseerd: wat later komt, wint. De reset moet als eerste komen zodat alles daarna het kan overschrijven. Helpers komen als laatste zodat ze altijd winnen van componentstijlen.

### Abstracts — apart geval

De `abstracts/`-map staat **niet** in `main.scss`. Abstracts produceren geen CSS-uitvoer — het zijn alleen variabelen en mixins. Je laadt ze rechtstreeks in het bestand dat ze nodig heeft:

```scss
// Bovenaan jouw componentbestand
@use "../abstracts" as a;

.card {
  background-color: a.$color-white;
  padding: a.$spacing-md;
}
```

### Moderne SCSS: `@use` en `@forward`

Dit project gebruikt de **moderne SCSS-syntax** (geen `@import`).

| Sleutelwoord | Waarvoor                                                     |
| ------------ | ------------------------------------------------------------ |
| `@use`       | Laad een bestand en gebruik de inhoud ervan                  |
| `@forward`   | Exporteer een bestand zodat anderen het via jou kunnen laden |

Het `_index.scss` in elke map bundelt alles met `@forward`, zodat je met één `@use 'abstracts'` alles krijgt.

---

## BEM-methodologie

BEM staat voor **Block, Element, Modifier**. Het is een naamgevingsconventie voor CSS-klassen die zorgt voor overzichtelijke, herbruikbare en conflictvrije stijlen.

### Blok

Een zelfstandig, herbruikbaar component. Het blok is de basis.

```html
<div class="card">...</div>
<nav class="nav">...</nav>
<section class="hero">...</section>
```

```scss
.card { ... }
.nav { ... }
.hero { ... }
```

### Element

Een onderdeel van een blok. Elementen hebben geen betekenis buiten hun blok. Gescheiden door **dubbele underscore** (`__`).

```html
<div class="card">
  <div class="card__image">...</div>
  <div class="card__body">
    <h3 class="card__title">...</h3>
    <p class="card__text">...</p>
  </div>
</div>
```

```scss
.card {
  &__image { ... }  // wordt .card__image
  &__body  { ... }  // wordt .card__body
  &__title { ... }  // wordt .card__title
  &__text  { ... }  // wordt .card__text
}
```

> **Let op:** je schrijft nooit `.card__body__title`. Elementen zijn altijd direct kind van het blok, ook als ze in de HTML dieper genest zijn.

### Modifier

Een variatie op een blok of element. Gescheiden door **dubbel koppelteken** (`--`).

```html
<article class="card card--featured">...</article>
<a class="button button--primary">...</a>
<a class="button button--outline">...</a>
```

```scss
.card {
  // Basisstijl van de kaart

  &--featured {
    // Alleen de afwijkende stijlen
    border: 2px solid $color-primary;
  }
}
```

> **Belangrijk:** een modifier staat **altijd naast** het blok, nooit alleen. Je schrijft dus `class="card card--featured"`, niet `class="card--featured"`.

### Samenvatting

```
.blok
.blok__element
.blok--modifier
.blok__element--modifier
```

---

## Variabelen en mixins

### Variabelen (`abstracts/_variables.scss`)

Importeer als: `@use '../abstracts' as a;`

#### Kleuren

| Variabele            | Waarde    | Gebruik                    |
| -------------------- | --------- | -------------------------- |
| `a.$color-primary`   | `#3a86ff` | Knoppen, links, accenten   |
| `a.$color-secondary` | `#ff006e` | Highlights, badges         |
| `a.$color-dark`      | `#1a1a2e` | Tekst, donkere achtergrond |
| `a.$color-light`     | `#f8f9fa` | Lichte achtergrond         |
| `a.$color-white`     | `#ffffff` | Wit                        |

#### Spacing

| Variabele       | Waarde   | Gebruik            |
| --------------- | -------- | ------------------ |
| `a.$spacing-xs` | `0.5rem` | Kleine ruimtes     |
| `a.$spacing-sm` | `1rem`   | Binnen componenten |
| `a.$spacing-md` | `2rem`   | Tussen componenten |
| `a.$spacing-lg` | `4rem`   | Tussen secties     |
| `a.$spacing-xl` | `8rem`   | Hero-ruimtes       |

#### Typografie

| Variabele             | Waarde                   |
| --------------------- | ------------------------ |
| `a.$font-family-base` | `system-ui, sans-serif`  |
| `a.$font-size-base`   | `1rem` (16px)            |
| `a.$font-size-lg`     | `1.25rem`                |
| `a.$font-size-xl`     | `2rem`                   |
| `a.$font-size-hero`   | `clamp(2rem, 5vw, 4rem)` |

### Mixins (`abstracts/_mixins.scss`)

#### `respond-to` — responsive breakpoints

Schrijf altijd **mobile-first**: kleine schermen eerst, dan grotere.

```scss
.hero__title {
  font-size: a.$font-size-xl; // klein scherm

  @include a.respond-to(md) {
    font-size: a.$font-size-hero; // tablet en groter
  }
}
```

Beschikbare breakpoints: `sm` (576px), `md` (768px), `lg` (1024px), `xl` (1280px)

#### `flex-center` — centreer horizontaal én verticaal

```scss
.card__image {
  @include a.flex-center;
}
```

---

## Een nieuw component toevoegen

Volg deze drie stappen:

### Stap 1 — Maak een nieuw bestand aan

Maak een bestand aan in `src/styles/components/`. Gebruik een underscore aan het begin (dat is een SCSS-partial).

```
src/styles/components/_button.scss
```

### Stap 2 — Schrijf het component in BEM

```scss
// src/styles/components/_button.scss

@use "../abstracts" as a; // Laad variabelen en mixins

.button {
  display: inline-block;
  padding: a.$spacing-xs a.$spacing-sm;
  border-radius: 4px;
  font-weight: 600;
  cursor: pointer;
  border: 2px solid transparent;

  // Modifier — primaire knop
  &--primary {
    background-color: a.$color-primary;
    color: a.$color-white;
  }

  // Modifier — omrande knop
  &--outline {
    background-color: transparent;
    border-color: a.$color-primary;
    color: a.$color-primary;
  }
}
```

### Stap 3 — Registreer het in de index

Verwijder de commentaarmarkering voor jouw component in `src/styles/components/_index.scss`:

```scss
@forward "button"; // ← commentaar weghalen
```

Het component is nu actief op de pagina.

---

## De pagina

`src/app/page.tsx` bevat een onepager met de volgende secties. Alle BEM-klassen staan al in de HTML — jij hoeft alleen de SCSS te schrijven.

| Sectie           | BEM-blok                          | Beschrijving                        |
| ---------------- | --------------------------------- | ----------------------------------- |
| Navigatiebalk    | `.header`, `.nav`                 | Logo + drie navigatielinks          |
| Hero             | `.hero`                           | Titel, subtitel en twee knoppen     |
| Kaarten          | `.section`, `.card-grid`, `.card` | Drie kaarten (één met `--featured`) |
| Contactformulier | `.section--dark`, `.form`         | Naam, e-mail, bericht, verzendknop  |
| Footer           | `.footer`                         | Copyright-tekst                     |

### BEM-klassen op de pagina

```
.header
.nav
  .nav__logo
  .nav__list
    .nav__item
      .nav__link
      .nav__link--cta        ← modifier op element

.hero
  .hero__content
    .hero__title
    .hero__subtitle
    .hero__actions
      .button
      .button--primary       ← modifier
      .button--outline       ← modifier

.section
  .section--dark             ← modifier op blok
  .section__title
  .card-grid
    .card
    .card--featured          ← modifier
      .card__image
      .card__body
        .card__title
        .card__text
        .card__link

.form
  .form__group
    .form__label
    .form__input
    .form__textarea

.footer
  .footer__copy
```

### Hulpklassen (al klaar)

| Klasse             | Gebruik                                                 |
| ------------------ | ------------------------------------------------------- |
| `.container`       | Centreer en begrens de breedte van de inhoud tot 1200px |
| `.visually-hidden` | Verberg visueel maar houd zichtbaar voor screenreaders  |

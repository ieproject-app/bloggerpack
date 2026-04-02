# SKILL: Blogger Template Development (Bloggerpack + Modern CSS)

> Panduan lengkap untuk AI assistant (GitHub Copilot, Claude, dsb.) agar bisa generate
> Blogger template yang benar secara sintaks, modern secara CSS, dan bagus secara desain.
> Letakkan file ini di root project dan referensikan saat prompt ke AI.

---

## 1. PROJECT CONTEXT

Ini adalah project pengembangan **Blogger (Blogspot) theme** menggunakan **Bloggerpack**
sebagai build tool. Target distribusi: **free + premium (freemium model)**.

- Platform: Google Blogger (Blogspot)
- Build tool: Bloggerpack (Nunjucks template engine, Sass, Skin CSS)
- Target: Template siap jual/distribusi — harus bersih, SEO-friendly, customizable
- CSS approach: Modern CSS (custom properties, Grid, Flexbox, clamp, container queries)
- Tidak menggunakan CSS framework eksternal (no Bootstrap, no Tailwind) — pure CSS

---

## 2. STRUKTUR PROJECT (WAJIB DIIKUTI)

```
my-theme/
├── src/
│   ├── template/
│   │   ├── index.xml              ← entry point utama
│   │   └── components/
│   │       ├── head.xml
│   │       ├── header.xml
│   │       ├── navbar.xml
│   │       ├── hero.xml
│   │       ├── post-card.xml
│   │       ├── post-list.xml
│   │       ├── post-page.xml
│   │       ├── sidebar.xml
│   │       ├── footer.xml
│   │       └── pagination.xml
│   ├── sass/
│   │   ├── index.scss             ← entry point Sass
│   │   ├── _tokens.scss           ← design tokens (CSS custom properties)
│   │   ├── _reset.scss
│   │   ├── _typography.scss
│   │   ├── _layout.scss
│   │   └── components/
│   │       └── _*.scss
│   ├── skin/
│   │   ├── index.css              ← entry point Skin (Blogger Theme Designer)
│   │   └── _variables.css         ← Blogger skin variables definitions
│   └── js/
│       ├── index.js               ← entry point JS
│       └── modules/
│           └── *.js
├── theme-config.json              ← data global (nama blog, warna default, dsb.)
├── package.json
└── BLOGGER_TEMPLATE_SKILL.md     ← file ini
```

---

## 3. BLOGGERPACK: ATURAN SINTAKS KRITIS

### 3.1 File Template (`.xml` dan `.bloggerpack.xml`)

Semua file template menggunakan ekstensi `.xml`.
File komponen yang mengandung Sass/Skin/JS inline menggunakan `.bloggerpack.xml`.

**Tag wajib di setiap file template:**
```xml
<bp:template>
  <!-- markup HTML + Blogger tags di sini -->
</bp:template>
```

**Cara include komponen (JANGAN pakai `{% include %}` bawaan Nunjucks):**
```xml
{# BENAR — pakai {% template %} #}
{% template "src/template/components/header.xml" %}

{# SALAH — jangan pakai ini #}
{% include "src/template/components/header.xml" %}
```

**Cara include asset (CSS/JS yang sudah dikompile):**
```xml
{% asset %}
<style>
  /* CSS akan di-inject di sini, tidak di-pretty oleh formatter */
</style>
{% endasset %}
```

### 3.2 Komponen `.bloggerpack.xml` — Struktur Lengkap

```xml
<bp:template>
  <div class='my-component' id='myComponent'>
    <!-- HTML markup -->
  </div>
</bp:template>

<bp:sass>
  /* Sass/SCSS untuk komponen ini */
  .my-component {
    display: flex;
  }
</bp:sass>

<bp:skin>
  /* CSS dengan Blogger skin variables — untuk Theme Designer */
  .my-component {
    background: $(my.bg.color);
  }
</bp:skin>

<bp:js>
  /* JavaScript untuk komponen ini */
  const el = document.getElementById('myComponent');
</bp:js>
```

### 3.3 `theme-config.json` — Data Global

```json
{
  "theme": {
    "name": "NamaTheme",
    "version": "1.0.0",
    "author": "Nama Kamu",
    "description": "Deskripsi tema"
  },
  "color": {
    "primary": "#1a73e8",
    "bg": "#ffffff",
    "text": "#202124"
  }
}
```

Akses di template: `{{ data.theme.name }}`, `{{ data.color.primary }}`

---

## 4. BLOGGER XML TAGS — REFERENSI LENGKAP

### 4.1 Struktur Dasar Template Blogger

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE html>
<html b:css='false' b:defaultwidgetversion='2' b:js='false'
      b:layoutsVersion='3' b:responsive='true'
      b:templateUrl='index' b:templateVersion='1.0.0'
      expr:dir='data:blog.languageDirection'
      expr:lang='data:blog.locale'
      xmlns='http://www.w3.org/1999/xhtml'
      xmlns:b='http://www.google.com/2005/gbl'
      xmlns:data='http://www.google.com/2005/gbl'
      xmlns:expr='http://www.google.com/2005/gbl'>

<head>
  <!-- ... -->
</head>

<body>
  <!-- Minimal 1 <b:section> wajib ada -->
</body>
</html>
```

### 4.2 Data Tags — Variabel Blogger

| Tag | Keterangan |
|-----|-----------|
| `<data:blog.title/>` | Judul blog |
| `<data:blog.url/>` | URL blog |
| `<data:blog.homepageUrl/>` | URL homepage |
| `<data:blog.pageType/>` | Tipe halaman: `index`, `item`, `archive`, `error_page` |
| `<data:blog.locale/>` | Bahasa: `id`, `en`, dsb. |
| `<data:post.title/>` | Judul post |
| `<data:post.url/>` | URL post |
| `<data:post.body/>` | Isi post (HTML) |
| `<data:post.snippet/>` | Cuplikan post |
| `<data:post.author/>` | Nama penulis |
| `<data:post.timestampISO8601/>` | Timestamp ISO (untuk `<time datetime>`) |
| `<data:post.timestamp/>` | Tanggal format human readable |
| `<data:post.labels/>` | List label/kategori |
| `<data:post.thumbnailUrl/>` | URL thumbnail post |
| `<data:post.numComments/>` | Jumlah komentar |
| `<data:post.allowComments/>` | Boolean: komentar diizinkan? |

### 4.3 Conditional Tags

```xml
{# Cek tipe halaman #}
<b:if cond='data:blog.pageType == &quot;index&quot;'>
  <!-- tampil di homepage -->
<b:elseif cond='data:blog.pageType == &quot;item&quot;'/>
  <!-- tampil di halaman post -->
<b:else/>
  <!-- fallback -->
</b:if>

{# Cek apakah ada thumbnail #}
<b:if cond='data:post.thumbnailUrl'>
  <img expr:src='data:post.thumbnailUrl' alt='' loading='lazy'/>
</b:if>

{# Cek label #}
<b:if cond='data:post.labels'>
  <!-- ada label -->
</b:if>
```

**Operator yang valid:** `==`, `!=`, `&lt;` (<), `&gt;` (>), `and`, `or`, `not`

### 4.4 Loop / Iterasi

```xml
{# Loop posts (di dalam widget Blog) #}
<b:loop values='data:posts' var='post'>
  <article>
    <h2><data:post.title/></h2>
    <a expr:href='data:post.url'>Baca selengkapnya</a>
  </article>
</b:loop>

{# Loop labels #}
<b:loop values='data:post.labels' var='label'>
  <a expr:href='data:label.url'><data:label.name/></a>
</b:loop>
```

### 4.5 `expr:` — Dynamic Attribute

Gunakan `expr:` untuk attribute yang nilainya dinamis dari data Blogger:

```xml
{# BENAR #}
<a expr:href='data:post.url'><data:post.title/></a>
<img expr:src='data:post.thumbnailUrl' expr:alt='data:post.title'/>
<time expr:datetime='data:post.timestampISO8601'><data:post.timestamp/></time>

{# SALAH — jangan campur #}
<a href='data:post.url'>...</a>
```

### 4.6 Section & Widget

```xml
{# Section = area yang bisa diisi widget #}
<b:section id='main' class='main-section' maxwidgets='1' showaddelement='no'>
  <b:widget id='Blog1' type='Blog' locked='true' title='Posting Blog'>
    <b:widget-settings>
      <b:widget-setting name='showDateHeader'>false</b:widget-setting>
      <b:widget-setting name='showCommentLink'>true</b:widget-setting>
    </b:widget-settings>
    <b:includable id='main'>
      {# Template utama widget blog #}
      <b:loop values='data:posts' var='post'>
        <b:include name='post'/>
      </b:loop>
    </b:includable>
    <b:includable id='post' var='post'>
      {# Template per-post #}
      <article class='post-card'>
        <h2 class='post-card__title'>
          <a expr:href='data:post.url'><data:post.title/></a>
        </h2>
      </article>
    </b:includable>
  </b:widget>
</b:section>
```

### 4.7 `<b:skin>` — CSS dalam Blogger

```xml
<b:skin><![CDATA[
/* CSS utama di sini */
/* Skin variables WAJIB di dalam CDATA */
]]></b:skin>
```

### 4.8 JavaScript — Wajib CDATA

```xml
<script type='text/javascript'>
//<![CDATA[
  // JavaScript di sini
  document.addEventListener('DOMContentLoaded', function() {
    // ...
  });
//]]>
</script>
```

### 4.9 Skin Variables (Blogger Theme Designer)

```css
/* Definisi variable — format wajib persis seperti ini */
/*
<Variable name="color.primary" description="Warna Utama" type="color"
  default="#1a73e8" value="#1a73e8"/>
<Variable name="color.bg" description="Warna Background" type="color"
  default="#ffffff" value="#ffffff"/>
<Variable name="font.body" description="Font Utama" type="font"
  default="normal normal 16px Georgia, Serif" value="normal normal 16px Georgia, Serif"/>
*/

/* Penggunaan */
body {
  background: $(color.bg);
  font: $(font.body);
}
a {
  color: $(color.primary);
}
```

---

## 5. MODERN CSS APPROACH

### 5.1 Design Tokens — `src/sass/_tokens.scss`

```scss
// Semua nilai desain didefinisikan sebagai CSS custom properties
// Bukan Sass variables, agar bisa dioverride via JavaScript/skin

:root {
  // --- Color Palette ---
  --color-primary:        #1a73e8;
  --color-primary-hover:  #1557b0;
  --color-accent:         #fbbc04;
  --color-bg:             #ffffff;
  --color-bg-secondary:   #f8f9fa;
  --color-surface:        #ffffff;
  --color-border:         #e8eaed;
  --color-text:           #202124;
  --color-text-muted:     #5f6368;
  --color-text-inverse:   #ffffff;

  // --- Dark mode (prefers-color-scheme) ---
  @media (prefers-color-scheme: dark) {
    --color-bg:            #1a1a1a;
    --color-bg-secondary:  #242424;
    --color-surface:       #2d2d2d;
    --color-border:        #3d3d3d;
    --color-text:          #e8eaed;
    --color-text-muted:    #9aa0a6;
  }

  // --- Typography Scale (fluid) ---
  --font-family-sans:    'Plus Jakarta Sans', system-ui, sans-serif;
  --font-family-serif:   'Lora', Georgia, serif;
  --font-family-mono:    'JetBrains Mono', 'Fira Code', monospace;

  --font-size-xs:    clamp(0.75rem,  0.7rem + 0.25vw,  0.875rem);
  --font-size-sm:    clamp(0.875rem, 0.82rem + 0.27vw, 1rem);
  --font-size-base:  clamp(1rem,     0.95rem + 0.25vw, 1.125rem);
  --font-size-lg:    clamp(1.125rem, 1rem + 0.625vw,   1.375rem);
  --font-size-xl:    clamp(1.25rem,  1rem + 1.25vw,    1.75rem);
  --font-size-2xl:   clamp(1.5rem,   1.1rem + 2vw,     2.25rem);
  --font-size-3xl:   clamp(1.875rem, 1.2rem + 3.375vw, 3rem);
  --font-size-4xl:   clamp(2.25rem,  1.3rem + 4.75vw,  3.75rem);

  --font-weight-normal:   400;
  --font-weight-medium:   500;
  --font-weight-semibold: 600;
  --font-weight-bold:     700;

  --line-height-tight:  1.2;
  --line-height-snug:   1.4;
  --line-height-normal: 1.6;
  --line-height-relaxed:1.8;

  // --- Spacing Scale ---
  --space-1:   0.25rem;   //  4px
  --space-2:   0.5rem;    //  8px
  --space-3:   0.75rem;   // 12px
  --space-4:   1rem;      // 16px
  --space-5:   1.25rem;   // 20px
  --space-6:   1.5rem;    // 24px
  --space-8:   2rem;      // 32px
  --space-10:  2.5rem;    // 40px
  --space-12:  3rem;      // 48px
  --space-16:  4rem;      // 64px
  --space-20:  5rem;      // 80px
  --space-24:  6rem;      // 96px

  // --- Layout ---
  --container-sm:   640px;
  --container-md:   768px;
  --container-lg:   1024px;
  --container-xl:   1280px;
  --container-2xl:  1440px;

  --sidebar-width:  300px;
  --gap-grid:       var(--space-6);

  // --- Border Radius ---
  --radius-sm:   4px;
  --radius-md:   8px;
  --radius-lg:   12px;
  --radius-xl:   16px;
  --radius-full: 9999px;

  // --- Shadows ---
  --shadow-sm:  0 1px 3px rgba(0,0,0,0.08), 0 1px 2px rgba(0,0,0,0.04);
  --shadow-md:  0 4px 6px rgba(0,0,0,0.07), 0 2px 4px rgba(0,0,0,0.05);
  --shadow-lg:  0 10px 15px rgba(0,0,0,0.1), 0 4px 6px rgba(0,0,0,0.05);
  --shadow-xl:  0 20px 25px rgba(0,0,0,0.1), 0 8px 10px rgba(0,0,0,0.04);

  // --- Transitions ---
  --transition-fast:   150ms ease;
  --transition-normal: 250ms ease;
  --transition-slow:   350ms ease;

  // --- Z-index ---
  --z-below:    -1;
  --z-base:      0;
  --z-raised:   10;
  --z-overlay:  20;
  --z-sticky:  100;
  --z-modal:   200;
  --z-toast:   300;
}
```

### 5.2 Layout Patterns (Grid & Flexbox)

```scss
// Container
.container {
  width: min(100% - var(--space-8), var(--container-xl));
  margin-inline: auto;
}

// Blog layout: content + sidebar
.blog-layout {
  display: grid;
  grid-template-columns: 1fr;
  gap: var(--gap-grid);

  @media (min-width: 1024px) {
    grid-template-columns: 1fr var(--sidebar-width);
  }
}

// Post grid (responsive, tanpa media query — menggunakan auto-fill)
.post-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(min(100%, 320px), 1fr));
  gap: var(--gap-grid);
}
```

### 5.3 CSS Rules untuk Blogger

**WAJIB diketahui AI:**

1. Semua CSS yang butuh Blogger skin variables → taruh di `src/skin/`
2. CSS murni styling (tidak butuh skin variables) → taruh di `src/sass/`
3. Jangan `@import` Google Fonts di Sass — taruh `<link>` di `<head>` template
4. Selector spesifik untuk tipe halaman bisa pakai class yang ditambahkan via `<b:if>` di body tag

```xml
{# Tambahkan class ke body sesuai page type #}
<body>
  <b:if cond='data:blog.pageType == &quot;index&quot;'>
    <div class='page-home'>
  <b:elseif cond='data:blog.pageType == &quot;item&quot;'/>
    <div class='page-post'>
  <b:else/>
    <div class='page-other'>
  </b:if>
    <!-- konten -->
  </div>
</body>
```

---

## 6. DESIGN SYSTEM — PANDUAN DESAIN

### 6.1 Prinsip Desain Template Ini

- **Clean & readable** — konten adalah raja, UI tidak boleh mengalihkan perhatian
- **Editorial feel** — terasa seperti majalah/publikasi profesional, bukan blog biasa
- **Performa tinggi** — tidak ada library besar, CSS ringan, lazy load gambar
- **Customizable** — warna utama dan font bisa diubah via Blogger Theme Designer
- **Mobile-first** — desain dimulai dari mobile, bukan di-shrink dari desktop

### 6.2 Typography System

```scss
// Heading menggunakan font serif untuk editorial feel
h1, h2, h3 {
  font-family: var(--font-family-serif);
  font-weight: var(--font-weight-bold);
  line-height: var(--line-height-tight);
  color: var(--color-text);
}

// Body text pakai sans-serif untuk readability
body {
  font-family: var(--font-family-sans);
  font-size: var(--font-size-base);
  line-height: var(--line-height-relaxed);
  color: var(--color-text);
}

// Ukuran heading
h1 { font-size: var(--font-size-4xl); }
h2 { font-size: var(--font-size-3xl); }
h3 { font-size: var(--font-size-2xl); }
h4 { font-size: var(--font-size-xl); }
h5 { font-size: var(--font-size-lg); }
h6 { font-size: var(--font-size-base); }
```

### 6.3 Post Card Anatomy (Komponen Kritis)

Setiap post card HARUS mengandung elemen ini (berurutan dari atas):

1. **Thumbnail** — aspect-ratio 16/9, object-fit cover, lazy loading
2. **Category/Label** — pill kecil di atas judul
3. **Judul** — clickable, max 2 baris (line-clamp: 2)
4. **Excerpt** — max 3 baris (line-clamp: 3)
5. **Meta** — author + tanggal + reading time (jika ada)

```scss
.post-card {
  background: var(--color-surface);
  border-radius: var(--radius-lg);
  overflow: hidden;
  box-shadow: var(--shadow-sm);
  transition: box-shadow var(--transition-normal),
              transform var(--transition-normal);

  &:hover {
    box-shadow: var(--shadow-lg);
    transform: translateY(-2px);
  }

  &__thumbnail {
    aspect-ratio: 16 / 9;
    overflow: hidden;

    img {
      width: 100%;
      height: 100%;
      object-fit: cover;
      transition: transform var(--transition-slow);
    }

    &:hover img {
      transform: scale(1.04);
    }
  }

  &__body {
    padding: var(--space-5) var(--space-6);
  }

  &__label {
    display: inline-flex;
    align-items: center;
    padding: var(--space-1) var(--space-3);
    background: color-mix(in srgb, var(--color-primary) 12%, transparent);
    color: var(--color-primary);
    border-radius: var(--radius-full);
    font-size: var(--font-size-xs);
    font-weight: var(--font-weight-semibold);
    text-transform: uppercase;
    letter-spacing: 0.05em;
    text-decoration: none;
    margin-bottom: var(--space-3);
  }

  &__title {
    font-size: var(--font-size-xl);
    font-family: var(--font-family-serif);
    line-height: var(--line-height-snug);
    display: -webkit-box;
    -webkit-line-clamp: 2;
    -webkit-box-orient: vertical;
    overflow: hidden;
    margin-bottom: var(--space-3);

    a {
      color: var(--color-text);
      text-decoration: none;
      &:hover { color: var(--color-primary); }
    }
  }

  &__excerpt {
    font-size: var(--font-size-sm);
    color: var(--color-text-muted);
    line-height: var(--line-height-relaxed);
    display: -webkit-box;
    -webkit-line-clamp: 3;
    -webkit-box-orient: vertical;
    overflow: hidden;
    margin-bottom: var(--space-4);
  }

  &__meta {
    display: flex;
    align-items: center;
    gap: var(--space-3);
    font-size: var(--font-size-xs);
    color: var(--color-text-muted);
  }
}
```

### 6.4 Komponen Wajib untuk Template Distribusi

Urutan prioritas pengerjaan:

1. ☐ **Head** (meta tags SEO, Open Graph, Google Fonts link)
2. ☐ **Header** (logo + nav + search + dark mode toggle)
3. ☐ **Hero/Featured Post** (post terbaru, full-width, eye-catching)
4. ☐ **Post Grid** (grid responsif 1→2→3 kolom)
5. ☐ **Post Card** (lihat anatomi di atas)
6. ☐ **Single Post Page** (typography, related posts, share buttons)
7. ☐ **Sidebar** (about widget, popular posts, labels, ads slot)
8. ☐ **Pagination** (prev/next + numbered)
9. ☐ **Footer** (links, copyright, back to top)
10. ☐ **404 Page**
11. ☐ **Search Results Page**

---

## 7. SEO & PERFORMA (WAJIB)

### 7.1 Meta Tags di `<head>`

```xml
<head>
  <meta charset='UTF-8'/>
  <meta name='viewport' content='width=device-width, initial-scale=1'/>

  <!-- Title & Description -->
  <b:if cond='data:blog.pageType == &quot;item&quot;'>
    <title><data:post.title/> - <data:blog.title/></title>
    <meta expr:content='data:post.snippet' name='description'/>
  <b:else/>
    <title><data:blog.title/></title>
    <meta expr:content='data:blog.metaDescription' name='description'/>
  </b:if>

  <!-- Canonical -->
  <b:if cond='data:blog.pageType == &quot;item&quot;'>
    <link expr:href='data:post.url' rel='canonical'/>
  <b:else/>
    <link expr:href='data:blog.canonicalUrl' rel='canonical'/>
  </b:if>

  <!-- Open Graph -->
  <meta content='website' property='og:type'/>
  <meta expr:content='data:blog.title' property='og:site_name'/>
  <b:if cond='data:blog.pageType == &quot;item&quot;'>
    <meta expr:content='data:post.title' property='og:title'/>
    <meta expr:content='data:post.snippet' property='og:description'/>
    <meta expr:content='data:post.url' property='og:url'/>
    <b:if cond='data:post.thumbnailUrl'>
      <meta expr:content='data:post.thumbnailUrl' property='og:image'/>
    </b:if>
  </b:if>

  <!-- Preconnect Google Fonts -->
  <link href='https://fonts.googleapis.com' rel='preconnect'/>
  <link crossorigin='' href='https://fonts.gstatic.com' rel='preconnect'/>
  <link href='https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;500;600;700&amp;family=Lora:ital,wght@0,400;0,700;1,400&amp;display=swap' rel='stylesheet'/>
</head>
```

### 7.2 Image Lazy Loading

```xml
{# Selalu pakai loading='lazy' dan aspect ratio yang jelas #}
<b:if cond='data:post.thumbnailUrl'>
  <div class='post-card__thumbnail'>
    <img
      expr:src='data:post.thumbnailUrl'
      expr:alt='data:post.title'
      loading='lazy'
      decoding='async'
      width='800'
      height='450'
    />
  </div>
</b:if>
```

### 7.3 Structured Data (JSON-LD)

```xml
<b:if cond='data:blog.pageType == &quot;item&quot;'>
  <script type='application/ld+json'>
  //<![CDATA[
  {
    "@context": "https://schema.org",
    "@type": "BlogPosting",
    "headline": "<data:post.title/>",
    "url": "<data:post.url/>",
    "datePublished": "<data:post.timestampISO8601/>",
    "author": {
      "@type": "Person",
      "name": "<data:post.author/>"
    },
    "publisher": {
      "@type": "Organization",
      "name": "<data:blog.title/>"
    }
  }
  //]]>
  </script>
</b:if>
```

---

## 8. DARK MODE IMPLEMENTATION

### 8.1 Pendekatan: CSS Custom Properties + `data-theme` attribute

```scss
// Semua warna via custom properties (sudah di _tokens.scss)
// Dark mode: dua cara — auto (system) + manual toggle

// Auto via prefers-color-scheme
@media (prefers-color-scheme: dark) {
  :root {
    --color-bg: #1a1a1a;
    // dst...
  }
}

// Manual via data-theme attribute
[data-theme="dark"] {
  --color-bg: #1a1a1a;
  // dst...
}

[data-theme="light"] {
  --color-bg: #ffffff;
  // dst...
}
```

```javascript
// Dark mode toggle
//<![CDATA[
(function() {
  const STORAGE_KEY = 'theme-preference';
  const root = document.documentElement;

  function getPreference() {
    return localStorage.getItem(STORAGE_KEY) ||
      (window.matchMedia('(prefers-color-scheme: dark)').matches ? 'dark' : 'light');
  }

  function setTheme(theme) {
    root.setAttribute('data-theme', theme);
    localStorage.setItem(STORAGE_KEY, theme);
  }

  // Apply immediately to prevent flash
  setTheme(getPreference());

  window.addEventListener('DOMContentLoaded', function() {
    const toggle = document.getElementById('theme-toggle');
    if (toggle) {
      toggle.addEventListener('click', function() {
        setTheme(root.getAttribute('data-theme') === 'dark' ? 'light' : 'dark');
      });
    }
  });
})();
//]]>
```

---

## 9. ATURAN UNTUK AI — CHECKLIST SEBELUM GENERATE

Sebelum AI generate kode apapun untuk project ini, pastikan:

### Blogger XML
- [ ] Semua attribute dinamis pakai `expr:` prefix
- [ ] Conditional pakai `<b:if>`, `<b:elseif>`, `<b:else>`, `<b:endif>`  
      *(Note: `</b:if>` bukan `<b:endif>`)*
- [ ] Loop pakai `<b:loop values='...' var='...'>`
- [ ] JavaScript selalu dibungkus `<![CDATA[` dan `]]>`
- [ ] CSS di `<b:skin>` selalu dibungkus `<![CDATA[`
- [ ] `&` ditulis sebagai `&amp;` di dalam attribute XML
- [ ] `"` ditulis sebagai `&quot;` di dalam `cond=` attribute
- [ ] Minimal ada 1 `<b:section>` di `<body>`

### Bloggerpack
- [ ] Pakai `{% template %}` bukan `{% include %}`
- [ ] Setiap file punya `<bp:template>` wrapper
- [ ] Asset inline pakai `{% asset %}` wrapper
- [ ] Data dari `theme-config.json` diakses via `{{ data.keyName }}`

### CSS
- [ ] Semua nilai warna pakai `var(--color-*)` — tidak ada hardcoded hex
- [ ] Semua nilai spacing pakai `var(--space-*)` — tidak ada hardcoded px arbitrary
- [ ] Font size pakai `var(--font-size-*)` — tidak ada hardcoded rem/px
- [ ] Responsive pakai CSS Grid dengan `auto-fill`/`auto-fit` dulu sebelum media query
- [ ] Gambar selalu punya `loading='lazy'` dan `aspect-ratio`
- [ ] Dark mode bekerja via `[data-theme]` attribute

### Design Quality
- [ ] Post card punya thumbnail, label, title (max 2 baris), excerpt (max 3 baris), meta
- [ ] Hover states smooth dengan `transition` pada semua elemen interaktif
- [ ] Typography hierarchy jelas: serif untuk heading, sans untuk body
- [ ] Mobile-first: komponen bekerja di lebar 320px
- [ ] Warna accessible: contrast ratio ≥ 4.5:1 untuk teks normal

---

## 10. COMMON MISTAKES — JANGAN DILAKUKAN

```xml
<!-- ❌ SALAH: href langsung tanpa expr: -->
<a href='data:post.url'>judul</a>

<!-- ✅ BENAR -->
<a expr:href='data:post.url'><data:post.title/></a>

<!-- ❌ SALAH: & di attribute -->
<a href='url?foo=1&bar=2'>link</a>

<!-- ✅ BENAR -->
<a href='url?foo=1&amp;bar=2'>link</a>

<!-- ❌ SALAH: include bawaan Nunjucks -->
{% include "components/header.xml" %}

<!-- ✅ BENAR -->
{% template "src/template/components/header.xml" %}

<!-- ❌ SALAH: hardcoded color -->
.btn { background: #1a73e8; }

<!-- ✅ BENAR -->
.btn { background: var(--color-primary); }

<!-- ❌ SALAH: JS tanpa CDATA -->
<script>alert('hello')</script>

<!-- ✅ BENAR -->
<script>//<![CDATA[
alert('hello');
//]]></script>

<!-- ❌ SALAH: gambar tanpa lazy loading -->
<img src='...' />

<!-- ✅ BENAR -->
<img expr:src='data:post.thumbnailUrl' loading='lazy' decoding='async'
     width='800' height='450' expr:alt='data:post.title'/>
```

---

## 11. PREMIUM vs FREE — FITUR BREAKDOWN

| Fitur | Free | Premium |
|-------|------|---------|
| Responsive layout | ✅ | ✅ |
| Dark mode | ✅ | ✅ |
| Basic SEO meta tags | ✅ | ✅ |
| 1 color scheme | ✅ | ✅ (10+ scheme) |
| Post grid layout | ✅ | ✅ |
| Magazine layout | ❌ | ✅ |
| Custom widgets (popular posts, related) | ❌ | ✅ |
| Mega menu | ❌ | ✅ |
| Ads placement (ready slot) | Basic | Advanced |
| Footer credit removal | ❌ | ✅ |
| Custom CSS via Theme Designer | Basic | Full |
| Priority support | ❌ | ✅ |
| Future updates | Limited | ✅ |

---

*File ini digunakan sebagai AI context untuk development Blogger template.
Update file ini setiap ada perubahan konvensi atau penambahan fitur.*

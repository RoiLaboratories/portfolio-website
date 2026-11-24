# CSS Selectors: Targeting Exactly the Elements You Mean

**Session length:** 3 hours, project-based
**Prior lessons covered:** Node.js, Express (learners also know HTML; no CSS experience is assumed)
**Tools:** a code editor, a modern browser with DevTools (Chrome, Edge, or Firefox), and optionally an Express server to serve the files
**Project thread:** **Selector Kitchen**, a single recipe page. The HTML is provided complete, and learners write **only selectors and rules** in `styles.css` to style it, one technique at a time.

> **Assumptions.**
> 1. **Code fences:** the brief asks for `tsx`/`ts`/`bash` fences, but CSS selectors are written in CSS and HTML, so this document uses `html`, `css`, `ts` (the small Express server), and `bash` fences. CSS has no type system, so "typed" examples don't apply. Every example is runnable by pasting it into `styles.css`.
> 2. Learners know HTML elements, attributes (`class`, `id`, `href`, `type`), and nesting. They have **not** seen CSS, so every term is defined on first use.
> 3. To keep results visible without teaching other topics, the lesson uses only a **tiny property vocabulary**: `color`, `background-color`, `font-weight`, `font-size`, `font-style`, `text-decoration`, `border`, `padding`, `outline`, `opacity`, and (for pseudo-elements) `content`. Each is explained in one line the first time it appears.
> 4. **React** is not taught yet. Where React fits (`className`, CSS Modules, Tailwind), a one-line **placeholder** names the lesson that covers it.
>
> **Not covered here (later lessons):** the **box model** (margin, padding, sizing in depth), **typography and colors**, **layout with Flexbox and Grid**, **responsive design and media queries**, **animations and transitions**, **CSS variables**, **cascade layers (`@layer`)**, and React styling approaches. Each is named once where it comes up.

---

## 1. Learning Objectives

By the end of this session, learners will be able to:

1. **Attach** a stylesheet to an HTML page, **write** rules with type, class, ID, and universal selectors, and **inspect** which rules apply using browser DevTools.
2. **Build** precise selectors using selector lists, the four combinators (descendant, child, adjacent sibling, general sibling), and attribute selectors.
3. **Apply** pseudo-classes (state, structure, form, and logical: `:hover`, `:nth-child()`, `:checked`, `:not()`, `:is()`, `:where()`, `:has()`) and pseudo-elements (`::before`, `::after`, `::marker`, `::selection`, `::placeholder`).
4. **Predict** which rule wins a conflict by calculating specificity, applying source order, and reasoning about inheritance, and **debug** "my style isn't applying" problems.
5. **Refactor** brittle, over-specific selectors into flat, low-specificity, maintainable ones.

---

## 2. Prerequisite Check

Learners must already be able to:

- Write a valid HTML page with headings, paragraphs, lists, links, forms, and tables.
- Use HTML attributes (`class`, `id`, `href`, `type`, `required`, `disabled`, `data-*`) and understand parent, child, and sibling relationships between elements.
- Create files and folders, and link to a file by relative path.
- Open DevTools in a browser (right-click → **Inspect**).
- *(Optional bridge from earlier lessons)* Serve a folder of static files with `express.static`.

**Where this fits:** This is the first CSS lesson and the foundation of everything visual. It comes before the box model, typography, Flexbox/Grid, and responsive design, and before the React styling lessons (`className`, CSS Modules, Tailwind), all of which depend on knowing how selectors choose elements.

---

## 3. Section Breakdown

### 3.1 CSS Basics: Rules, Stylesheets, and DevTools

**Explanation**
**CSS** (Cascading Style Sheets) is the language that controls how HTML looks. A **stylesheet** is a file (or block) of **rules**. Each rule has two parts:

```css
selector {
  property: value;
}
```

- The **selector** says **which elements** the rule applies to.
- The **declaration block** (inside `{ }`) holds one or more **declarations**, each a `property: value;` pair. A **property** is *what* to change (for example `color`), and the **value** is *how* (for example `#b45309`).

This lesson is about the **selector** half. The rest of the rule is deliberately simple.

There are three ways to attach CSS to HTML:

| Way | Example | Use it? |
|---|---|---|
| **External stylesheet** | `<link rel="stylesheet" href="styles.css">` | **Yes.** The standard way. |
| **Internal** | a `<style>` element in `<head>` | Quick experiments only |
| **Inline** | `style="color: red"` on an element | Avoid. It can't use selectors, and it overrides almost everything |

**DevTools** are the browser's built-in inspection tools. Right-click an element → **Inspect**, then use the **Styles** pane to see every rule that matches the selected element, which ones are crossed out (overridden), and to edit values live. You'll use this all lesson.

**The Selector Kitchen starter.** Create this folder structure and paste the HTML:

```text
selector-kitchen/
├── public/
│   ├── index.html
│   └── styles.css      (start with an empty file)
└── server.ts           (optional, see below)
```

```html
<!-- public/index.html -->
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Selector Kitchen</title>
  <link rel="stylesheet" href="styles.css" />
</head>
<body>
  <header id="site-header">
    <h1>Selector Kitchen</h1>
    <nav class="main-nav">
      <a href="/" class="active">Home</a>
      <a href="/recipes.html">Recipes</a>
      <a href="https://example.com/about" target="_blank" rel="noopener">About</a>
      <a href="/downloads/menu.pdf">Menu (PDF)</a>
    </nav>
  </header>

  <main>
    <article class="recipe featured" id="jollof">
      <h2>Party Jollof Rice</h2>
      <p class="meta">By Ada · <span class="tag">Nigerian</span> <span class="tag vegan">Vegan option</span></p>
      <p>A smoky, tomato-rich rice dish served at celebrations.</p>

      <h3>Ingredients</h3>
      <ul class="ingredients">
        <li>2 cups long-grain rice</li>
        <li>4 ripe tomatoes</li>
        <li>2 red bell peppers</li>
        <li class="optional">1 scotch bonnet (optional)</li>
        <li>Spice mix
          <ul>
            <li>1 tsp thyme</li>
            <li>1 tsp curry powder</li>
          </ul>
        </li>
      </ul>

      <h3>Steps</h3>
      <ol class="steps">
        <li>Blend tomatoes, peppers, and onion.</li>
        <li>Fry the blend until the water cooks off.</li>
        <li>Add rice and stock, then simmer covered.</li>
      </ol>
      <p class="note">Tip: let the rice rest for 10 minutes.</p>
    </article>

    <aside class="tips">
      <h2>Kitchen tips</h2>
      <p>Use <a href="#jollof">the recipe above</a> as a base.</p>
      <p class="note warning">Never leave hot oil unattended.</p>
    </aside>

    <section class="comments">
      <h2>Leave a comment</h2>
      <form>
        <label for="name">Name</label>
        <input id="name" name="name" type="text" required placeholder="Your name" />

        <label for="email">Email</label>
        <input id="email" name="email" type="email" required placeholder="you@example.com" />

        <input type="checkbox" id="subscribe" name="subscribe" checked />
        <label for="subscribe">Email me replies</label>

        <input type="text" name="promo" value="Locked" disabled />
        <button type="submit">Post</button>
      </form>
    </section>

    <table class="nutrition">
      <caption>Nutrition per serving</caption>
      <thead><tr><th>Nutrient</th><th>Amount</th></tr></thead>
      <tbody>
        <tr><td>Calories</td><td>420</td></tr>
        <tr><td>Protein</td><td>9 g</td></tr>
        <tr><td>Fat</td><td>12 g</td></tr>
        <tr><td>Fibre</td><td>4 g</td></tr>
      </tbody>
    </table>
  </main>

  <footer>
    <p>© Selector Kitchen</p>
    <a href="mailto:hello@example.com">Contact us</a>
  </footer>
</body>
</html>
```

You can simply double-click `index.html`. If you'd like to reuse your Express skills, serve the folder instead (this is the *only* server code in the lesson):

```ts
// server.ts
import express from "express";

const app = express();
app.use(express.static("public")); // serve everything in ./public
app.listen(3000, () => console.log("http://localhost:3000"));
```

```bash
npm init -y
npm install express
npm install -D tsx typescript @types/express @types/node
npx tsx server.ts   # run from the selector-kitchen folder, then open http://localhost:3000
```

Your first rules, in `public/styles.css`:

```css
body {
  font-family: system-ui, sans-serif;
  color: #1f2933;
  background-color: #fafaf9;
}

h1 {
  color: #b45309;
}
```

Line by line:
- `body { … }`: the selector `body` targets the `<body>` element. Because `color` and `font-family` are **inherited** (3.8), almost everything inside `<body>` picks them up.
- `color` sets the **text color**, `background-color` sets the **background fill**, and `font-family` picks the font.
- Each declaration ends with a **semicolon**, and each block closes with `}`.
- `h1 { color: … }` targets every `<h1>`. It's a **type selector** (3.2).
- CSS comments are `/* … */`. (`//` is **not** a CSS comment.)

> **Important:** CSS never throws errors. A typo or an unknown property is **silently ignored**, so the page just doesn't change. DevTools is how you find out why.

**Why it matters**
Every web interface, whether hand-written CSS, a CSS framework, or a React app, ultimately works by selecting elements and applying declarations. If you can't reason about selectors, styles feel random, and fixing one thing breaks another.

> **React bridge (placeholder):** In React you'll write `className="…"` instead of `class="…"` in JSX, and import a CSS file into a component. That's covered in the **React styling** lesson. Everything you learn today applies unchanged.

**Common beginner mistakes**
- **Mistake:** A wrong `href` path to the stylesheet.
  **Symptom:** No styling at all. DevTools → **Network** tab shows `styles.css` as `404`.
  **Fix:** Check the path relative to the HTML file, and the folder your server serves.
- **Mistake:** A missing `}` or `;`.
  **Symptom:** The broken rule *and often the next rule* stop working, with no error.
  **Fix:** In DevTools' Styles pane, an invalid declaration shows with a warning icon or strike-through, and your editor's syntax highlighting should flag unmatched braces.
- **Mistake:** Forgetting `rel="stylesheet"` on the `<link>`.
  **Symptom:** The file loads but is never applied.
  **Fix:** Add `rel="stylesheet"`.
- **Mistake:** Editing CSS and seeing no change.
  **Symptom:** The old styles persist.
  **Fix:** Hard refresh (`Ctrl/Cmd + Shift + R`), because the browser may have cached the file.
- **Mistake:** Using `//` for comments.
  **Symptom:** The next rule breaks.
  **Fix:** `/* comment */`.
- **Mistake (Express bridge):** Opening `http://localhost:3000` and seeing `Cannot GET /`.
  **Symptom:** The server isn't serving the right folder.
  **Fix:** Run the command from the project root so `public/` resolves correctly.

**Try it (3 min)**
Right-click the `<h1>` → **Inspect**. In the Styles pane, change the `color` value live, then un-tick the checkbox beside it. Then deliberately delete a `;` in `styles.css` and watch what stops working.

**Project step: Milestone 1 (about 5 min)**
Set up the folder, paste the HTML, create `styles.css`, link it, and style `body` and `h1`. Confirm the heading changes color.

---

### 3.2 Type, Class, ID, and Universal Selectors

**Explanation**
These four are the **basic selectors**:

| Selector | Syntax | Matches | Example |
|---|---|---|---|
| **Type** (element) | `name` | every element of that tag | `h2` |
| **Class** | `.name` | every element whose `class` includes `name` | `.tag` |
| **ID** | `#name` | the one element with that `id` | `#site-header` |
| **Universal** | `*` | every element | `*` |

A **compound selector** joins simple selectors with **no spaces**, and the element must satisfy **all** of them: `p.note` is "a `<p>` that also has class `note`", and `.tag.vegan` is "an element with both classes."

```css
/* type: every <h2> on the page */
h2 {
  color: #7c2d12;
}

/* class: any element with class="tag" */
.tag {
  background-color: #fde68a;
  padding: 2px 8px;
}

/* ID: exactly the element with id="site-header" */
#site-header {
  background-color: #fff7ed;
  padding: 16px;
}

/* multiple classes: the element needs BOTH */
.tag.vegan {
  background-color: #bbf7d0;
}

/* type + class: only <p> elements that have class="note" */
p.note {
  border: 1px solid #d6d3d1;
  padding: 8px;
}
```

Line by line:
- `padding` adds space **inside** an element, between its content and its edge. `border: 1px solid #d6d3d1` draws a 1-pixel solid line in that color. (Both are explained properly in the **Box Model** lesson.)
- `.tag` is applied to **both** `<span class="tag">` elements. `.tag.vegan` matches only the second, because it has both classes, and it **adds** a more specific green over the yellow.
- `p.note` matches the tip in the recipe **and** the warning in the aside, because both are `<p>` elements with class `note`. The warning's *extra* `warning` class doesn't stop it from matching.
- `#site-header` matches the single `<header id="site-header">`.

**The universal selector as a debugging trick.** Temporarily draw an outline around *everything* to see element boundaries:

```css
* {
  outline: 1px solid rgba(220, 38, 38, 0.4); /* remove when you're done */
}
```

`outline` draws a line **outside** an element's border without affecting layout, which makes it ideal for debugging.

**Class or ID?** Prefer **classes** for styling. A class can be reused on many elements, and an element can have several. An `id` must be **unique** on the page and carries **very high specificity** (3.8), which makes rules hard to override. Use IDs for fragment links (`href="#jollof"`) and JavaScript hooks, not for styling. Name classes by **role** (`.warning`), not appearance (`.red-text`), so a design change doesn't make the name a lie.

**Why it matters**
Classes are the workhorse selector of professional CSS. Nearly every real stylesheet and every framework is built from them.

> **React bridge (placeholder):** In JSX, `class` becomes `className`, and **CSS Modules** generate unique class names so two components can both use `.title` without clashing, both covered in the **React styling** lesson.

**Common beginner mistakes**
- **Mistake:** Forgetting the dot: `tag { … }`.
  **Symptom:** Nothing happens. The selector looks for an element named `<tag>`, which doesn't exist.
  **Fix:** `.tag` for classes, `#id` for IDs.
- **Mistake:** `.tag vegan` (with a space) when you mean both classes.
  **Symptom:** No match. The space means "a `vegan` **element** inside `.tag`" (3.3).
  **Fix:** No space: `.tag.vegan`.
- **Mistake:** Mixing up `.` and `#`.
  **Symptom:** The rule never applies.
  **Fix:** Check the HTML: `class="…"` → `.`, `id="…"` → `#`.
- **Mistake:** Reusing the same `id` on several elements.
  **Symptom:** Inconsistent behavior, and invalid HTML.
  **Fix:** Use a class instead.
- **Mistake:** Class names that start with a digit (`.1col`) or contain spaces.
  **Symptom:** The selector is invalid and ignored.
  **Fix:** Start with a letter, and use hyphens (`.col-1`).
- **Mistake:** Wrong capitalization (`.Tag` vs `class="tag"`).
  **Symptom:** No match, because class and ID values are **case-sensitive** (HTML tag names are not).
  **Fix:** Match the HTML exactly. A lowercase-with-hyphens convention avoids this.

**Try it (4 min)**
Make all `<h3>` elements italic by adding `font-style: italic;`, then give only `.tag.vegan` a bold weight with `font-weight: 700;`. Verify in DevTools that the plain `.tag` is *not* bold.

**Project step: Milestone 2 (about 15 min)**
Style `h2`, `.tag`, `.tag.vegan`, `#site-header`, and `p.note`, as above.

---

### 3.3 Combinators and Selector Lists

**Explanation**
A **combinator** links two selectors by the **relationship** between the elements they match. The element that finally gets styled is the **subject**, which is the **rightmost** part of the selector.

| Name | Syntax | Meaning | Example |
|---|---|---|---|
| **Selector list** | `A, B` | A **or** B (same rule, shorter) | `h2, h3` |
| **Descendant** | `A B` (a space) | B anywhere **inside** A | `.main-nav a` |
| **Child** | `A > B` | B is a **direct child** of A | `.ingredients > li` |
| **Adjacent sibling** | `A + B` | B is the element **immediately after** A (same parent) | `h2 + p` |
| **General sibling** | `A ~ B` | B is **any later sibling** of A | `h3 ~ p` |

```css
/* selector list: one rule, several selectors */
h2,
h3 {
  color: #7c2d12;
}

/* descendant: every <a> anywhere inside .main-nav */
.main-nav a {
  color: #78350f;
  text-decoration: none;   /* removes the default underline */
}

/* child: only <li> elements directly inside .ingredients */
.ingredients > li {
  border-bottom: 1px dotted #a8a29e;
}

/* adjacent sibling: the <p> right after an <h2> */
h2 + p {
  font-weight: 600;
}

/* general sibling: every <p> after an <h3> (same parent) */
h3 ~ p {
  font-style: italic;
}
```

Line by line:
- `.ingredients > li` matches the five top-level ingredients but **not** the two nested `<li>` items inside "Spice mix", because those are children of the inner `<ul>`, not of `.ingredients`. Change it to `.ingredients li` (descendant) and the nested items match too. This is the difference between `>` and a space.
- `h2 + p` hits `<p class="meta">` (right after the recipe `<h2>`) and the first paragraph in the aside, but **not** the form section: its `<h2>` is followed by a `<form>`, not a `<p>`.
- `h3 ~ p` matches `p.note` (a later sibling of the `<h3>` headings) but **not** the description paragraph, because that comes *before* the `<h3>`. `~` only looks **forward**.
- `text-decoration: none` removes underlines, and `font-style: italic` slants text.

**How browsers read selectors:** from **right to left**. For `.main-nav a`, the browser considers each `<a>` and asks "is an ancestor `.main-nav`?" That's why the rightmost part is the subject.

**Why it matters**
Combinators let you style by **structure and context** (links *in the nav* look different from links *in the footer*) without adding a class to every element.

> **Placeholder (later lesson: layout):** Combinators style *appearance*. Positioning things (side-by-side columns, centering) is **Flexbox and Grid**, which has its own lessons.

**Common beginner mistakes**
- **Mistake:** Using a descendant selector when you meant only direct children (`ul li` or `.ingredients li`).
  **Symptom:** Nested items get styled too.
  **Fix:** Use `>` for direct children.
- **Mistake:** Expecting `~` or `+` to look backwards.
  **Symptom:** Nothing matches. CSS siblings combinators only select **later** siblings.
  **Fix:** Restructure, or use `:has()` (3.6).
- **Mistake:** An invalid selector inside a selector list: `h2, h3, ..bad { … }`.
  **Symptom:** The **whole rule** is dropped, including the valid parts. (A normal selector list is all-or-nothing.)
  **Fix:** Fix the typo, and check in DevTools whether the rule appears at all.
- **Mistake:** Long chains like `main article ul li a`.
  **Symptom:** The style breaks whenever the HTML structure changes, and the selector is hard to override.
  **Fix:** Prefer a class (3.9).
- **Mistake:** Trying to select a **parent** by its child (`li < ul`).
  **Symptom:** There is no such combinator.
  **Fix:** `:has()` (3.6).

**Try it (4 min)**
Change `.ingredients > li` to `.ingredients li` and see the nested "thyme" and "curry powder" items gain a border. Change it back and explain why they lose it.

**Project step: Milestone 3 (about 15 min)**
Style the nav links with a descendant selector, ingredients with a child selector, and use `h2 + p` and `h3 ~ p` as above.

---

### 3.4 Attribute Selectors

**Explanation**
An **attribute selector** matches elements by the **presence or value of an HTML attribute**, written in square brackets. It lets you style without adding a class.

| Syntax | Matches | Example |
|---|---|---|
| `[attr]` | the attribute exists | `[required]` |
| `[attr="value"]` | exact value | `input[type="email"]` |
| `[attr^="value"]` | value **starts with** | `a[href^="https"]` |
| `[attr$="value"]` | value **ends with** | `a[href$=".pdf"]` |
| `[attr*="value"]` | value **contains** | `a[href*="example"]` |
| `[attr~="value"]` | one of the **space-separated words** | `[class~="tag"]` |
| `[attr\|="value"]` | exactly the value, or the value followed by `-` | `[lang\|="en"]` (matches `en` and `en-US`) |

Add a space and `i` before the closing bracket to ignore **letter case** in the value: `[type="EMAIL" i]`.

```css
/* external links: start with http */
a[href^="http"] {
  color: #1d4ed8;
}

/* links to PDF files */
a[href$=".pdf"] {
  font-weight: 700;
}

/* email links */
a[href^="mailto:"] {
  text-decoration: underline;
}

/* links that open in a new tab */
a[target="_blank"] {
  background-color: #eff6ff;
}

/* form fields by type, and by presence of an attribute */
input[type="email"] {
  border: 1px solid #1d4ed8;
}

[required] {
  border-color: #b45309;
}
```

Line by line:
- `a[href^="http"]` matches "About" (`https://example.com/about`), but not "Home" (`/`) or the `mailto:` link.
- `a[href$=".pdf"]` matches the menu link by its ending. The **quotes are required** here because `.pdf` isn't a valid bare identifier.
- `[required]` has no element name, so it matches *any* element with a `required` attribute (the name and email inputs). Adding the element (`input[required]`) makes it clearer and slightly more specific.
- `border-color` changes only a border's color.

**Why it matters**
Attribute selectors style by **meaning** already in your HTML (link destinations, input types, `data-*` flags, `aria-*` states), which is how components get state-based styling like `[aria-expanded="true"]` or `[data-state="open"]`.

> **React bridge (placeholder):** In React you'll often drive styling with attributes such as `data-state` or `aria-*`, covered in the **React styling** and **Accessibility** lessons.

**Common beginner mistakes**
- **Mistake:** Leaving out quotes around values with special characters: `a[href$=.pdf]`.
  **Symptom:** The selector is invalid, so the **rule is ignored**.
  **Fix:** Quote values: `a[href$=".pdf"]`.
- **Mistake:** Using `=` when you want "starts with": `a[href="http"]`.
  **Symptom:** No match, because `=` needs the *exact* full value.
  **Fix:** `^=` (starts with), `$=` (ends with), `*=` (contains).
- **Mistake:** `*=` that is too broad: `a[href*="a"]`.
  **Symptom:** Matches far more than intended.
  **Fix:** Use the most specific operator you can.
- **Mistake:** Forgetting `^` and `$` go **before** `=`.
  **Symptom:** `[href=^"http"]` is invalid.
  **Fix:** `[href^="http"]`.
- **Mistake:** Expecting case-insensitive matching for values by default.
  **Symptom:** `[data-state="Open"]` doesn't match `open`.
  **Fix:** Match exactly, or add the ` i` flag.

**Try it (4 min)**
Style internal links (`a[href^="/"]`) in dark green, then confirm in DevTools that "Home" and "Recipes" match but "About" doesn't.

**Project step: Milestone 4 (about 10 min)**
Add the external, PDF, mailto, `target`, email-input, and `[required]` rules above.

---

### 3.5 Pseudo-Classes: State and Structure

**Explanation**
A **pseudo-class** (written with **one colon**) selects an element that's in a certain **state** or **position**, something not visible as a class or attribute in your HTML. It attaches to a selector: `a:hover`.

**User-action states**

| Pseudo-class | Matches |
|---|---|
| `:hover` | the pointer is over the element |
| `:focus` | the element has keyboard focus |
| `:focus-visible` | focus **that should show a focus ring** (keyboard navigation) |
| `:active` | the element is being pressed |

**Structural (position among siblings)**

| Pseudo-class | Matches |
|---|---|
| `:first-child` / `:last-child` | the first / last child of its parent |
| `:nth-child(n)` | the nth child (counting **all** siblings). `n` can be a number, `odd`, `even`, or a formula such as `2n+1`, `-n+3` |
| `:nth-of-type(n)` | the nth sibling **of the same element type** |
| `:only-child` | an element that's the only child |

**Form states**

| Pseudo-class | Matches |
|---|---|
| `:checked` | a checked checkbox/radio |
| `:disabled` / `:enabled` | disabled / enabled controls |
| `:required` / `:optional` | fields with / without `required` |
| `:placeholder-shown` | an empty field currently showing its placeholder |
| `:invalid` / `:valid` | fields failing / passing validation |

```css
/* user action */
a:hover {
  text-decoration: underline;
}

a:focus-visible {
  outline: 3px solid #2563eb;   /* keyboard focus ring: keep it visible! */
}

button:active {
  opacity: 0.8;                 /* opacity: 1 = solid, 0 = invisible */
}

/* structural */
.ingredients > li:first-child {
  font-weight: 700;
}

.steps li:nth-child(even) {
  background-color: #f5f5f4;
}

tbody tr:nth-child(odd) {
  background-color: #fff7ed;
}

p:nth-child(2) {
  color: #78716c;                /* the 2nd CHILD, but only if it's a <p> */
}

/* forms */
input:required {
  border: 1px solid #b45309;
}

input:disabled {
  background-color: #e7e5e4;
}

input:checked + label {
  font-weight: 700;              /* label right after a checked box */
}

input:invalid {
  border-color: #dc2626;
}
```

Line by line:
- `a:focus-visible` shows a ring only when focus came from the **keyboard**, which is the accessible default. Don't remove focus outlines without replacing them.
- `.steps li:nth-child(even)` stripes every second step. `tbody tr:nth-child(odd)` stripes table rows.
- **`nth-child` vs `nth-of-type`:** `p:nth-child(2)` means "an element that is the **second child overall** *and* is a `<p>`." In the article, the second child is `p.meta`, so that's what matches. `p:nth-of-type(2)` means "the second `<p>` among its siblings," which is the **description paragraph**. They answer different questions.
- `input:checked + label` combines a pseudo-class with the adjacent sibling combinator (3.3): the label immediately after a checked checkbox becomes bold. This works because the HTML places the `<label>` right after the `<input>`.
- `input:invalid` marks the empty required fields **red immediately on page load**, which is annoying UX. Browsers now offer `:user-invalid`, which only applies **after the user interacts**:

```css
input:user-invalid {
  border-color: #dc2626;
}
```

> **Version note:** `:focus-visible` is widely supported. `:user-invalid`/`:user-valid` are newer, so verify their support on MDN's Baseline/compatibility tables for your target browsers. Browsers also restrict what `:visited` can change (mostly color) for privacy, so don't rely on it for layout or anything complex.

**Link state order (LVHA).** When styling links with several states, order matters because the rules have **equal specificity** (3.8): `a:link`, then `a:visited`, then `a:hover`, then `a:active`. Later rules win ties, so putting `:hover` before `:link` would make hover unable to apply.

**Why it matters**
Hover, focus, and form states make interfaces feel alive and accessible. Structural pseudo-classes let you style lists, tables, and grids ("every second row," "the last item") without touching the HTML.

> **React bridge (placeholder):** These states map directly to Tailwind's variants (`hover:`, `focus-visible:`, `odd:`, `disabled:`), covered in the **Tailwind** lesson.

**Common beginner mistakes**
- **Mistake:** Hiding important information behind `:hover` only.
  **Symptom:** Touch devices and keyboard users never see it (touchscreens have no hover).
  **Fix:** Treat hover as decoration, and provide a focus and touch equivalent.
- **Mistake:** `outline: none` with no replacement.
  **Symptom:** Keyboard users can't see where they are.
  **Fix:** Style `:focus-visible` instead of removing focus indication.
- **Mistake:** Mixing up `nth-child` and `nth-of-type`.
  **Symptom:** The "wrong" element is styled, or nothing is.
  **Fix:** `nth-child` counts **every** sibling, so use `nth-of-type` if other element types are mixed in.
- **Mistake:** `li:last-child` without scoping, when lists are nested.
  **Symptom:** It also matches the last item of the **nested** list.
  **Fix:** Scope it: `.ingredients > li:last-child`.
- **Mistake:** Writing `:nth-child(0)`, or off-by-one counting.
  **Symptom:** No match. `nth-child` counts from **1**.
  **Fix:** Use `1`, `2`, … or formulas like `2n`.
- **Mistake:** Putting `:hover` rules *before* a same-specificity base rule.
  **Symptom:** The hover style never appears.
  **Fix:** Order: base rule first, then `:hover`, then `:active`.

**Try it (4 min)**
Use `:nth-child(-n+3)` on `tbody tr` to style the first three table rows, then try `:nth-child(2n)` and compare it with `even`.

**Project step: Milestone 5 (about 20 min)**
Add hover and `:focus-visible` for links, striping for steps and table rows, `:first-child` on ingredients, and the form state rules. Tab through the page with your keyboard to check focus rings.

---

### 3.6 Logical Pseudo-Classes: `:not()`, `:is()`, `:where()`, and `:has()`

**Explanation**
These **functional pseudo-classes** take selectors as arguments and let you combine, exclude, and test relationships.

| Pseudo-class | Meaning | Specificity |
|---|---|---|
| `:not(S)` | elements that do **not** match `S` | that of its most specific argument |
| `:is(S1, S2, …)` | elements matching **any** of the selectors (a shorthand for repetition) | that of its most specific argument |
| `:where(S1, S2, …)` | same as `:is()` | **always zero** |
| `:has(S)` | elements that **contain / are followed by** something matching `S` (the **parent selector**) | that of its most specific argument |

```css
/* :not() — everything except */
a:not([href^="http"]) {
  color: #78350f;                 /* internal and mailto links */
}

.ingredients > li:not(.optional) {
  font-weight: 600;
}

/* :is() — shorthand for repeating a long prefix */
.recipe :is(h2, h3) {
  color: #7c2d12;                 /* same as ".recipe h2, .recipe h3" */
}

/* :where() — same matching, but ZERO specificity (easy to override later) */
:where(ul, ol) {
  padding: 4px 24px;
}

/* :has() — style a parent based on what it contains */
.recipe:has(.vegan) {
  border: 2px solid #86efac;      /* the recipe card contains a vegan tag */
}

li:has(ul) {
  font-weight: 700;               /* list items that contain a nested list */
}

label:has(+ input:required) {
  color: #b45309;                 /* labels followed by a required field */
}

form:has(:invalid) button {
  opacity: 0.6;                   /* the button when ANY field in the form is invalid */
}
```

Line by line:
- `a:not([href^="http"])` reads "any link that is **not** external." You can pass a comma-separated list: `:not(.a, .b)` means "neither `.a` nor `.b`."
- `.recipe :is(h2, h3)` expands to two selectors, so you don't repeat `.recipe`.
- `:where()` matches exactly like `:is()`, but contributes **no specificity**, so any simple later rule overrides it. It's ideal for **default styles** you expect to be overridden.
- `:has()` finally lets CSS select a **parent** (and also **previous siblings**) based on its descendants or following siblings. `label:has(+ input:required)` uses a **relative selector** that starts with the combinator `+`.
- Inside `:is()` and `:where()`, selector lists are **forgiving**. An invalid entry is ignored instead of invalidating the whole rule.

> **Version note:** `:is()`, `:where()`, and `:not()` with a selector list are widely supported. `:has()` is a newer feature that became available across all major browsers around late 2023, so check MDN's Baseline status for your audience. Complex selectors inside `:not()` and `:has()` inside `:has()` have restrictions. Verify details before relying on them in production.

**Why it matters**
`:not()` and `:is()` remove repetition, `:where()` makes defaults easy to override, and `:has()` replaces many cases that used to require JavaScript or extra classes.

**Common beginner mistakes**
- **Mistake:** Assuming `:is()` is "free" for specificity.
  **Symptom:** A rule with `:is(#a, .b)` is as strong as an ID selector, so it's hard to override.
  **Fix:** Use `:where()` when you want zero specificity.
- **Mistake:** Over-broad `:has()` such as `body:has(...)` or deeply nested `:has()`.
  **Symptom:** Surprising matches, and possible performance cost on huge pages.
  **Fix:** Keep `:has()` anchored on a specific element.
- **Mistake:** Writing `:not(.a) .b` and expecting "`.b` not inside `.a`."
  **Symptom:** It matches `.b` inside **any** ancestor that isn't `.a`, which is almost everything.
  **Fix:** Rethink the condition. `:not()` tests the element itself, not its ancestry.
- **Mistake:** Forgetting that `:has(+ …)`/`:has(~ …)` look at **following** siblings.
  **Symptom:** Wrong labels highlighted.
  **Fix:** Draw the HTML order.

**Try it (4 min)**
Give every `li` that contains a nested list (`li:has(ul)`) a bold weight, then in DevTools confirm only "Spice mix" is bold.

**Project step: Milestone 6 (about 15 min)**
Add the `:not()`, `:is()`, `:where()`, and four `:has()` rules above.

---

### 3.7 Pseudo-Elements

**Explanation**
A **pseudo-element** (written with **two colons**) targets a **part** of an element, or **generated content** that doesn't exist in your HTML, rather than a whole element. Don't confuse it with a pseudo-class: a pseudo-class is a *state*, and a pseudo-element is a *part*.

| Pseudo-element | Targets |
|---|---|
| `::before` / `::after` | generated content inserted **before / after** an element's content |
| `::first-letter` / `::first-line` | the first letter / first line of a block of text |
| `::marker` | the bullet or number of a list item |
| `::selection` | text the user has highlighted |
| `::placeholder` | the placeholder text of an input |

`::before` and `::after` do nothing unless you set the **`content`** property (even an empty string `""`).

```css
/* generated content: a small arrow after external links */
a[href^="http"]::after {
  content: " ↗";
}

/* a decorative mark before optional ingredients */
.optional::before {
  content: "✦ ";
  color: #b45309;
}

/* a required-field asterisk after the label */
label:has(+ input:required)::after {
  content: " *";
  color: #dc2626;
}

/* styling parts of existing content */
.steps li::marker {
  color: #b45309;
  font-weight: 700;
}

.recipe > p:nth-of-type(2)::first-letter {
  font-size: 1.6em;
  font-weight: 700;
}

::selection {
  background-color: #fde68a;
}

input::placeholder {
  color: #78716c;
}
```

Line by line:
- `content: " ↗"` inserts text the browser draws **after** each external link. It isn't in the HTML, and the user **can't select or copy it** in most browsers.
- `::marker` styles the step numbers (`1.`, `2.`, `3.`) separately from the step text.
- `::first-letter` works on **block-level** text containers like a `<p>`, which is why we target the description paragraph.
- A selector with a pseudo-element is the **last** part: `label:has(+ input:required)::after` first finds the labels, then targets the content after them.
- `font-size: 1.6em` makes text 1.6× the size of the surrounding text.

**Single versus double colon:** The CSS3 syntax is `::before`. Older single-colon forms (`:before`) still work for the four legacy pseudo-elements, but use `::` for clarity and consistency.

**Why it matters**
Pseudo-elements add polish (icons, decorative marks, drop caps, custom list markers) without extra HTML elements, keeping your markup clean.

**Common beginner mistakes**
- **Mistake:** Forgetting `content` on `::before`/`::after`.
  **Symptom:** Nothing appears.
  **Fix:** `content: ""` (even if empty).
- **Mistake:** Putting **important text** in `content`.
  **Symptom:** It can't be selected, searched, or reliably read by assistive technology.
  **Fix:** Use pseudo-elements for **decoration** only, and keep real content in HTML.
- **Mistake:** Using `::before` on elements that can't have children, such as `<img>` or `<input>`.
  **Symptom:** No effect.
  **Fix:** Add it to a wrapper element or the label.
- **Mistake:** Writing `a:hover::after` as `a::after:hover`.
  **Symptom:** The selector is invalid.
  **Fix:** The pseudo-element always goes **last**.
- **Mistake:** Expecting `::first-letter` to work on inline elements like `<span>`.
  **Symptom:** No effect.
  **Fix:** Apply it to a block container such as `p`.
- **Mistake:** Trying to "select" a pseudo-element in HTML or find it in the Elements tree.
  **Symptom:** It isn't there. It exists only in CSS and the Styles pane.
  **Fix:** Look for `::before` and `::after` nodes in DevTools' Elements panel, which shows generated content.

**Try it (4 min)**
Add `a[href$=".pdf"]::before { content: "📄 "; }` and check that the arrow and the document icon appear in the right places. Then remove the `content` line and watch the icon vanish.

**Project step: Milestone 7 (about 10 min)**
Add the arrow, optional mark, required asterisk, `::marker`, `::first-letter`, `::selection`, and `::placeholder` rules.

---

### 3.8 Specificity, the Cascade, and Inheritance

**Explanation**
Sooner or later two rules will try to set the **same property** on the **same element**. The **cascade** is the algorithm that decides the winner. Simplified, it checks in this order:

1. **Importance:** a declaration marked `!important` beats a normal one.
2. **Inline styles** (the `style="…"` attribute) beat any selector.
3. *(Advanced: **cascade layers**, `@layer`, are a later topic that changes this order.)*
4. **Specificity:** the more **specific** selector wins.
5. **Source order:** if specificity is equal, the **later** rule wins.

**Specificity** is a score written as three numbers **(A, B, C)**:

| Column | Counts | Examples |
|---|---|---|
| **A** | **ID** selectors | `#jollof` |
| **B** | **class**, **attribute**, and **pseudo-class** selectors | `.note`, `[href]`, `:hover` |
| **C** | **type** selectors and **pseudo-elements** | `p`, `::after` |

The universal selector `*` and combinators count as zero. `:is()`, `:not()`, and `:has()` count as their most specific argument, and `:where()` counts as zero.

| Selector | Specificity |
|---|---|
| `p` | (0, 0, 1) |
| `.note` | (0, 1, 0) |
| `p.note` | (0, 1, 1) |
| `a:hover` | (0, 1, 1) |
| `.tag.vegan` | (0, 2, 0) |
| `#jollof` | (1, 0, 0) |
| `#jollof p.note` | (1, 1, 1) |
| `:where(.a)` | (0, 0, 0) |

**Compare column by column, left to right**, not as one big number: (1, 0, 0) beats (0, 11, 0), because an ID outranks any number of classes.

**A worked conflict.** Apply these to `<p class="note">` inside `#jollof`:

```css
p            { color: #1f2933; }   /* (0,0,1) */
.note        { color: #b45309; }   /* (0,1,0): beats p */
p.note       { color: #7c2d12; }   /* (0,1,1): beats .note */
#jollof .note { color: #1d4ed8; }  /* (1,1,0): wins */
```

The tip paragraph is **blue**. Now remove the last rule and the paragraph turns brown (`p.note`). The tied-specificity case:

```css
.note { color: red; }
.note { color: green; }   /* same specificity (0,1,0): the LATER rule wins → green */
```

**Inheritance.** Some properties **inherit**: an element takes its **parent's** value when no rule sets it. Text-related properties such as `color`, `font-family`, `font-size`, `font-weight`, and `line-height` inherit, while box-related ones (`border`, `padding`, `margin`, `background-color`) do **not**. That's why styling `body` changes all text, but putting a border on `body` doesn't put borders on everything inside. You can force behavior with the keywords `inherit` (take the parent's value), `initial` (the property's default), and `unset`.

A classic surprise: browsers apply their own default stylesheet, so **links have a built-in color**. Setting `body { color: … }` won't change them, because a rule aimed at `a` directly (even the browser's) beats an inherited value:

```css
a {
  color: inherit;   /* make links follow the surrounding text color */
}
```

**`!important`** forces a declaration to win, but it starts an arms race (you need a stronger `!important` to override it), so treat it as a last resort.

**Debugging workflow:**
1. Inspect the element in DevTools.
2. Read the **Styles** pane: matching rules are listed **highest priority first**, and overridden declarations are **struck through**.
3. Ask "which rule is beating mine, and why" (specificity or order?).
4. The **Computed** tab shows the final value and which rule it came from.

**Why it matters**
"Why isn't my style applying?" is the most common CSS question in the world, and the answer is nearly always specificity, source order, or inheritance. Knowing the rules replaces guessing.

> **Version note:** `@layer` (cascade layers) and `@scope` change or extend this order and are newer features. They're not taught today, so check MDN's Baseline status and the Cascade documentation before using them.

**Common beginner mistakes**
- **Mistake:** Adding `!important` as soon as a rule fails.
  **Symptom:** A stylesheet full of `!important` that can't be overridden cleanly.
  **Fix:** Find the competing rule in DevTools and lower its specificity, or raise yours *sensibly*.
- **Mistake:** Using IDs for styling.
  **Symptom:** (1,0,0) selectors that beat every class, so you need more IDs to override them.
  **Fix:** Use classes.
- **Mistake:** Over-qualifying selectors (`div.card ul li a.link`).
  **Symptom:** Very high specificity that's hard to override.
  **Fix:** One class: `.card-link`.
- **Mistake:** Thinking selector order in the *HTML attribute* matters (`class="b a"`).
  **Symptom:** Confusion about which class "wins."
  **Fix:** The **stylesheet's** rule order and specificity decide, not the attribute order.
- **Mistake:** Putting a generic rule *after* a specific one of equal weight.
  **Symptom:** The specific style disappears.
  **Fix:** Order rules from general to specific.
- **Mistake:** Expecting a border or padding on a parent to apply to children.
  **Symptom:** Nothing happens.
  **Fix:** Those properties don't inherit.
- **Mistake:** Expecting `body { color }` to recolor links.
  **Symptom:** Links stay blue.
  **Fix:** Style `a` directly or use `color: inherit`.

**Try it (5 min)**
Add all four conflicting `p`/`.note` rules and predict the color of **the tip** (inside `#jollof`) and **the warning** in the aside (which also has class `note` but isn't inside `#jollof`). Then check in DevTools. Why do they differ?

**Project step: Milestone 8 (about 15 min)**
Add the conflict rules, then use DevTools to find the strike-through declarations and write one line per rule explaining why it lost.

---

### 3.9 Writing Maintainable Selectors (and Where This Goes Next)

**Explanation**
Good selectors are **flat, class-based, and low in specificity**, so they're easy to read, override, and reuse.

Guidelines:
- **Style with classes**, not IDs or deep structure.
- **Keep specificity low and similar** across rules, so source order, not escalation, resolves conflicts.
- **Name by role**: `.warning`, not `.red-box`.
- **Avoid long chains** that depend on the HTML structure.
- **Don't over-qualify**: `.nav-link`, not `ul.nav li a.nav-link`.
- Use a naming convention such as **BEM** (Block, Element, Modifier): `.recipe` (block), `.recipe__title` (element), `.recipe--featured` (modifier).

**Before and after:**

```css
/* BEFORE: brittle, high specificity (1,2,3), breaks if the HTML structure changes */
div#site-header nav.main-nav ul li a.link {
  color: #78350f;
}
```

```css
/* AFTER: flat, one class (0,1,0), reusable anywhere */
.nav-link {
  color: #78350f;
}
```

**Native CSS nesting** lets you write related rules together without repeating the parent selector. The `&` stands for the parent selector:

```css
.recipe {
  border: 1px solid #d6d3d1;
  padding: 16px;

  & h2 {                 /* same as ".recipe h2" */
    color: #7c2d12;
  }

  &.featured {           /* same as ".recipe.featured" */
    border-color: #b45309;
  }

  &:hover {              /* same as ".recipe:hover" */
    background-color: #fffbeb;
  }
}
```

> **Version note:** Native CSS nesting works in current major browsers, but the exact syntax rules have been relaxed over time (older versions required `&` before type selectors like `h2`). Using `&` as shown works everywhere nesting is supported, but verify support for your audience on MDN. Nesting **doesn't** reduce specificity: `.recipe { & h2 }` still produces `.recipe h2` (0,1,1). Avoid nesting more than two or three levels deep.

**Where selectors go next (placeholders):**
- In **React**, you'll attach classes with `className` and import CSS files (**React styling** lesson).
- **CSS Modules** automatically make class names unique per component, so the "global" nature of selectors stops causing accidental clashes (**React styling** lesson).
- **Tailwind** replaces most hand-written selectors with single-purpose classes, but its `hover:`, `focus-visible:`, and `has-[…]:` variants *are* these pseudo-classes (**Tailwind** lesson).
- DevTools debugging, specificity, and inheritance apply in every one of these approaches.

**Why it matters**
Stylesheets live for years and are edited by many people. Flat, predictable selectors prevent the slow decay into `!important` and "don't touch it" CSS.

**Common beginner mistakes**
- **Mistake:** Selecting by structure: `div > div > span`.
  **Symptom:** The style breaks when someone wraps an element in another `<div>`.
  **Fix:** Add a descriptive class.
- **Mistake:** Naming classes by appearance: `.red-text`.
  **Symptom:** The name lies after a redesign.
  **Fix:** Name by purpose: `.error`, `.warning`.
- **Mistake:** Nesting four or five levels deep.
  **Symptom:** Huge specificity and unreadable code.
  **Fix:** Keep nesting shallow.
- **Mistake:** Duplicating one declaration across many rules.
  **Symptom:** Redesign means editing 20 places.
  **Fix:** Use a shared class or selector list (CSS variables arrive in a later lesson).
- **Mistake:** Using ID selectors "because it's one element."
  **Symptom:** Can't override with a class.
  **Fix:** Class selectors, always.

**Try it (4 min)**
Rewrite `ul.ingredients li.optional span.hint` as a single class selector and explain in one sentence what you changed and why.

**Project step: Milestone 9 (about 10 min)**
Refactor your stylesheet: group repeated rules, remove any ID selectors you used for styling, and rewrite one recipe-card block using nesting.

---

## 4. How the Sub-Sections Connect

**Dependency order**

```
3.1 CSS basics: rules, stylesheets, DevTools   [the container for everything]
   │
3.2 Type / class / ID / universal              [the basic selectors, the foundation]
   │
3.3 Combinators + selector lists               [builds on 3.2: relationships between elements]
   │
3.4 Attribute selectors                        [builds on 3.2: select by attributes]
   │
3.5 Pseudo-classes (state, structure, forms)   [attach to anything from 3.2-3.4]
   │
3.6 :not() / :is() / :where() / :has()         [takes selectors from 3.2-3.5 as arguments]
   │
3.7 Pseudo-elements                            [parts and generated content; ends a selector]
   │
3.8 Specificity, cascade, inheritance          [explains how ALL of 3.2-3.7 compete]
   │
3.9 Maintainable selectors + nesting           [applies 3.8 to write good selectors]
```

**Hierarchy:** 3.1 is the *setting*. 3.2-3.4 are **what to select** (by name, by relationship, by attribute). 3.5-3.7 are **extra precision** (state and position, logic, parts). 3.8-3.9 are **resolution and quality** (which rule wins, and how to write rules you can live with).

**Key relationships:**
- **Compound selectors** from 3.2 appear inside every later technique: `input:checked + label` uses a type, a pseudo-class (3.5), and a combinator (3.3).
- **Specificity** (3.8) is built from the selector types you learned in 3.2-3.7, and `:where()` and `:is()` (3.6) are specificity tools.
- **DevTools** (3.1) is the debugging tool for every section.

**Project thread:** one page, nine milestones: link the stylesheet → basic selectors → combinators → attribute selectors → pseudo-classes → logical selectors → pseudo-elements → conflicts → refactor.

**Suggested live-teaching flow (3 hours = 180 min)**

| Clock | Min | Section | Mode |
|---|---|---|---|
| 0:00-0:10 | 10 | 3.1 CSS basics | Demo creating the file and DevTools; learners set up (Milestone 1) |
| 0:10-0:30 | 20 | 3.2 Basic selectors | Demo class vs ID; whole class codes Milestone 2; use the `*` outline trick |
| 0:30-0:50 | 20 | 3.3 Combinators | Live-toggle `>` vs a space to show the nested list difference (Milestone 3) |
| 0:50-1:00 | 10 | 3.4 Attribute selectors | Quick demo; learners code Milestone 4 |
| 1:00-1:20 | 20 | 3.5 Pseudo-classes | Demo keyboard tabbing and `nth-child` vs `nth-of-type`; Milestone 5 |
| 1:20-1:30 | 10 | Break | |
| 1:30-1:45 | 15 | 3.6 Logical pseudo-classes | Demo `:has()` as "the parent selector"; Milestone 6 |
| 1:45-1:55 | 10 | 3.7 Pseudo-elements | Demo the missing `content` bug; Milestone 7 |
| 1:55-2:15 | 20 | 3.8 Specificity and cascade | Whiteboard the (A, B, C) score; predict-then-check game; Milestone 8 |
| 2:15-2:25 | 10 | 3.9 Maintainable selectors | Before/after refactor; Milestone 9 |
| 2:25-2:50 | 25 | Knowledge check and applied exercise | About 8 min on questions, 17 min on the **Task List Styling** exercise |
| 2:50-3:00 | 10 | Recap and next-lesson bridge | Recap the specificity table and debugging steps |

*Trim rule if running long:* demo 3.7 and make Milestone 9 homework, and shorten the Express bridge by just opening `index.html` in the browser.

---

## 5. Key Terms Glossary

*(in order of first appearance)*

- **CSS (Cascading Style Sheets)**: the language that controls how HTML is presented.
- **Stylesheet**: a file or block containing CSS rules.
- **Rule**: one selector plus its declaration block.
- **Selector**: the part of a rule that chooses which elements it applies to.
- **Declaration block**: the `{ … }` section containing declarations.
- **Declaration**: one `property: value;` pair.
- **Property / value**: what to change (`color`) / what to change it to (`#b45309`).
- **DevTools**: the browser's built-in tools for inspecting and editing a page.
- **Type selector**: matches elements by tag name (`h2`).
- **Class selector**: matches elements with a given class (`.tag`).
- **ID selector**: matches the one element with a given `id` (`#site-header`).
- **Universal selector**: `*`, which matches every element.
- **Compound selector**: simple selectors joined without spaces (`p.note`).
- **Combinator**: a symbol (space, `>`, `+`, `~`) linking selectors by relationship.
- **Selector list**: comma-separated selectors sharing one rule.
- **Subject**: the rightmost part of a selector, which is the element that gets styled.
- **Descendant combinator (space)**: matches elements anywhere inside another.
- **Child combinator (`>`)**: matches only direct children.
- **Adjacent sibling combinator (`+`)**: matches the element immediately after another.
- **General sibling combinator (`~`)**: matches any later sibling.
- **Attribute selector**: matches elements by an attribute or its value (`[href^="http"]`).
- **Pseudo-class**: a selector for a state or position, written with one colon (`:hover`).
- **Structural pseudo-class**: one based on position among siblings (`:nth-child()`).
- **`:focus-visible`**: focus that should show a ring, such as from keyboard navigation.
- **Functional pseudo-class**: one that takes a selector argument (`:not()`, `:is()`, `:where()`, `:has()`).
- **Relative selector**: a selector starting with a combinator, used inside `:has()`.
- **Pseudo-element**: a selector for a part of an element or generated content, written with two colons (`::before`).
- **Generated content**: content inserted by CSS via the `content` property.
- **Cascade**: the algorithm that decides which conflicting declaration wins.
- **Specificity**: the (A, B, C) score that ranks how strongly a selector competes.
- **Source order**: the order rules appear; the later rule wins a tie.
- **Inheritance**: an element taking its parent's value for certain properties.
- **`!important`**: a flag that makes a declaration override normal declarations.
- **Inline style**: CSS written in an element's `style` attribute.
- **Computed style**: the final value the browser applies after the cascade.
- **Native CSS nesting**: writing rules inside other rules, using `&` for the parent selector.
- **BEM**: a naming convention (Block, Element, Modifier) for classes.

---

## 6. Knowledge Check

### Part A: Questions

**Q1 (Recall).** What is the difference between `.tag .vegan` and `.tag.vegan`? Describe which HTML each would match.

**Q2 (Predict the output).** What color is the paragraph text, and why?

```html
<div id="box" class="panel">
  <p class="intro">Hello</p>
</div>
```

```css
p            { color: red; }
.intro       { color: blue; }
.panel p     { color: green; }
#box p       { color: orange; }
```

**Q3 (Spot the bug).** The PDF link isn't bold. Why, and what is the fix?

```css
a[href$=.pdf] {
  font-weight: 700;
}
```

**Q4 (Predict the output).** Which element receives the border, and does it match what the author probably intended?

```html
<section class="cards">
  <h2>Our menu</h2>
  <div class="card">Starter</div>
  <div class="card">Main</div>
  <div class="card">Dessert</div>
</section>
```

```css
.card:nth-child(2) {
  border: 2px solid #b45309;
}
```

**Q5 (Spot the bug).** Hovering over the nav links never changes their color. Why?

```css
a:hover {
  color: #1d4ed8;
}

.main-nav a {
  color: #78350f;
}
```

### Part B: Applied mini-exercise: Task List Styling

**Goal:** Style a small task list using **only the selector techniques from this lesson**, with low specificity and no shortcuts.

**Starter code:**

```html
<ul class="tasks">
  <li class="task done">Write lesson plan</li>
  <li class="task">Review pull request <span class="badge urgent">urgent</span></li>
  <li class="task">Deploy to staging <a href="https://status.example.com">status</a></li>
  <li class="task" data-priority="high">Fix login bug</li>
  <li class="task" data-priority="low">Update README</li>
</ul>

<form class="add-task">
  <input type="text" name="title" required placeholder="New task" />
  <button type="submit" disabled>Add</button>
</form>
```

```css
/* styles.css: your rules go here */
```

**Requirements:**
1. Every `.task` has a thin bottom border, except the **last** one (use a structural pseudo-class).
2. **Even** rows have a light background stripe.
3. A **done** task has muted text and a line through it (`text-decoration: line-through;`) **and** a grey background that **wins over the even-row stripe** (explain in a CSS comment why it wins).
4. Tasks with `data-priority="high"` get a thick left border (`border-left`), selected with an attribute selector.
5. External links inside tasks get an arrow added with a pseudo-element.
6. The `.badge.urgent` badge has its own background and text color.
7. Any task **containing** a badge is bold (use `:has()`).
8. The disabled button looks disabled, and the input's placeholder has a muted color.
9. **Constraints:** no ID selectors, no `!important`, no inline styles, and no selector with specificity greater than **(0, 3, 0)**.

**Acceptance criteria:**
- DevTools shows the stripe on even rows, and the **done** task on an even row still shows its grey background.
- Only the last task has no bottom border, and nested content doesn't change that.
- The high-priority task has the left border, and the low-priority one doesn't.
- The "status" link shows an arrow that isn't in the HTML source.
- Only the "Review pull request" task is bold.
- No `!important` or `#` appears in your stylesheet.

---

## 7. Summary Recap

- **A rule is a selector plus declarations.** CSS fails silently, so DevTools (the Styles and Computed panes) is how you see what matched and what lost.
- **Start with classes.** Type, class, ID, and universal selectors are the foundation, and compound selectors (`p.note`, `.tag.vegan`) combine them. Prefer classes over IDs, and name by role.
- **Combinators and attribute selectors target by context.** Descendant (space), child (`>`), and sibling (`+`, `~`) combinators select by relationship, and `[attr^=…]`, `[attr$=…]`, and `[attr*=…]` select by attribute values.
- **Pseudo-classes and pseudo-elements add state and detail.** `:hover`, `:focus-visible`, `:nth-child()`, and form states style *when and where*, `:not()`/`:is()`/`:where()`/`:has()` add logic (including the long-awaited parent selector), and `::before`/`::after`/`::marker` style parts, with `content` required.
- **Conflicts resolve in a fixed order.** Importance, inline style, then **specificity** (A, B, C), then **source order**, with inherited values the weakest. The right fix is almost never `!important`.
- **Flat beats clever.** Single-class selectors, low specificity, and shallow nesting keep CSS easy to override and safe to change, and the same selector knowledge carries into React, CSS Modules, and Tailwind.

---

## 8. Next Lesson Bridge

You can now point at any element on a page, but you've only changed a handful of visual properties. The next lessons teach what to *do* with those selectors: the **box model** (padding, margin, borders, and sizing) and **typography and color**, followed by **Flexbox and Grid** for layout. After that, the **React styling** lessons show how `className`, CSS Modules, and Tailwind apply exactly the selector and cascade rules you learned today.

---

## Instructor Answer Key

**Q1.** `.tag .vegan` (with a space) is a **descendant** selector: it matches an element with class `vegan` that is **inside** an element with class `tag` (for example `<span class="tag"><em class="vegan">…</em></span>`). `.tag.vegan` (no space) is a **compound** selector: it matches a **single element** that has **both** classes (for example `<span class="tag vegan">`). In the Selector Kitchen HTML only `.tag.vegan` matches anything.

**Q2.** **Orange.** Specificity: `p` = (0,0,1), `.intro` = (0,1,0), `.panel p` = (0,1,1), `#box p` = (1,0,1). The ID in `#box p` gives it the highest score, so it wins regardless of source order. (Without the `#box p` rule, `.panel p` (0,1,1) would beat `.intro` (0,1,0) and the text would be **green**, which is a good follow-up to ask.)

**Q3.** The value `.pdf` isn't quoted. A bare attribute value must be a valid identifier, and `.pdf` starts with a dot, so the whole selector is **invalid** and CSS **silently drops the rule**. Fix: `a[href$=".pdf"] { font-weight: 700; }`.

**Q4.** The border goes on the **first** card ("Starter"), not the second. `:nth-child(2)` counts **all** siblings, and the `<h2>` is child number 1, so the second child is the first `.card`. The author probably intended "Main." Fixes: `.card:nth-of-type(2)` (counts only `<div>` siblings, so it matches "Main"), or give the intended card its own class.

**Q5.** `a:hover` has specificity (0,1,1) (one pseudo-class and one type), and `.main-nav a` also has (0,1,1) (one class and one type). They **tie**, so the **later** rule wins, and `.main-nav a` comes later and keeps the color fixed. Fix: put the hover rule **after**, or make it more specific: `.main-nav a:hover { color: #1d4ed8; }` (0,2,1).

### Model solution: Task List Styling

```css
/* Base task row: (0,1,0) */
.task {
  border-bottom: 1px solid #e7e5e4;
  padding: 8px;
}

/* Requirement 1: no border on the last task: (0,2,0) */
.task:last-child {
  border-bottom: none;
}

/* Requirement 2: stripe on even rows: (0,2,0) */
.task:nth-child(even) {
  background-color: #f5f5f4;
}

/* Requirement 3: done tasks. This is (0,2,0), the SAME specificity as the stripe rule
   above, so it wins ONLY because it comes LATER in the file (source order). */
.task.done {
  color: #78716c;
  text-decoration: line-through;
  background-color: #e7e5e4;
}

/* Requirement 4: high priority: attribute selector (0,2,0) */
.task[data-priority="high"] {
  border-left: 6px solid #b45309;
}

/* Requirement 5: arrow added to external links inside tasks (0,2,1) */
.task a[href^="http"]::after {
  content: " ↗";
}

/* Requirement 6: urgent badge (0,2,0) */
.badge.urgent {
  background-color: #dc2626;
  color: #ffffff;
  padding: 2px 8px;
}

/* Requirement 7: tasks containing a badge are bold (0,2,0) */
.task:has(.badge) {
  font-weight: 700;
}

/* Requirement 8: disabled button and placeholder */
.add-task button:disabled {
  opacity: 0.5;
}

.add-task input::placeholder {
  color: #78716c;
}
```

**Talking points for review:**
- **Source-order puzzle:** `.task:nth-child(even)` and `.task.done` both score (0,2,0) (one class plus one pseudo-class, or two classes). A tie is resolved by **order**, so `.task.done` must come **after** the stripe rule to win on even rows. Move it above and the stripe wins, which is a great live demonstration.
- **No ID and no `!important`:** every selector is built from classes, attributes, and pseudo-classes, and the highest specificity used is (0,2,1), within the (0,3,0) limit.
- **`:last-child` vs nested content:** the `.task` rows have no nested lists, so `:last-child` is safe here. In the recipe's nested ingredient list the unscoped form would have matched too. Scoping with `.task` avoids that.
- **`:has(.badge)`:** this is the "parent selector" case. It needed no extra class on the `<li>`.
- **Decoration vs content:** the `↗` arrow is generated content (decorative only). The link text and destination remain in the HTML.
- **DevTools check:** inspect the done task on an even row, and in the Styles pane note `.task:nth-child(even)` struck through beneath `.task.done`.

> **Version note for the instructor:** before class, verify browser support (via MDN's Baseline status and your learners' browsers) for `:has()`, `:is()`/`:where()`, `:not()` with selector lists, `:focus-visible`, `:user-invalid`, native CSS nesting, and `::marker`. DevTools labels and layouts differ slightly between Chrome, Edge, and Firefox, so run the demos in the browser you'll teach with. Express is used only for static file serving; any version works.

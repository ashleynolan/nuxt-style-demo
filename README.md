# nuxt-style-demo

## Issue being reproduced

CSS from different pages & layouts loads on all pages, resulting in pages being styled incorrectly.

For example, I have two layouts, default and altlayout, and two pages, Home and Search.

Styles defined in Home also load in the Search page (i.e. a background-colour on the homepage is applied to both pages), even when the homepage uses the default layout and search page uses altlayout.

## Test setup to reproduce

See this repo where this has been setup in Nuxt 2 and 3.

- Install base Nuxt build
- Setup 2 layout files in `layouts/` – `default.vue` and `altlayout.vue`.
    - `default.vue` sets a style block with `body { background-color: lightblue; }`
    - `altLayout.vue` sets no layout specific styles.
- Setup 4 pages; `index.vue`, `search.vue,` `menu.vue` and `checkout.vue`
    - `index.vue` uses the default layout and sets `background-color: pink`.
    - `search.vue`uses the default layout and sets `color: purple;`
    - `menu.vue` uses the altLayout and sets no page specific styles.
    - checkout.vue uses the altlayout and sets `color: darkolivegreen;`


Expected Rendering:

| Page        | Expected text colour | Expected background colour |
| ----------- | -------------------- | -------------------------- |
| `/`         | black                | pink                       |
| `/search`   | purple               | lightblue                  |
| `/menu`     | black                | white                      |
| `/checkout` | darkolivegreen       | white                      |


### Nuxt 3 Rendering

**In dev mode**

With the default nuxt config, all CSS styles from all layouts and all pages are included when rendering a page.

| Page        | Layout      | Expected text colour | Actual text colour | Expected background colour | Actual background colour |
| ----------- | ----------- | -------------------- | ------------------ | -------------------------- | ------------------------ |
| `/`         | `default`   | black                | ❌ purple          | pink                       | ❌ lightblue             |
| `/search`   | `default`   | purple               | ✅ purple          | lightblue                  | ✅ lightblue             |
| `/menu`     | `altlayout` | black                | ❌ purple          | white                      | ❌ pink                  |
| `/checkout` | `altlayout` | darkolivegreen       | ✅ darkolivegreen  | white                      | ❌ pink                  |

- Homepage renders with a light-blue background and purple text (incorrect bg and text colour)
- Search page renders with a light-blue background and purple text (correct rendering)
- Menu page renders with a pink background and purple text (incorrect bg and text colour)
- Checkout page renders with a pink background and dark olive green text (incorrect bg colour)

**In Build Mode**

All pages render styles as expected.

### Nuxt 2 Rendering

**In dev mode**

With the default nuxt config, CSS styles from all layouts are included when rendering a page.

| Page        | Layout      | Expected text colour | Actual text colour | Expected background colour | Actual background colour |
| ----------- | ----------- | -------------------- | ------------------ | -------------------------- | ------------------------ |
| `/`         | `default`   | black                | ✅ black           | pink                       | ✅ pink                 |
| `/search`   | `default`   | purple               | ✅ purple          | lightblue                  | ✅ lightblue            |
| `/menu`     | `altlayout` | black                | ✅ black           | white                      | ❌ lightblue            |
| `/checkout` | `altlayout` | darkolivegreen       | ✅ darkolivegreen  | white                      | ❌ lightblue            |

- Homepage renders with a pink background and black text (correct rendering)
- Search page renders with a light-blue background and purple text (correct rendering)
- Menu page renders with a light-blue background and black text (incorrect background colour, applying bg style from incorrect layout)
- Checkout page renders with a light-blue background and dark olive green text (incorrect background colour, applying bg style from incorrect layout)

With `splitChunks: layout` set in the nuxt config (uncomment this config in `nuxt.config.js` (lines 40-42):

| Page        | Layout      | Expected text colour | Actual text colour | Expected background colour | Actual background colour |
| ----------- | ----------- | -------------------- | ------------------ | -------------------------- | ------------------------ |
| `/`         | `default`   | black                | ✅ black           | pink                       | ❌ lightblue             |
| `/search`   | `default`   | purple               | ✅ purple          | lightblue                  | ✅ lightblue             |
| `/menu`     | `altlayout` | black                | ✅ black           | white                      | ✅ white                 |
| `/checkout` | `altlayout` | darkolivegreen       | ✅ darkolivegreen  | white                      | ✅ white                 |

- Homepage renders with a light-blue background and black text (incorrect background colour, layout bg style is taking precedence over page style)
- Search page renders with a light-blue background and purple text (correct rendering)
- Menu page renders with a white background and black text (correct rendering)
- Checkout page renders with a white background and dark olive green text (correct rendering)

**In build mode**

Renders pages the same as in dev mode (see above).
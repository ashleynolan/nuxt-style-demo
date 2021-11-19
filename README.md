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


**In Build Mode**

All pages render styles as expected.

| Page        | Layout      | Expected text colour | Actual text colour | Expected background colour | Actual background colour |
| ----------- | ----------- | -------------------- | ------------------ | -------------------------- | ------------------------ |
| `/`         | `default`   | black                | ✅ black           | pink                       | ✅ pink                  |
| `/search`   | `default`   | purple               | ✅ purple          | lightblue                  | ✅ lightblue             |
| `/menu`     | `altlayout` | black                | ✅ black           | white                      | ✅ white                 |
| `/checkout` | `altlayout` | darkolivegreen       | ✅ darkolivegreen  | white                      | ✅ white                 |

### Nuxt 2 Rendering

**In dev mode**

With the default nuxt config, CSS styles from all layouts are included when rendering a page.

| Page        | Layout      | Expected text colour | Actual text colour | Expected background colour | Actual background colour |
| ----------- | ----------- | -------------------- | ------------------ | -------------------------- | ------------------------ |
| `/`         | `default`   | black                | ✅ black           | pink                       | ✅ pink                  |
| `/search`   | `default`   | purple               | ✅ purple          | lightblue                  | ✅ lightblue             |
| `/menu`     | `altlayout` | black                | ✅ black           | white                      | ❌ lightblue             |
| `/checkout` | `altlayout` | darkolivegreen       | ✅ darkolivegreen  | white                      | ❌ lightblue             |


With `splitChunks: layout` set in the Nuxt config (uncomment this config in `nuxt.config.js` - lines 40-42):

| Page        | Layout      | Expected text colour | Actual text colour | Expected background colour | Actual background colour |
| ----------- | ----------- | -------------------- | ------------------ | -------------------------- | ------------------------ |
| `/`         | `default`   | black                | ✅ black           | pink                       | ❌ lightblue             |
| `/search`   | `default`   | purple               | ✅ purple          | lightblue                  | ✅ lightblue             |
| `/menu`     | `altlayout` | black                | ✅ black           | white                      | ✅ white                 |
| `/checkout` | `altlayout` | darkolivegreen       | ✅ darkolivegreen  | white                      | ✅ white                 |


**In build mode**

Renders pages the same as in dev mode (see above).
---
{"dg-publish":true,"dg-path":"UltraInsync/publish/Digital Garden.md","permalink":"/ultra-insync/publish/digital-garden/","dg-note-properties":{}}
---



> [!summary]
> Git Push ultrainsync.github.io repo will start GitHub workflow of serving GitHub Pages (with no-indexing business and tax folders)
> run `npm run build:adsvise` and put it inside `/on/` folder inside adsvise.me
> `npm run dev` will default to GitHub workflow (which is no-indexing `adsvise.me/on` blogpost)
> >[!info]
> > compare customized Digital Garden repo with main dev at `.tmp/digitalgarden`
> 
> >[!warning]
> > use `insert margin note + ;;` for sensitive information (it will omit from publishing it)

## Gripes
![embedding operon task (with its Id) dont show stylized checklist.png](/img/user/on/Passion/UltraInsync/publish/attachments/embedding%20operon%20task%20(with%20its%20Id)%20dont%20show%20stylized%20checklist.png)

## Customizations (Divergence from Upstream)
When pulling updates from `https://github.com/oleeskild/digitalgarden`, be aware of the following local customizations we've made to support dual deployment (`ultrainsync.github.io` and `adsvise.me/on/`).

**Date:** July 25, 2026
**Conversation ID:** 902be4ad-e781-42a1-91d2-67c77defc3e7
**Haft Decisions:** `prob-20260725-a2fdb368`, `dec-20260725-27f791ac`

### Changes Made:
1. **`.eleventy.js`**: Added dynamic `pathPrefix` injection.
   - **What changed:** Appended `pathPrefix: process.env.PATH_PREFIX || "/",` to the returned configuration object at the end of the file.
   - **Why:** Allows Eleventy 3.0's HTML Base Plugin to automatically rewrite all `href` and `src` paths in the generated HTML based on the deployment target (e.g., prepending `/on/` for adsvise.me).
2. **`package.json`**: Added custom build script.
   - **What changed:** Added `"build:adsvise": "cross-env PATH_PREFIX=/on/ npm-run-all get-theme build:sass build:eleventy"` to the `scripts` object.
   - **Why:** Used to build the site targeting the subfolder deployment.
3. **`src/site/_data/meta.js` & `src/site/_includes/components/pageheader.njk`**: Added Conditional SEO NoIndex.
   - **What changed:** Exported `pathPrefix` in `meta.js` and added Jinja if-statements in `pageheader.njk` to inject `<meta name="robots" content="noindex, nofollow">`.
   - **Why:** Prevents duplicate content SEO penalties. Prime/Public rank on Github Pages; Profession/Passion rank on Adsvise.me.
4. **`.eleventy.js`**: Operon tag stripping and task transclusion (embeds).
   - **What changed:** Added custom `markdown-it` core rules near line 163. The `transclude_operon` rule intercepts `[[path#-operonId]]` wikilinks, synchronously fetches the target file from `src/site/notes/`, extracts the specific task by its `operonId`, and directly replaces the wikilink with the raw markdown task text. Then, `strip_operon` silently strips out `{{key:: value}}` metadata tags from the raw markdown.
   - **Why:** This replicates the Obsidian Operon plugin's behavior by turning dead anchor links into basic native checklists on your website, avoiding complex HTML wrappers and styling conflicts while keeping backend parameters hidden.

### Note on Upstream Merges:
- **Only `pageheader.njk` was modified among templates.** If upstream templates (`.njk` files) change, you can merge them safely without worrying about hardcoded paths breaking our subfolder deployment, as long as the `pathPrefix` config in `.eleventy.js` remains intact. Be careful not to overwrite the SEO logic in `pageheader.njk`.
# heb-meal-planner
Automate meal planning + grocery shopping at HEB

# 🍽 Family Meal Planner

A Google Sheets-based meal planning and grocery management tool powered by Claude AI. Generates weekly dinner suggestions tailored to your family, scales ingredient quantities to your household size, and builds a searchable grocery list with direct HEB links — all from a single spreadsheet.

---

## What It Does

Each week, the planner generates 7 healthy dinner suggestions across varied cuisines. You and your spouse select 4, and the sheet automatically builds a scaled grocery list with one-click HEB search links for every ingredient. Over time it learns your preferences through a Meal Catalogue you build by rating meals after cooking.

**Key features:**
- 7 AI-generated meal suggestions per week with full recipes
- Dynamic serving sizes — change Adults or Leftover Nights and ingredient quantities update instantly
- Grocery list auto-populated and deduplicated from selected meals
- Single-column status per grocery item: `Need It` / `In Cart` / `Have It`
- Pantry & Staples tracker so known items are never added to your cart
- Growing Meal Catalogue that Claude references to avoid repeating meals
- HEB search links on every ingredient

---

## Prerequisites

- A Google account
- An Anthropic API key — get one free at [console.anthropic.com](https://console.anthropic.com)
- A few minutes for one-time setup

---

## Installation

### Step 1 — Create the Apps Script project

1. Go to [script.google.com](https://script.google.com) and click **New project**
2. Delete all the placeholder code in the editor
3. Paste the entire contents of `MealPlannerFull.gs` into the editor
4. Click **Save** (Ctrl+S / Cmd+S) and give the project a name like `Meal Planner`

### Step 2 — Run setup

1. In the function dropdown at the top of the editor, select **`setupMealPlanner`**
2. Click **Run**
3. Google will ask for permissions — click **Allow** (this only happens once)
4. A dialog will appear with a link to your new spreadsheet, or find **"Family Meal Planner"** in your Google Drive

### Step 3 — Add your API key

1. In the Apps Script editor, click **Project Settings** (gear icon on the left sidebar)
2. Scroll down to **Script Properties** and click **Add script property**
3. Set the property name to `ANTHROPIC_API_KEY` and the value to your key from Anthropic Console
4. Click **Save script properties**

### Step 4 — Share with your spouse

Open the spreadsheet in Google Drive, click **Share**, and add your spouse's email with Editor access. Both of you can now update meal selections, grocery statuses, and ratings in real time.

---

## Weekly Workflow

### 1. Generate meals
Open the spreadsheet → click **Meal Planner** in the top menu → **Generate This Week's Meals**

The script calls Claude, which returns 7 dinner suggestions based on:
- Your family's serving preferences
- Items already in your pantry
- Meals you've made recently (from the Catalogue)
- A healthy, varied cuisine rotation

This takes about 15 seconds. When complete, the **Weekly Meals** tab populates with 7 new suggestions and the **Recipes** tab fills in with full ingredient lists and instructions.

### 2. Select your meals
Review the 7 suggestions with your spouse on the **Weekly Meals** tab. Change the **Select?** column to `Yes` for the 4 meals you want this week. The other 3 are simply ignored.

**Adjust serving sizes if needed** — the Adults and Leftover Nights columns (highlighted in yellow) are editable. Change them per meal and the Total Servings column updates instantly. Ingredient quantities on the grocery list scale accordingly.

| Column | Default | What it does |
|---|---|---|
| Adults | 2 | Number of people eating |
| Leftover Nights | 1 | How many nights of leftovers you want |
| Total Servings | Formula | Adults × (1 + Leftover Nights) |

> **Example:** 2 adults, 1 leftover night = 4 total servings. Change Adults to 4 and you get 8 servings automatically.

### 3. Refresh the grocery list
Click **Meal Planner** → **Refresh Grocery List**

All ingredients from your selected meals are pulled, deduplicated across meals, scaled to your serving sizes, and written to the **Grocery List** tab with a category, the meal(s) they belong to, and an HEB search link. Items already marked `Yes` in your Pantry & Staples tab are pre-flagged as `Have It`.

### 4. Shop
On the **Grocery List** tab, work through the **Meal Ingredients** and **Standing Grocery List** sections. Use the **Status** dropdown for each item:

| Status | Meaning | Row color |
|---|---|---|
| `Need It` | Default — add this to your cart | White / light gray |
| `In Cart` | You've added it to your HEB cart | Green tint |
| `Have It` | You already own this — skip it | Grayed out |

Click any **HEB Link** to open a search for that ingredient directly on heb.com.

### 5. Rate meals after cooking
After you cook a meal, go back to the **Weekly Meals** tab and enter a rating (1–5) in the Rating column. Then run **Meal Planner** → **Save Rated Meals to Catalogue**. This:
- Saves the meal to your **Meal Catalogue** with the date, rating, and times made
- Stamps the **Last Made** date on the Weekly Meals tab
- Updates Claude's context so it avoids suggesting that meal again too soon

---

## Tab Reference

### Weekly Meals
The main control panel. Shows the 7 current meal suggestions. You select meals here, adjust serving sizes, and add ratings after cooking.

Yellow cells (Adults, Leftover Nights) are the only cells you need to edit manually. Everything else is either auto-populated by Claude or formula-driven.

### Recipes
Full recipes for all 7 current meals — ingredient list, step-by-step instructions, cook tips, and an HEB search link for the main ingredient. Populated automatically when you generate meals. Ingredient quantities shown are base (2 adults) — the Grocery List applies your scaling.

### Grocery List
Two sections:

**Meal Ingredients** (rows 5–29) — auto-filled each time you run Refresh Grocery List. Quantities are scaled to your Adults and Leftover Nights settings.

**Standing Grocery List** (row 31 onward) — your permanent household items. Edit this section to match what you regularly buy. It is never overwritten by the script.

### Meal Catalogue
A growing library of every meal you've rated. Claude reads this before generating new suggestions to avoid repeating recent meals. The more you use the planner and rate meals, the more personalized suggestions become.

### Pantry and Staples
A list of items you always keep at home. Any ingredient marked `Yes` is automatically flagged as `Have It` when the grocery list refreshes. Keep this updated and you'll never be reminded to buy olive oil you already own.

---

## Customization

### Changing your default serving size
Go to the **Weekly Meals** tab and change the Adults or Leftover Nights values in any row. These are remembered until you generate new meals. If you always cook for 2 adults with 1 leftover night, the defaults are already set correctly.

### Editing your pantry
Open the **Pantry and Staples** tab and change any item's **On Hand?** value to `Yes`, `No`, or `Running Low`. Only items marked `Yes` are excluded from the grocery list. Add new rows at the bottom for any staples not already listed.

### Editing your standing grocery list
The Standing Grocery List section of the **Grocery List** tab is yours to edit freely. Add, remove, or change items and quantities — the script never touches this section.

### Updating your API key
Go to **Extensions** → **Apps Script** → **Project Settings** → **Script Properties** and update the `ANTHROPIC_API_KEY` value.

---

## Troubleshooting

**"Missing API Key" alert when generating meals**
Your Anthropic API key hasn't been added yet. Follow Step 3 of the Installation section above.

**"Parse Error" when generating meals**
Claude occasionally returns malformed JSON. Run Generate This Week's Meals again — it almost always succeeds on retry. If it fails repeatedly, check the Execution Log in Apps Script (View → Execution log) for the raw response.

**Grocery list shows more than 25 ingredients**
The script caps the meal ingredient section at 25 rows. If you select meals with many unique ingredients and exceed this, the overflow is silently dropped. You can increase the cap by changing the `25` value in the `refreshGroceryList` function (two places — the blank row loop and the `limit` variable).

**Meal Catalogue shows old meals from the seed data**
The seed meals (Sheet Pan Chicken, Tacos, etc.) were added so the sheet isn't empty on first use. You can delete any of them from the Meal Catalogue tab — they'll stop appearing in Claude's "avoid repeating" list.

**The sheet already exists and I want to start fresh**
Delete or archive the old "Family Meal Planner" sheet in Google Drive, then run `setupMealPlanner` again from the Apps Script editor.

---

## Architecture

```
script.google.com
└── MealPlannerFull.gs
    ├── setupMealPlanner()     — one-time sheet builder
    ├── onOpen()               — installs Meal Planner menu
    ├── generateMeals()        — calls Claude API, writes meals + recipes
    ├── refreshGroceryList()   — scales + deduplicates ingredients
    └── saveRatedMealsToCatalogue() — updates library + stamps dates

Google Sheet: "Family Meal Planner"
├── Weekly Meals               — meal selection + serving controls
├── Recipes                    — full recipes (read-only, auto-populated)
├── Grocery List               — scaled shopping list + standing items
├── Meal Catalogue             — rated meal library
└── Pantry and Staples         — owned items to exclude from grocery list

Claude API (claude-opus-4-6)
└── Called once per week to generate 7 meal suggestions
    Input:  pantry items, recent meals from Catalogue
    Output: JSON with meals, ingredients (base qty), instructions
```

---

## Phase 2 — HEB Cart Automation (Coming Soon)

Phase 2 will add a Playwright-based browser automation script that opens HEB, logs in with your credentials, and automatically adds every `Need It` ingredient from your grocery list to your cart — no clicking required.

This runs as a separate local script on your computer triggered after you've reviewed your grocery list in the sheet. Setup guide will be provided as a separate document.

---

## Cost

The planner calls the Claude API once per week to generate meals. A single generation request uses approximately 1,500–2,000 output tokens. At current Anthropic API pricing, a full year of weekly generations costs roughly **$1–3 total** depending on the model tier.

Anthropic offers free API credits for new accounts. Check [console.anthropic.com](https://console.anthropic.com) for current pricing and credit availability.

---

## License

MIT — use freely, modify as needed, no attribution required.

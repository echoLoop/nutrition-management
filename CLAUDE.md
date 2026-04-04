# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A Chinese-language **carb-cycling nutrition planner** ("Keep Going - 凯圣王碳循环计划") — a single-page mobile web app for planning meals based on body type, tracking macros (carbs/protein/fat), recording weight, and generating daily check-in summaries.

## Architecture

This is a **zero-build, no-framework** project. Everything runs client-side from two files:

- **`index.html`** — the entire app: HTML structure, all CSS (inlined `<style>`), and all JavaScript (inlined `<script>`). ~1530 lines total.
- **`food_db.js`** — a standalone nutrition database (`FOOD_DB` array, `FOOD_DB_CATS` derived categories) loaded via `<script src>`. Data sourced from Chinese Food Composition Tables (每100g可食部分). ~260 lines.

### Key Data Flow

1. User enters weight, gender, body type, and custom coefficients (carb/fat/protein g/kg ratios)
2. A 7-day cycle (high/mid/low carb days) is configured via the cycle editor
3. `generatePlan()` computes weekly macros, distributes across days using `CARB_DIST`/`FAT_DIST` multipliers
4. Each day has 3 meals (breakfast 25%, lunch 40%, dinner 35%) with food selection and weight adjustment
5. Food can be selected from built-in `FOODS` object or added from `FOOD_DB` via search modal
6. Check-in panel aggregates all data (weight, diet, training, poop) into copyable text

### State Management

All state is in global variables. Persistence uses `localStorage` with these keys:
- `wt_records` — weight history
- `user_foods` — user-added food shortcuts per category (staple/protein/fat/veggie)
- `custom_trains` — custom training labels
- `ck_records` — check-in history
- `ck_goal_w`, `ck_start_w` — goal/start weights
- `ck_emojis` — customized emoji assignments for check-in fields

### Body Type Defaults (`BODY_DEFAULTS`)

- Endomorph (内胚型): carb 2.0, fat 0.8, protein 1.3 g/kg
- Mesomorph (中胚型): carb 2.5, fat 0.9, protein 1.5 g/kg
- Ectomorph (外胚型): carb 3.0, fat 1.0, protein 1.6 g/kg

## Development

Open `index.html` directly in a browser — no build step, no server required. For live reload during development:

```bash
# Any static file server works, e.g.:
python3 -m http.server 8000
npx serve .
```

## Food Database Categories

`food_db.js` contains entries across: 谷薯类, 畜肉类, 禽肉类, 水产海鲜, 蛋奶类, 豆制品, 蔬菜类, 水果类, 坚果种子, 油脂调味, 加工即食, 饮品冲调.

Each entry: `{ name, emoji, carb, protein, fat, cat }` — all values per 100g.

The in-app `FOODS` object (inside index.html) is a separate curated subset used as default quick-select options in meal builders. `FOOD_DB` is the full searchable database.

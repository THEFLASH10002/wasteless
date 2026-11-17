# WasteLess

**Team HerdWise (Senior Project, Group 7):** Anthony Tucker, Selorm Kalitsi, Sharon Omotosho, Nmachi Igwe, Kennedy Gregg

WasteLess is a food waste reduction system for university dining halls. It forecasts how many students will eat at each meal from past sales, weather, holidays, the academic calendar and campus events. From that forecast it recommends how much food to cook and buy, so the kitchen wastes less while still keeping enough food on the line.

This repository holds a working prototype. It's a single web page with sample data for one dining hall during the Fall 2026 semester, and the system is set to have gone live on Oct 1, 2026.

## Running it

The prototype has no dependencies and no build step.

- **Locally:** download or clone the repo and open `index.html` in a browser.
- **GitHub Pages:** go to *Settings → Pages*, choose *Deploy from a branch*, pick `main` and `/ (root)`, and save. The app will then be hosted at `https://<username>.github.io/<repo-name>/`.

Sign in with one of the demo accounts. Each role sees only the screens it has permission to use. Anything you change lasts until you reload the page.

## Demo accounts

| Account | Role | What they can do |
| --- | --- | --- |
| Dana Ruiz | Dining Hall Manager | Forecasts, overrides, prep, purchasing, waste log, dashboard, reports |
| Marcus Lee | Executive Chef | Prep guide, menu performance, waste log, stock alerts, recipes |
| Priya Shah | Inventory Manager | Purchasing recommendations, stock alerts |
| Tom Becker | Dining Hall Staff | Waste log and closing a service |
| Alex Morgan | Dining Services Admin | Data upload, menu & recipes, users & roles, dashboard, reports |
| Jordan Kim | Sustainability Coordinator | Dashboard, savings & sustainability reports |
| Sam Patel | Student | Menu & availability, crowd forecast, meal check-in, ratings, campus impact |

## Features by epic

### Epic 1: Machine learning & demand forecasting
- **1.1 Baseline forecast.** A time-series model learns a baseline for each meal and weekday from past diner counts, giving more weight to recent days. Each forecast comes with a 90% range.
- **1.2 Weather.** A sample weather feed for the next 7 days. The model learns how much rain, snow, heat and cold change attendance.
- **1.3 Calendar and events.** The model learns how much move-in, holidays, exams, breaks and home games change attendance. Managers can add new events, and forecasts update right away.
- **1.4 Retraining.** Closing a service records the actual diner count and refits the model. The retraining log shows the forecast error before and after.
- **1.5 Overrides.** A manager can change a forecast by a percentage or set an exact count. A reason is required, and every override is written to an audit log.

### Epic 2: Inventory & kitchen prep
- **2.1 Prep guide.** Servings and pans per dish, split into batches timed before opening, at the peak and for late service.
- **2.2 Purchasing.** Order quantities for 2, 3 or 5 days ahead, with safety stock sized to the forecast error. Perishables that won't keep that long are split into two deliveries.
- **2.3 Stock alerts.** Ingredients that won't cover demand through tomorrow, and the menu items they affect.
- **2.4 Menu performance.** Take rate, waste rate and student rating for each dish. Dishes with high waste and low ratings get a recommended prep cut.

### Epic 3: Waste tracking & analytics
- **3.1 Waste logging.** Log unserved prep or plate waste by dish, weight and meal period, with a timestamp.
- **3.2 Dashboard.** Predicted diners, actual diners and waste side by side.
- **3.3 Trends.** Switch between day, week, month and semester views.
- **3.4 Financial report.** Food cost avoided, projected savings for the semester, and return on the system's cost.
- **3.5 Sustainability report.** Metric tons of CO2e avoided, using an emission factor you can edit, plus text and CSV you can copy.

### Epic 4: Data administration
- **4.1 Data upload.** Upload CSV or JSON files. Rows are checked against the schema (`date, meal_period, menu_item, servings_sold`, plus an optional `waste_lbs`), errors are flagged row by row, and only clean rows are imported. Use **Load sample file** to see the checks in action.
- **4.2 Menu & recipes.** Edit dishes, portion sizes, pan sizes, dietary tags, allergens and ingredients per serving.
- **4.3 Users & roles.** Change each person's role and which screens each role can open.

### Epic 5: Student dining portal
- **5.1 Today's menu.** Live availability during service, with dietary and allergen filters.
- **5.2 Crowd forecast.** Expected diners and wait times by hour, with the quietest hour for each meal.
- **5.3 Low-availability alerts.** Students can opt in to a notification when a popular dish, or one they're watching, drops below 10%.
- **5.4 Meal check-in.** One tap per meal for the coming week. Check-ins feed into the forecast.
- **5.5 Ratings.** Anonymous 1–5 star ratings with comments, which go to the chef's menu performance screen.
- **5.6 Campus impact.** Pounds of waste avoided, progress toward a semester goal, and milestone badges.

The **Requirements map** screen in the app links every user story ID to the screen that handles it. The full traceability matrix is in [`docs/traceability-matrix.csv`](docs/traceability-matrix.csv).

## How the forecast works

```
forecast = baseline[meal][weekday] × weather factor × calendar factor × student check-in factor
```

- The baseline and factors are fitted from past data by alternating weighted averages. Recent days count more (a weight of 0.985 per day of age), and each factor is pulled toward 1.0 when there's little data for it.
- The 90% range is ±1.645 × the standard deviation of recent forecast errors.
- The accuracy shown since go-live is a true backtest: each day is predicted using only data from before that day.
- Prep servings = forecast × the dish's take rate × any prep cut × 1.05 buffer.
- Purchasing need = prep servings × recipe quantity, summed over every shift in the window. The order is need + safety stock − stock on hand.

## Prototype limits

- All data is generated sample data. The weather feed and register counts are simulated, and nothing connects to a real API, point-of-sale system or database.
- Data is kept in the browser's memory and resets on reload. A production version needs a backend database, real sign-in and a scheduled retraining job.

## Repository layout

```
index.html                     The complete app (HTML, CSS and JavaScript in one file)
docs/traceability-matrix.csv   Requirements-to-features traceability matrix
README.md
```

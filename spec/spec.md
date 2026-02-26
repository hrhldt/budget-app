Project: Centsible

Goal
Build a simple web app to track individual expenses grouped by month.

Scope
- Single-user, client-only app
- Data persisted to browser local storage
- No auth, no backend, no sync
- Entire app is a single self-contained index.html file (CSS and JS inlined)
- Must be launchable by opening index.html directly in a browser (file:// protocol, no server required)

Key user flows
- Set a monthly budget
- Add an expense with amount, name, and tag
- See remaining budget and total spent for the current month
- Edit and delete existing expenses

Data model
- Month
    - id: string (YYYY-MM)
    - budget: number (>= 0)
    - expenses: Expense[]
- Expense
    - id: string (uuid or timestamp-based)
    - name: string (1-60 chars)
    - amount: number (> 0, two decimals max)
    - tag: string (one of the predefined tags listed below)
    - createdAt: number (unix ms)
- Predefined Tags (user must select one; no free-text entry)
    - Housing
    - Transportation
    - Food
    - Utilities
    - Clothing
    - Medical/Healthcare
    - Insurance
    - Household Items/Supplies
    - Personal
    - Debt
    - Retirement
    - Education
    - Savings
    - Gifts/Donations
    - Entertainment

Local storage
- Storage key: centsible:v1
- Persist the full Month object for the currently viewed month.
- On load, restore last saved month; if none, initialize with current month and empty data.

Core requirements
- Budget
    - User can set or update the monthly budget.
    - Remaining = budget - totalSpent.
- Expenses
    - Create expense with name, amount, and tag.
    - Editing updates totals immediately.
    - Deleting updates totals immediately.
    - Expenses are grouped under the active month.
    - In the expense list, expenses are grouped by tag with a tag header showing the tag name and subtotal.
    - A spending-by-tag breakdown section shows total spent per tag, sorted by amount descending.
- Spending graph
    - A visual progress bar in the overview shows the percentage of budget spent.
    - Bar color shifts from accent (under 75%) → amber/warning (75-100%) → red (over budget).
    - Legend shows formatted spent and remaining amounts.
- Daily spending guidance
    - When adding an expense, calculate the daily spending allowance.
    - Daily allowance = (budget - totalSpent) / remaining days in current month.
    - Display message: "You can spend {value} every day for the {remaining_days_in_month} and still be within the budget".
    - {value} and {remaining_days_in_month} should be bold.
    - {value} should be formatted as a currency amount using the browser's locale (via Intl.NumberFormat).
    - {remaining_days_in_month} should show the number of days remaining, e.g. "remaining 5 days".
- Status indicator
    - If totalSpent > budget, show a red warning state.
    - If totalSpent <= budget, show a green state.
- Persistence
    - All budget and expenses persist via local storage.

UI/UX requirements
- Modern, clean, minimal design with elevated polish.
- Glassmorphism-style cards (semi-transparent backgrounds with backdrop blur).
- Gradient accent color used for logo, primary buttons, and progress bars.
- Layered shadow system (sm / md / lg) for depth hierarchy.
- Micro-interactions: hover lifts on cards and expense rows, scale on icon buttons, slide-up modal entrance.
- Focus rings with soft accent glow on inputs and selects.
- Smooth cubic-bezier transitions throughout.
- Custom font pair (distinct for headings and body).
- All icons use Font Awesome (loaded via CDN).
- All icons must be clearly visible in both light and dark modes (use theme-aware colors).
- Currency symbols and formatting derived from the browser's locale (Intl.NumberFormat).
- User can select their preferred currency from a dropdown picker in the header.
- Selected currency is persisted to local storage and used for all amount formatting.
- Default currency is USD.
- Support light and dark mode with a toggle.
- Responsive layout for mobile and desktop.

PWA requirements
- Web app manifest (name, short_name, icons, display: standalone, theme_color, background_color).
- Manifest and service worker are inlined via JS Blob URLs to keep the app as a single file.
- Service worker provides offline support with a network-first caching strategy for navigation requests.
- Service worker only registers when served over HTTP/HTTPS; gracefully skipped on file:// protocol.
- Meta tags for theme-color (light & dark), apple-mobile-web-app-capable, apple-mobile-web-app-title, and description.
- App is installable (Add to Home Screen) when served from a web server.

Validation and edge cases
- Prevent negative or zero amounts.
- Reject empty name or unselected tag.
- Allow decimal amounts with at most two digits.
- If budget is unset, treat it as 0 and show remaining as negative when expenses exist.
- When switching months (future enhancement), do not mix expenses across months.

Acceptance criteria
- User can add, edit, delete expenses and see totals update instantly.
- Remaining budget and total spent are always correct.
- Red warning state only when totalSpent > budget.
- Data persists after refresh and browser restart.
- Dark mode toggle changes the full UI theme.
- App works by opening index.html directly from the filesystem (no HTTP server needed).

Notes
- Keep implementation minimal and focused on the features above.
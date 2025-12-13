# Gold Ornament Pricing Calculator - AI Coding Guide

## Project Overview
This is a **single-file HTML/CSS/JavaScript calculator** for computing gold ornament prices with GST calculations. Two implementations exist:
- **`index.html`** (primary): Feature-rich multi-row calculator with wastage support and grand totals
- **`Gold Ornament Price Calcularo.html`** (reference): Simpler single-row version with different UI design

Both are standalone—no build process, dependencies, or backend needed. Files open directly in any browser.

## Architecture & Data Flow

### Single-Page Application Pattern
- **Input Form** → User enters ornament data and rates
- **Calculate Function** → Performs financial calculations locally
- **Results Display** → Shows breakdown in results box
- **Table Storage** → "Add to Table" button persists entries in memory (cleared on page reload)

### Key Data Structure (in `index.html`)
```javascript
items = []; // Global array storing all ornament records
window.lastItem = { 
  name, weight, purity,
  totalGoldBeforeGst, totalMaking, gstOnGold, finalPrice 
};
```

## Calculation Logic & Formulas

### Core Formula Sequence (`calculatePrice()` function)
```
1. Pure Gold Price = weight × purity × goldPrice
2. Total Gold Before GST = pure gold (wastage removed in current implementation)
4. Total Making = weight × makingCharges
5. GST on Gold = total gold before GST × (goldGst ÷ 100)
6. Final Price = total gold + total making + GST on gold
```

**Important**: Wastage is optional (defaults to 0) but represents gold loss during crafting—it's **added to cost**, not subtracted.

### Purity Handling
- Dropdown offers standard karatsL: 24K (99.9%), 22K (91.6%), 18K (75%)
- Custom purity toggle allows manual percentage entry
- Always stored as decimal (0-1) internally; multiply by 100 for display

## UI & Styling Patterns

### Gold Color Scheme
- Primary gold: `#b8860b` (dark goldenrod for headers, buttons)
- Background: `linear-gradient(to bottom right, #fff8dc, #ffd700)` (light cream to gold)
- Accent: `#fffef6` (off-white for containers)

### Responsive Grid Layout
```css
form { display: grid; grid-template-columns: repeat(auto-fill, minmax(220px, 1fr)); }
```
Form wraps automatically on small screens; table requires horizontal scroll below 600px.

### Grand Total Row Styling
- Row class: `.grand-total-row` with `background: #ffe9a3`
- Updated by `updateGrandTotals()` function after each table addition

## State Management & UI Interactions

### Form-to-Results Workflow
1. User fills form → clicks "Calculate"
2. Results box becomes visible (`display: block`)
3. "Add to Table" button appears
4. After adding to table, form resets and results hide

### Event Handlers
- `toggleCustomPurity()` on purity dropdown change
- `calculatePrice()` on "Calculate" button click
- `addToTable()` moves from `lastItem` to `items` array
- `updateGrandTotals()` recalculates footer values after each addition

### Persistent Data Challenge
- **Data is lost on page reload** (stored in RAM, not localStorage)
- Could enhance with `localStorage` to persist entries across sessions

## Common Modifications & Extension Points

### Adding a New Input Field
1. Add input in form: `<input id="newFieldId" ... />`
2. Extract in `calculatePrice()`: `const newField = parseFloat(document.getElementById("newFieldId").value)`
3. Update calculation formula
4. Add to `window.lastItem` object
5. Insert column in table header and `addCell()` call in `addToTable()`
6. Update `updateGrandTotals()` if needed

### Modifying Calculation Logic
- Edit formula in `calculatePrice()` function (lines ~170-200 in `index.html`)
- Wastage model differs from other calculators—confirm scope before changing

### Styling Changes
- Edit CSS variables in `<style>` block (lines ~10-100)
- Keep color theme consistent with gold palette
- Test responsive behavior on `@media (max-width: 600px)`

## File Conventions & Naming
- HTML file uses camelCase for IDs: `#ornamentName`, `#customPurity`
- Functions use camelCase: `calculatePrice()`, `updateGrandTotals()`
- CSS uses kebab-case for classes: `.table-wrapper`, `.grand-total-row`
- Global `items` array is lowercase; `window.lastItem` is camelCase

## Testing Scenarios
- **Basic flow**: Enter 10g gold at ₹5000/g, 22K purity, 0% waste → Calculate → Add to Table
- **Wastage impact**: Same inputs with 5% waste—final price should increase by ~5%
- **Custom purity**: Select "Custom", enter 90%, verify calculation adjusts
- **Multiple entries**: Add 3 different ornaments, verify grand totals sum correctly
- **Mobile responsiveness**: Table should scroll horizontally on phones; form should stack

## Known Limitations & Future Enhancements
- ⚠️ **No data persistence** between page reloads (consider adding localStorage)
- ⚠️ **No delete functionality** for table rows (rows persist until refresh)
- ⚠️ **No discount mechanism** (requested in original spec but not implemented)
- 💡 **Opportunity**: Add export to CSV or PDF for ornament records
- 💡 **Opportunity**: Add real-time price API integration (e.g., live gold rates)

## Debugging Tips
- Open browser DevTools (F12) → Console to inspect `items` array state
- Use `console.log(window.lastItem)` to verify calculation before table addition
- Check that purity dropdowns correctly toggle custom input visibility
- Verify number formatting with `.toFixed(2)` for currency display

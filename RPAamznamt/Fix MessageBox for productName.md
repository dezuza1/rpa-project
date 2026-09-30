# Fix Plan: Amazon Product Availability Checker

## Summary of Bugs Found

| # | Bug | Impact |
|---|-----|--------|
| 1 | `MessageBox` for productName uses `["productName"]` (string literal) instead of `[productName]` (variable) | Shows text "productName" instead of actual value |
| 2 | `MessageBox` for productPrice uses `["productPrice"]` (string literal) instead of `[productPrice]` (variable) | Shows text "productPrice" instead of actual value |
| 3 | `NGetText_4` (price) uses selector `idx='197'` — a positional index that breaks on any page change | Runtime crash or wrong price captured |
| 4 | `NGetText_5` (availability) targets a "Limited time deal" badge that may NOT exist on the page — no `ContinueOnError`, so the workflow **crashes** with a timeout error | Workflow crashes every run |

## Steps

### Step 1: Fix MessageBox for productName
Edit `Main.xaml` — change `Text="[&quot;productName&quot;]"` to `Text="[productName]"` so the dialog shows the actual scraped product name.

### Step 2: Fix MessageBox for productPrice
Edit `Main.xaml` — change `Text="[&quot;productPrice&quot;]"` to `Text="[productPrice]"` so the dialog shows the actual scraped price.

### Step 3: Add default-value Assign + ContinueOnError to the Price Get Text
Edit `Main.xaml` — insert an `Assign` activity immediately before `NGetText_4` that sets `productPrice = "N/A"`, then add `ContinueOnError="True"` to `NGetText_4`. This ensures:
- A clean default value on every loop iteration (no stale data from the previous product)
- No crash if the `idx='197'` selector misses the element

### Step 4: Add default-value Assign + ContinueOnError to the Availability Get Text
Edit `Main.xaml` — insert an `Assign` activity immediately before `NGetText_5` that sets `availability = "N/A"`, then add `ContinueOnError="True"` to `NGetText_5`. This ensures:
- A clean default value on every loop iteration
- No crash when the "Limited time deal" badge is absent from the page (the main cause of workflow failure)

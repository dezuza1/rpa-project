# Universal Selector Fix Plan — Amazon Product Availability Checker

## What We're Fixing
All three `Get Text` activities use hardcoded or position-based selectors that only work for the first product (Logitech). We're replacing them with class-based selectors that work for **any Amazon product search** and fixing the scope selectors so they match the browser on every loop iteration.

### Universal Selectors We'll Use
| Data | Old Selector (Broken) | New Selector (Universal) |
|---|---|---|
| Product Name | `<webctrl tag='H2' aria-label='MX Master 3S...'/>` | `<webctrl tag='H2'/>` (first H2 = first result title) |
| Price | `<webctrl tag='SPAN' idx='197'/>` | `<webctrl tag='SPAN' class='a-price-whole'/>` (Amazon's stable price class) |
| Offer/Deal | `<webctrl tag='SPAN' class='a-badge-text' css-selector='body>div>...>span>span>span>span'/>` | `<webctrl tag='SPAN' class='a-badge-text'/>` (class only, no path) |
| All Scopes | `<html title='Amazon.in : logitech mx master 3s' app='chrome.exe'/>` | `<html app='chrome.exe'/>` (any Chrome window) |

## Steps

### Step 1: Fix NGetText_3 selector — product name
Edit `Main.xaml` — replace the `FullSelectorArgument` of `NGetText_3` from the hardcoded `aria-label` H2 to a plain `<webctrl tag='H2'/>`. Also update its `ScopeSelectorArgument` to `<html app='chrome.exe'/>`.

### Step 2: Fix NGetText_4 selector — price  
Edit `Main.xaml` — replace `FullSelectorArgument` of `NGetText_4` from `idx='197'` to `class='a-price-whole'` (Amazon's dedicated CSS class for the price integer). Add the missing `ElementType="Text"` attribute. Update `ScopeSelectorArgument` to `<html app='chrome.exe'/>`.

### Step 3: Fix NGetText_5 selector — offer/deal
Edit `Main.xaml` — replace `FullSelectorArgument` of `NGetText_5` from the 26-level CSS path to just `<webctrl tag='SPAN' class='a-badge-text'/>`. Update `ScopeSelectorArgument` to `<html app='chrome.exe'/>`.

### Step 4: Also add ContinueOnError to NGetText_3
Edit `Main.xaml` — add `ContinueOnError="True"` to `NGetText_3` so the workflow doesn't crash if the H2 isn't loaded yet. (NGetText_4 and NGetText_5 already have it from the previous fix.)

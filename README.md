# QA Test Report: Sauce Demo (saucedemo.com)

Sample manual QA report. The target is a practice site that contains intentional bugs, so no real business was tested.

## Summary

| Item | Detail |
|---|---|
| Target | https://www.saucedemo.com |
| Test date | 31 October 2026 |
| Environment | Brave browser, Windows 10 |
| Method | Ran the same purchase flow with `standard_user` (baseline) and compared it against `problem_user` and `error_user` |
| Flow tested | Login, product list, sort, add to cart, cart, checkout form, overview, finish |
| Testing time | [fill in] |
| Findings | 7 bugs: 4 High, 3 Medium |

Severity scale: **High** = a core function is broken and the user cannot reach their goal. **Medium** = the function runs but gives a wrong or misleading result. **Low** = minor, does not block anything.

---

## Bug Reports

### BUG-01: Last Name field cannot be filled in
- **Severity:** High. The checkout form cannot be completed.
- **Users:** `problem_user`, `error_user`
- **Steps:**
  1. Log in as `problem_user`
  2. Add an item to the cart (use one whose button works), open the cart, click Checkout
  3. Type a name in First Name
  4. Click Last Name and type anything
- **Expected:** Characters appear in Last Name.
- **Actual:** Nothing appears in Last Name. Each typed character instead appears in the First Name field, replacing its content with a single letter. Pasting also fails and the text lands in First Name.
- **Additional difference:** With `problem_user`, Continue is blocked with the message "Last Name is required". With `error_user`, Last Name also cannot be filled (typing, paste, and browser history dropdown all fail), but Continue is **not** blocked and the flow proceeds with an empty Last Name.
- **Screenshot:** `screenshots/bug1-lastname.jpg`

### BUG-02: Checkout can be completed with an empty cart
- **Severity:** Medium. Empty orders go through to completion, which can pollute order data and load the PDF generation.
- **User:** `standard_user`
- **Steps:**
  1. Log in as `standard_user`
  2. Do not add any item
  3. Open the cart, click Checkout
  4. Fill in any name and postal code, click Continue, then Finish
- **Expected:** Checkout is refused because the cart is empty.
- **Actual:** The "Checkout: Complete!" page appears, and the generated PDF is empty.
- **Screenshot:** `screenshots/bug2-empty-cart.jpg`

### BUG-03: Sorting does not work
- **Severity:** Medium
- **Users:** `error_user` (with error popup), `problem_user` (silent failure)
- **Steps:**
  1. Log in as `error_user`
  2. On the product list, open the sort dropdown
  3. Select any option (Name A-Z, Name Z-A, Price low-high, Price high-low)
- **Expected:** The list is reordered.
- **Actual:** A popup appears: "Sorting is broken! This error has been reported to Backtrace." The order does not change. This happens for all four sort options. With `problem_user` the sort does nothing and shows no message.
- **Screenshot:** `screenshots/bug3-sort-error.jpg`

### BUG-04: Product detail page shows a different product
- **Severity:** High. Users see wrong information about what they are buying.
- **User:** `problem_user`
- **Steps:**
  1. Log in as `problem_user`
  2. Click a product on the list
- **Expected:** The detail page shows the product that was clicked.
- **Actual:** The detail page shows a different product with a different description. (`standard_user` shows correct details.)
- **Screenshot:** `screenshots/bug4-wrong-detail.jpg`

### BUG-05: Some "Add to cart" buttons do not work
- **Severity:** High
- **Users:** `problem_user`, `error_user`
- **Steps:**
  1. Log in as `problem_user`
  2. Click "Add to cart" on each product, one by one
- **Expected:** All six buttons add the item (the button changes to "Remove" and the cart badge increases).
- **Actual:** Some buttons do nothing: the cart badge does not increase. Affected products: [list which ones]. With `standard_user` all six work.
- **Screenshot:** `screenshots/bug5-add-to-cart.jpg`

### BUG-06: Postal code accepts letters with no validation
- **Severity:** Medium. Invalid data is stored in orders. Combined with BUG-02, junk orders can pass through the whole flow.
- **User:** `standard_user`
- **Steps:**
  1. Log in as `standard_user`, add an item, open the cart, click Checkout
  2. Fill First Name and Last Name
  3. Enter letters only in Zip/Postal Code
  4. Click Continue
- **Expected:** A validation message that the postal code is invalid.
- **Actual:** The form accepts it and proceeds to Checkout Overview.
- **Screenshot:** `screenshots/bug6-zip-letters.jpg`

### BUG-07: Finish button does not respond
- **Severity:** High. The order cannot be completed.
- **User:** `error_user`
- **Steps:**
  1. Log in as `error_user`, add an item, open the cart, click Checkout
  2. Fill in the form and click Continue
  3. On Checkout Overview, click Finish
- **Expected:** The "Checkout: Complete!" page appears.
- **Actual:** Nothing happens, the order cannot be completed.
- **Screenshot:** `screenshots/bug7-finish.jpg`

---

## Other Observations

- `problem_user`: all product images show the same dog picture.
- `problem_user` and `error_user`: "Remove" does not work on the product list page, only inside the cart.
- `error_user`: going back from Checkout Overview clears all form fields.

## Suggestions (not bugs)

- Add a show/hide (eye) icon to the password field.
- Form fields use placeholder text that disappears when the cursor enters the field. Persistent labels (First Name, Last Name, Postal Code) would be clearer.
- The checkout has no basic safeguards that a real store would have (validation of empty cart, shipping options, payment choices). This is expected for a demo site.

## Real-World Example

Beyond practice sites, I investigated a regression bug in the open-source editor Notepad3 (issue #5990): pasting multi-line text into the Replace with / Search string fields. I compared a stable version (7.26.426.1) against the newer one (7.26.602.1) and posted the findings in the issue: [link]

---

*This is a sample report written to show my testing and reporting format. It is not professional QA work history.*

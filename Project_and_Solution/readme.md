Perfect — this is exactly the right way to learn. Let me walk you through it **exactly like a hands-on lab**: every click, every property value, every screen you'll see in Visual Studio. Follow it top-to-bottom and don't skip steps.

We'll build the 8 items in the order I gave you earlier. By the end you'll have a **working button on All Sales Orders** that opens your Controller class and prints a message.

## Before you start — one-time setup checks

Open Visual Studio (Run as Administrator — this is important). Confirm:

1. **Dynamics 365 → Model Management → Create model** is available in the top menu. If not, your Dynamics 365 developer tools are not installed correctly.
2. You know your **Package/Model name**. If your team already created one, use it. Otherwise create a new one in Step 1a below.

## Step 1a — Create the model (skip if your team already has one)

1. Top menu: **Dynamics 365 → Model Management → Create model**
2. Fill in:
   * **Model name:** `ANSARI_FIN04ProFormaInvoiceNumberReservation`
   * **Model publisher:** Ansari
   * **Layer:** ISV (or CUS if your project standard says so)
   * **Model description:** Thailand Pro Forma / Customs Invoice Number Reservation
3. Click **Next**
4. On "Select package": choose **Create new package**
5. On "Select referenced packages": tick these (very important — if you miss one, your code won't compile later):
   * ApplicationSuite
   * ApplicationPlatform
   * ApplicationFoundation
   * Directory
   * WMSApp (for WHSShipmentTable)
   * Ledger
6. Click **Next → Finish**
7. Visual Studio creates the model folder. Wait for it to finish.

## Step 1b — Create the Visual Studio project

1. **File → New → Project**
2. Select **Finance Operations → Dynamics 365 Finance and Operations Project**
3. Name: `ANSARI_FIN04ProFormaInvoiceNumberReservation`
4. Location: default is fine
5. Click **OK**
6. Right-click the project in Solution Explorer → **Properties**
7. Set:
   * **Model:** `ANSARI_FIN04ProFormaInvoiceNumberReservation`
   * **Company:** `HSOT` (so debugging opens the right legal entity)
   * **Startup object:** leave blank for now
8. Click **OK**

You now have an empty project. Save it: **File → Save All** (Ctrl+Shift+S).

## Step 2 — Create the EDT `ANSARICustomsInvoiceId`

1. In Solution Explorer, **right-click the project → Add → New Item**
2. Left panel: **Data Types**
3. Middle panel: **Extended Data Type String**
4. Name: `ANSARICustomsInvoiceId`
5. Click **Add**
6. The EDT designer opens. In the Properties window (F4 if not visible), set:
   * **Name:** `ANSARICustomsInvoiceId`
   * **Label:** `Customs invoice number` (type it directly — Visual Studio will create the label ID later)
   * **Help text:** `Thai Customs invoice number reserved before invoice posting.`
   * **String size:** `20`
   * **Country region codes:** `THA`
7. **File → Save** (Ctrl+S)

Confirmation you did it right: expand the EDT node in Solution Explorer — you should see `ANSARICustomsInvoiceId` under **User Interface → Extended Data Types**.

## Step 3 — Create the Enum `ANSARIThaiCustomsInvoiceProcessMode`

1. **Right-click project → Add → New Item**
2. Left panel: **Data Types**
3. Middle panel: **Base Enum**
4. Name: `ANSARIThaiCustomsInvoiceProcessMode`
5. Click **Add**
6. The enum designer opens. In the tree on the left, right-click **Elements → New Element**. Do this **three times**.
7. Set each element's properties:

| Element | Name            | Label                     | Value |
| ------- | --------------- | ------------------------- | ----- |
| 1st     | Generate        | Generate Customs invoice  | 0     |
| 2nd     | View            | View Customs invoice      | 1     |
| 3rd     | GenerateAndPost | Generate and Post Invoice | 2     |

8. Click on the enum root and set:
   * **Label:** `Thai Customs invoice process mode`
   * **Help text:** `Controls Generate, View, Generate and Post process mode.`
9. **Ctrl+S** to save.

## Step 4 — Add ANSARICustomInv field to WHSShipmentTable (table extension)

1. In Solution Explorer, **right-click project → Add → New Item**
2. Left panel: **Data Model**
3. Middle panel: **Table Extension**
4. Name: `WHSShipmentTable.ANSARIExtension` (the `.ANSARIExtension` suffix is critical — this is how D365 knows it's your extension of the standard table)
5. Click **Add**
6. Visual Studio asks which table to extend. Type `WHSShipmentTable`, click it, click **OK**.
7. The extension designer opens. In the tree on the left, you'll see the standard fields greyed out (read-only — you can't touch them).
8. **Right-click Fields (under the extension node) → New → String**
9. Set properties on the new field:
   * **Name:** `ANSARICustomInv`
   * **Extended Data Type:** `ANSARICustomsInvoiceId` (pick from dropdown)
   * **Label:** leave blank (it inherits from the EDT)
   * **Allow edit:** `Yes`
   * **Allow edit on create:** `No`
   * **Mandatory:** `Yes`
10. Now add a field group. **Right-click Field Groups → New Group**.
    * **Name:** `ANSARIThaiCustomsInvoice`
    * **Label:** `Thai Customs Invoice`
11. Drag your new `ANSARICustomInv` field from Fields onto the field group.
12. **Ctrl+S**.

**Repeat the exact same 12 steps** for two more table extensions:

* `CustPackingSlipJour.ANSARIExtension` — same field, but set **Allow edit = No**
* `SalesParmLine.ANSARIExtension` — same field, same properties as CustPackingSlipJour

You now have three table extensions ready.

## Step 5 — Create the Context helper class

1. **Right-click project → Add → New Item**
2. Left panel: **Code**
3. Middle panel: **Class**
4. Name: `ANSARIThaiCustomsInvoiceContext`
5. Click **Add**
6. The code editor opens. Delete everything Visual Studio generated and paste this exactly:

```xpp
/// <summary>
/// Session-scoped whiteboard used by ANSARIThaiCustomsInvoiceService to pass 
/// the Customs invoice number into the SalesFormLetter_Invoice CoC extension.
/// TDS §5.6.5 supporting class.
/// </summary>
public class ANSARIThaiCustomsInvoiceContext
{
    private static ANSARICustomsInvoiceId               customsInvoiceId;
    private static ANSARIThaiCustomsInvoiceProcessMode  mode;

    public static void setCustomsInvoiceId(ANSARICustomsInvoiceId _value)
    {
        customsInvoiceId = _value;
    }

    public static ANSARICustomsInvoiceId getCustomsInvoiceId()
    {
        return customsInvoiceId;
    }

    public static void setMode(ANSARIThaiCustomsInvoiceProcessMode _value)
    {
        mode = _value;
    }

    public static boolean isGenerateAndPost()
    {
        return mode == ANSARIThaiCustomsInvoiceProcessMode::GenerateAndPost;
    }

    public static void clear()
    {
        customsInvoiceId = '';
        mode = ANSARIThaiCustomsInvoiceProcessMode::Generate;
    }
}
```

7. **Ctrl+S**. Then **Build → Build Solution** (Ctrl+Shift+B).
8. Check the **Output** window at the bottom. It should say **Build succeeded**. If not, read the error, fix the typo, build again.

## Step 6 — Create the Service class (empty skeleton for now)

We'll build it in two phases: first an empty skeleton so we can wire the buttons and see them work. Then we'll add real logic.

1. **Right-click project → Add → New Item → Code → Class**
2. Name: `ANSARIThaiCustomsInvoiceService`
3. Click **Add**
4. Replace all code with this **skeleton**:

```xpp
using System.Collections.Generic;

/// <summary>
/// Central business service. TDS §5.6.4.
/// SKELETON VERSION - prints Infolog messages so we can verify the 
/// button → controller → service chain works end-to-end.
/// Full logic is added after the skeleton compiles and buttons fire.
/// </summary>
public class ANSARIThaiCustomsInvoiceService
{
    public static void run(
        ANSARIThaiCustomsInvoiceProcessMode _mode,
        List                             _selectedSalesTableRecIds)
    {
        info(strFmt("ANSARIThaiCustomsInvoiceService::run fired. Mode = %1, records = %2",
                    enum2Str(_mode),
                    _selectedSalesTableRecIds.elements()));

        switch (_mode)
        {
            case ANSARIThaiCustomsInvoiceProcessMode::Generate:
                info("Would call generateCustomsInvoice() here.");
                break;

            case ANSARIThaiCustomsInvoiceProcessMode::View:
                info("Would call viewCustomsInvoice() here.");
                break;

            case ANSARIThaiCustomsInvoiceProcessMode::GenerateAndPost:
                info("Would call generateAndPostInvoice() here.");
                break;

            default:
                throw error(strFmt("Unknown mode: %1", _mode));
        }
    }
}
```

5. **Ctrl+S** and **Ctrl+Shift+B**. Confirm Build succeeded.

## Step 7 — Create the Controller class

1. **Right-click project → Add → New Item → Code → Class**
2. Name: `ANSARIThaiCustomsInvoiceController`
3. Click **Add**
4. Replace all code with this:

```xpp
using System.Collections.Generic;

/// <summary>
/// Entry point invoked by the three menu items on All Sales Orders.
/// TDS §5.6.3.
/// </summary>
public class ANSARIThaiCustomsInvoiceController
{
    protected ANSARIThaiCustomsInvoiceProcessMode mode;

    /// <summary>
    /// Called by the menu item click. This is the very first line of code
    /// that runs after the user clicks a button.
    /// </summary>
    public static void main(Args _args)
    {
        ANSARIThaiCustomsInvoiceController controller;
        List                            selectedRecIds;
        ListEnumerator                  enumerator;
        SalesTable                      salesTable;
        MultiSelectionHelper            helper;

        // ─── Guard: something must be selected ───
        if (!_args || !_args.record())
        {
            throw error("Please select at least one sales order.");
        }

        controller = new ANSARIThaiCustomsInvoiceController();

        // ─── Read the mode passed by the menu item ───
        // The menu item is configured with EnumTypeParameter and EnumParameter.
        controller.mode = _args.parmEnum();

        // ─── Collect all ticked sales orders from the grid ───
        selectedRecIds = new List(Types::Int64);
        helper         = MultiSelectionHelper::createFromCaller(_args.caller());
        salesTable     = helper.getFirst();

        while (salesTable.RecId != 0)
        {
            selectedRecIds.addEnd(salesTable.RecId);
            salesTable = helper.getNext();
        }

        // ─── Hand off to Service ───
        ANSARIThaiCustomsInvoiceService::run(controller.mode, selectedRecIds);
    }

    public ANSARIThaiCustomsInvoiceProcessMode parmMode(
        ANSARIThaiCustomsInvoiceProcessMode _mode = mode)
    {
        mode = _mode;
        return mode;
    }
}
```

5. **Ctrl+S** and **Ctrl+Shift+B**. Confirm Build succeeded.

## Step 8 — Create the three menu items

You need to add three **Action** menu items. Each one is identical except for one enum value.

### Step 8a — ANSARIGenerateCustomsInvoice

1. **Right-click project → Add → New Item**
2. Left panel: **User Interface**
3. Middle panel: **Action Menu Item**
4. Name: `ANSARIGenerateCustomsInvoice`
5. Click **Add**
6. The menu item designer opens. Set these properties in the Properties window:

| Property             | Value                            |
| -------------------- | -------------------------------- |
| Name                 | ANSARIGenerateCustomsInvoice        |
| Label                | Generate Customs invoice         |
| Object Type          | Class                            |
| Object               | ANSARIThaiCustomsInvoiceController  |
| Configuration Key    | (leave empty)                    |
| Country Region Codes | THA                              |
| Enum Type Parameter  | ANSARIThaiCustomsInvoiceProcessMode |
| Enum Parameter       | Generate                         |

7. **Ctrl+S**.

### Step 8b — ANSARIViewCustomsInvoice

Repeat 8a exactly, but change:

* **Name:** ANSARIViewCustomsInvoice
* **Label:** View Customs invoice
* **Enum Parameter:** View

### Step 8c — ANSARIGeneratePostCustomsInvoice

Repeat 8a exactly, but change:

* **Name:** ANSARIGeneratePostCustomsInvoice
* **Label:** Generate and Post Invoice
* **Enum Parameter:** GenerateAndPost

You now have three menu items. **Ctrl+Shift+B** to build. Confirm all green.

## Step 9 — Create the form extension for All Sales Orders

This is where you add the button group + three buttons to the standard `SalesTableListPage` form.

1. **Right-click project → Add → New Item**
2. Left panel: **User Interface**
3. Middle panel: **Form Extension**
4. Name: `SalesTableListPage.ANSARIExtension`
5. Click **Add**
6. Type `SalesTableListPage` in the picker, select it, click **OK**.
7. The form extension designer opens. The standard form design tree appears on the left, greyed out (read-only).

Now find where to put the button group:

8. In the tree, expand **Design → InvoiceHeaderActionPane → InvoiceHeaderActionPaneTab → Invoice** (this is the "Invoice" tab in the ribbon of the standard form).

9. **Right-click Invoice → New Control → Button Group**

10. A new button group appears at the bottom. Set its properties:
    * **Name:** `ANSARIThaiCustomsInvoiceButtonGroup`
    * **Caption:** `Thai Customs Invoice`
    * **Auto Declaration:** `No`

11. **Right-click your new button group → New Control → Menu Item Button**. Set:
    * **Name:** `ANSARIGenerateCustomsInvoice`
    * **Menu Item Type:** `Action`
    * **Menu Item Name:** `ANSARIGenerateCustomsInvoice` (pick from dropdown — if not visible, save all first)
    * **Data Source:** `SalesTable`
    * **Multi Select:** `Yes`
    * **Text:** `Generate Customs invoice` (leave blank if you want it to inherit from the menu item)

12. Repeat step 11 two more times inside the same button group:
    * Menu Item Button 2 → Menu Item Name = `ANSARIViewCustomsInvoice`
    * Menu Item Button 3 → Menu Item Name = `ANSARIGeneratePostCustomsInvoice`

13. **Ctrl+S** and **Ctrl+Shift+B**. Confirm all green.

## Step 10 — First real test: click the button

This is the moment you'll feel like you actually built something.

1. **Ctrl+Shift+B** one final time to make sure everything is compiled.
2. Right-click the project → **Deploy** (or **Synchronize database** if VS asks).
3. Open a browser to your D365 environment → sign in → make sure the current company is `HSOT`.
4. Navigate: **Accounts receivable → Orders → All sales orders**.
5. On the ribbon, click the **Invoice** tab.
6. You should see a new button group at the far right: **Thai Customs Invoice** — with three buttons underneath: **Generate Customs invoice**, **View Customs invoice**, **Generate and Post Invoice**.
7. Tick any sales order in the grid.
8. Click **Generate Customs invoice**.

Look at the yellow Infolog at the top of the screen. You should see:

```
✓ ANSARIThaiCustomsInvoiceService::run fired. Mode = Generate, records = 1
✓ Would call generateCustomsInvoice() here.
```

**If you see this, you're done with the wiring.** The button-to-controller-to-service chain is working end to end.

Try the other two buttons too — each should print its own message. This confirms the mode is being passed correctly.

## What to do if something goes wrong

| Symptom                                                                  | Most likely cause                             | Fix                                                                 |
| ------------------------------------------------------------------------ | --------------------------------------------- | ------------------------------------------------------------------- |
| Button group doesn't appear on the form                                  | Deployment didn't refresh                     | Full IIS reset + hard refresh browser (Ctrl+F5)                     |
| Button appears but clicking does nothing                                 | Menu item's Object property is wrong          | Open the menu item, verify Object = ANSARIThaiCustomsInvoiceController |
| Infolog says "no record selected" but you ticked a row                   | MultiSelectionHelper couldn't read the caller | Confirm the Menu Item Button property "Data Source" = SalesTable    |
| Compilation error "type ANSARIThaiCustomsInvoiceProcessMode does not exist" | Enum wasn't saved before you referenced it    | Ctrl+S on the enum, then rebuild                                    |
| Build fails on WHSShipmentTable.ANSARIExtension                             | WMSApp reference missing                      | Right-click project → References → Add → WMSApp                     |

## What comes next (Phase 2)

Right now your service just prints "would call X here." That was intentional — you wanted the button wiring first. Once you're comfortable that Step 10 works reliably, you replace the three `info()` lines inside `ANSARIThaiCustomsInvoiceService::run` with real logic:

1. Build the SalesParmTable staging (I'll give you that code next)
2. Validate lines share the same Customs invoice number
3. Call the three action methods (`generateCustomsInvoice`, `viewCustomsInvoice`, `generateAndPostInvoice`)
4. Create the CoC on `SalesFormLetter_Invoice` (§5.6.5) for the InvoiceId override

You're not afraid of these — you've already seen the code shape in my earlier reply. You just need the button-wiring foundation first, which you now have.

## A word before you start clicking

Do the steps in exact order. **Do not skip Step 10.** The infolog message is your safety net — it proves the plumbing works before you add complicated logic. Every professional D365 developer builds this way: wire the click, prove it fires, then fill in the logic. You'll never be lost this way, because if something breaks later you know at what layer it broke.

Would you like me to (a) walk through **Phase 2** (replacing the info() lines with real logic) with the same step-by-step level of detail, (b) write a companion **checklist HTML article** in your `.ac-body` template that you can keep open while you build, or (c) explain the **SalesParmTable / SalesParmLine staging pattern** in depth since that's the trickiest part of Phase 2?



Perfect next step — this is exactly how you build confidence: verify each layer works before moving on. Right now you're printing the *count* of selected orders; let's print the actual **SalesId** of each one so you can visually confirm the multi-select is working correctly.

## The code — add this right after your existing `info` line

```xpp
public static void run(
    ANSARIThaiCustomsInvoiceProcessMode _mode,
    List                             _selectedSalesTableRecIds)
{
    info(strFmt("ANSARIThaiCustomsInvoiceService::run fired. Mode = %1, records = %2",
                enum2Str(_mode),
                _selectedSalesTableRecIds.elements()));

    // ─── NEW: print each selected sales order ───
    ListEnumerator enumerator;
    SalesTable     salesTable;
    RecId          currentRecId;
    int            lineNumber = 0;

    enumerator = _selectedSalesTableRecIds.getEnumerator();

    while (enumerator.moveNext())
    {
        lineNumber++;
        currentRecId = enumerator.current();

        // Look up the SalesTable row using the RecId we collected
        salesTable = SalesTable::findRecId(currentRecId);

        if (salesTable.RecId)
        {
            info(strFmt("  [%1] SalesId = %2 | Customer = %3 | Status = %4",
                        lineNumber,
                        salesTable.SalesId,
                        salesTable.CustAccount,
                        enum2Str(salesTable.SalesStatus)));
        }
        else
        {
            warning(strFmt("  [%1] RecId %2 not found in SalesTable", 
                           lineNumber, 
                           currentRecId));
        }
    }

    info(strFmt("Total sales orders processed: %1", lineNumber));

    // ─── existing switch statement stays exactly as it was ───
    switch (_mode)
    {
        case ANSARIThaiCustomsInvoiceProcessMode::Generate:
            info("Would call generateCustomsInvoice() here.");
            break;

        case ANSARIThaiCustomsInvoiceProcessMode::View:
            info("Would call viewCustomsInvoice() here.");
            break;

        case ANSARIThaiCustomsInvoiceProcessMode::GenerateAndPost:
            info("Would call generateAndPostInvoice() here.");
            break;

        default:
            throw error(strFmt("Unknown mode: %1", _mode));
    }
}
```

## What each new line does (in plain English)

Let me walk you through it so you understand it, not just paste it.

### `ListEnumerator enumerator;`

A `List` in X++ is a collection you can't loop over directly with a `for` — you need a helper object called an **enumerator** to walk through it one item at a time. Think of it like a bookmark that moves forward through the list.

### `enumerator = _selectedSalesTableRecIds.getEnumerator();`

You ask the List for its enumerator. Now `enumerator` is pointing at *before* the first item (not at the first item — this is important).

### `while (enumerator.moveNext())`

`moveNext()` does two things at once:

1. Advances the bookmark forward by one position
2. Returns `true` if there was something to move to, `false` if we've fallen off the end

So this loop keeps running until we've walked through every RecId in the List.

### `currentRecId = enumerator.current();`

Read the value the bookmark is currently pointing at. This is one `RecId` — a 64-bit integer that uniquely identifies one row in `SalesTable`.

### `salesTable = SalesTable::findRecId(currentRecId);`

D365 tables have a built-in static method `findRecId` on almost every table. Give it a RecId, it returns the whole row. This is the standard X++ pattern for "fetch this specific record by its unique ID."

### The `if (salesTable.RecId)` check

`findRecId` returns an empty buffer if the RecId doesn't exist (rare, but possible if someone deleted the row between the click and the code running). We check `RecId` because it's the field D365 populates automatically — if it's zero, the row wasn't found. Never assume a lookup succeeds; always check.

### The `info(strFmt(...))` line

Same pattern you already know. We're building a formatted string with the line number, the human-readable `SalesId` (like "000001"), the customer account, and the sales order status.

## Test it now

1. **Ctrl+Shift+B** to build.
2. Right-click project → **Deploy**.
3. Refresh your browser (Ctrl+F5).
4. Go to **Accounts receivable → Orders → All sales orders**.
5. **Tick three or four sales orders** — hold Ctrl+click to multi-select in the grid.
6. Click **Invoice tab → Thai Customs Invoice → Generate Customs invoice**.

You should see something like this in the Infolog:

```
✓ ANSARIThaiCustomsInvoiceService::run fired. Mode = Generate, records = 3
✓   [1] SalesId = SO-000045 | Customer = C-00012 | Status = Backorder
✓   [2] SalesId = SO-000048 | Customer = C-00017 | Status = Backorder
✓   [3] SalesId = SO-000051 | Customer = C-00012 | Status = Delivered
✓ Total sales orders processed: 3
✓ Would call generateCustomsInvoice() here.
```

**If you see this, you've just proven three things:**

1. Multi-select is being captured correctly from the grid
2. Your RecId → SalesTable lookup works
3. Your code can now identify exactly which orders the user picked

That's the foundation everything else in the Service class will build on.

## A small learning bonus — the `list.pack()` shortcut

If you ever want a **quick one-line dump** of what's in a List (for debugging only), you can do:

```xpp
info(con2Str(_selectedSalesTableRecIds.pack()));
```

`pack()` turns the List into a container, and `con2Str` prints it as a comma-separated string. Useful when you're not sure what's in the list at all. But don't use it in production code — the formatted loop above is much more readable for a reviewer.

## What to do next once this works

Once you can visually confirm every selected order is being picked up correctly, the next natural step is:

1. **Filter out ineligible orders** — orders where `SalesType == ReturnItem`, or where the customer's country isn't Thailand
2. **Group them by Customs invoice number** — different orders may have been reserved under different numbers; your code needs to detect that
3. **Build the SalesParmTable staging** — the standard D365 pattern for "I want to post these orders together"

Would you like me to (a) walk through **filtering** as the next tiny step (5 minutes of code), (b) explain **why we need SalesParmTable at all** with a story before we write the code, or (c) show you how to **set a breakpoint on `enumerator.current()`** and step through in Visual Studio's debugger so you can watch the RecIds arrive one by one?

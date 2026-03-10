# AI REPORT BUILDER PROTOCOL

## Purpose
This file is for GitHub Copilot only.
When the user asks for any Odoo report, read this file first and follow it exactly.

## Read Order
1. Read this file.
2. Read AI_REPORT_LEARNINGS.md.
3. Inspect similar reports in the target module or core addons.
4. Build.
5. Test.
6. If a new working pattern is discovered, append it to AI_REPORT_LEARNINGS.md.

## Fixed Report Structure
Use this structure unless the existing module already follows another valid pattern:
- __manifest__.py
- __init__.py
- models/*.py
- views/*_views.xml
- report/*_paperformat.xml
- report/*_action.xml
- report/*_template.xml
- security/ir.model.access.csv

## Hard Rules
- Do not add new database fields unless the user explicitly requires persistent storage.
- Prefer model methods for report data extraction.
- Use ir.actions.report, not legacy report tag.
- Use `qweb-pdf` by default; use `qweb-html` only when the user explicitly wants HTML preview/output.
- Put paperformat before action before template in manifest data.
- Use full external IDs with module prefix.
- Use safe formatting like '{:,.2f}'.format(value).
- Do not use &nbsp; in XML. Use &#160;.
- For single-record methods, start with self.ensure_one().
- If a related model may be missing, check `if 'model.name' in self.env` first.
- If customizing an existing standard report, prefer `inherit_id` + `xpath` over copying the whole template.
- Use `web.external_layout` when standard header/footer/company info is desired; use `web.basic_layout` for tighter custom layouts.
- Keep `web.html_container` around the report document loop.

## Build Algorithm
### 1) Understand
Extract:
- source model
- output format
- required fields
- related data sources
- calculated values
- design source (image/pdf/example)
- print entry point (button/menu/both)

### 2) Inspect Existing Code
Before writing code:
- search the same module for existing report patterns
- search core/addons for same model report examples
- reuse naming style already used in that module
- prefer the workspace pattern over generic examples

### 3) Build Data Layer
In model method:
- return dict only
- initialize safe defaults
- search related records with primary lookup then fallback lookup
- avoid crashing when optional modules are absent

Pattern:
```python
def _get_report_data(self):
    self.ensure_one()
    vals = {
        'amount': 0.0,
        'name': self.display_name or '',
    }
    if 'related.model' in self.env:
        related = self.env['related.model'].search([], limit=1)
        if related:
            vals['amount'] = related.amount or 0.0
    return vals
```

### 4) Build Action
Required fields:
- name
- model
- report_type = qweb-pdf or qweb-html based on request
- report_name
- report_file for PDF reports and when matching an existing working pattern
- binding_model_id
- binding_type = report
- paperformat_id when custom margins are needed
- print_report_name when the output filename should include the record name

### 5) Build Template
Default template pattern:
```xml
<template id="report_template_id">
    <t t-call="web.html_container">
        <t t-foreach="docs" t-as="o">
            <t t-call="web.basic_layout">
                <t t-set="vals" t-value="o._get_report_data()"/>
                <div class="page">
                    <!-- content -->
                </div>
            </t>
        </t>
    </t>
</template>
```

Useful variants:
- full custom report: `web.html_container` -> `web.basic_layout` or `web.external_layout`
- inherited standard report: `<template inherit_id="module.template_id">` + targeted `xpath`
- labels/barcodes: use custom paperformat + barcode widget + very small margins

Use these choices:
- `web.external_layout`: invoices, reminders, letters, documents needing standard company frame
- `web.basic_layout`: highly custom pages, labels, tight printable designs
- `inherit_id` + `xpath`: when user wants to hide/show/replace parts of an existing Odoo report

### 6) Build Entry Point
If user wants print from form:
- inherit the form view
- insert button inside header
- button type = object
- invisible = not id

If user wants to modify an existing built-in report only:
- do not create a new report unless necessary
- inherit the original QWeb template
- use stable xpath targets like `@name`, `@t-field`, or `hasclass()`
- prefer `position="attributes"` to hide existing nodes with `t-if="False"`
- use `position="replace"` only when the node must be removed or rebuilt

### 7) Test Before Delivery
Must verify:
- module upgrades cleanly
- env.ref(action_xmlid) works
- data method returns expected dict
- PDF renders successfully
- no new relevant errors introduced

## Quick Checklist
- correct depends
- correct manifest order
- correct xml ids
- no invalid XML entities
- no formatLang in qweb-pdf
- no unnecessary fields
- button points to existing method
- report_name matches template id exactly
- paperformat field names use margin_top/margin_bottom/margin_left/margin_right
- if using html output, report_type is qweb-html intentionally
- if inheriting a report, xpath targets are specific and stable
- if using static images, path is `/module_name/static/src/img/file.png`
- if using barcode widget, test the data source and widget options

## Self-Update Rule
If a new pattern is proven to work in a real implementation:
1. add a short entry to AI_REPORT_LEARNINGS.md
2. include module path
3. include what failed before
4. include the final working pattern
5. mark it as TESTED

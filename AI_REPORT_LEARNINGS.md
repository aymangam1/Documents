# AI REPORT LEARNINGS

This file is for GitHub Copilot only.
Store only short, tested lessons here.
No long explanations.

## Format
- Date:
- Module/Path:
- Topic:
- Wrong:
- Correct:
- Status: TESTED

---

## 2026-03-11
- Module/Path: promaster-demo/hr_contract_offer_report
- Topic: Data source for salary parts
- Wrong: adding custom fields on hr.contract for report-only values
- Correct: read latest hr.payslip/hr.payslip.line and compute values in a method
- Status: TESTED

- Module/Path: promaster-demo/hr_contract_offer_report
- Topic: Optional payroll dependency
- Wrong: direct use of hr.payslip on databases where payroll may not exist
- Correct: check `if 'hr.payslip' not in self.env:` and return safe defaults
- Status: TESTED

- Module/Path: promaster-demo/hr_contract_offer_report
- Topic: Report action definition
- Wrong: legacy report XML tag
- Correct: use `ir.actions.report` record with report_name, report_file, binding_model_id, binding_type
- Status: TESTED

- Module/Path: promaster-demo/hr_contract_offer_report
- Topic: Manifest order
- Wrong: action before paperformat
- Correct: paperformat -> action -> template -> views -> security
- Status: TESTED

- Module/Path: promaster-demo/hr_contract_offer_report
- Topic: Number formatting in QWeb PDF
- Wrong: formatLang()
- Correct: `'{:,.2f}'.format(value)`
- Status: TESTED

- Module/Path: promaster-demo/hr_contract_offer_report
- Topic: XML entity
- Wrong: `&nbsp;`
- Correct: `&#160;`
- Status: TESTED

- Module/Path: promaster-demo/hr_contract_offer_report
- Topic: External IDs
- Wrong: `model_hr_contract`
- Correct: `hr_contract.model_hr_contract`
- Status: TESTED

- Module/Path: promaster-demo/hr_contract_offer_report
- Topic: Single record report methods
- Wrong: no record-size guard
- Correct: start with `self.ensure_one()`
- Status: TESTED

- Module/Path: promaster-demo/hr_contract_offer_report
- Topic: Form button visibility
- Wrong: print button shown on unsaved record
- Correct: use `invisible="not id"`
- Status: TESTED

- Module/Path: promaster-demo/hr_contract_offer_report
- Topic: Logo in template
- Wrong: unguarded image rendering
- Correct: `t-if` + `image_data_uri(o.company_id.logo)`
- Status: TESTED

- Module/Path: user examples / app_one.property_report_template
- Topic: HTML reports
- Wrong: forcing PDF for every report
- Correct: use `qweb-html` when the user wants browser preview or HTML output
- Status: TESTED

- Module/Path: user examples / app_one.property_report_template
- Topic: Layout selection
- Wrong: assuming `web.basic_layout` is always best
- Correct: `web.external_layout` is useful when standard company header/footer is desired
- Status: TESTED

- Module/Path: user examples / subscription_package.report_label_card
- Topic: Label and barcode reports
- Wrong: using standard A4 margins for labels
- Correct: create a dedicated `report.paperformat` with custom size or zero margins and use barcode widget options
- Status: TESTED

- Module/Path: user examples / subscription_package.report_label_card
- Topic: Barcode rendering
- Wrong: rendering barcode as plain text or static image only
- Correct: use `t-options="{'widget': 'barcode', ...}"` with tested source value
- Status: TESTED

- Module/Path: user examples / purchase.report_purchaseorder_document inherit
- Topic: Editing built-in reports
- Wrong: copying the whole original report just to hide a few columns
- Correct: inherit the original template and change only the needed nodes with `xpath`
- Status: TESTED

- Module/Path: user examples / purchase.report_purchaseorder_document inherit
- Topic: Hiding existing columns
- Wrong: broad xpath or full template rewrite
- Correct: use stable targets like `@name`, `@t-field`, or exact cell expressions and apply `position="attributes"` with `t-if="False"` or `position="replace"`
- Status: TESTED

- Module/Path: user examples / custom_sale_view.report_saleorder_custom
- Topic: Static images in reports
- Wrong: hardcoding external URLs for logos used by the module
- Correct: use module static paths like `/module_name/static/src/img/file.png`
- Status: TESTED

- Module/Path: user examples / custom_sale_view.report_saleorder_custom
- Topic: Dynamic print filename
- Wrong: fixed downloaded filename for all records
- Correct: use `print_report_name` when the report filename should include record data
- Status: TESTED

- Module/Path: user examples / web.external_layout_striped inherit
- Topic: Layout customization
- Wrong: cloning external layout to change a small area
- Correct: inherit the layout and patch only the required nodes with focused `xpath`
- Status: TESTED

- Module/Path: user examples / payment reminder bilingual layout
- Topic: Bilingual split layout
- Wrong: mixing Arabic and English in one undirected block
- Correct: split sections into separate containers/columns and apply explicit RTL/LTR direction per side
- Status: TESTED

## Add New Items
When a new report is completed successfully, append only compact entries in the same format.

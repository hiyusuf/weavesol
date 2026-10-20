# WeaveSol - UX review rules

Read by the `review-ux` skill. Intended behaviour: `docs/project-documents/03-App-Flow-Document.md`,
`04-Design-Brief.md` and each `docs/forms/F-xxx` spec. Report any deviation.

## Keyboard first
- Every entry screen can be completed without a mouse: Enter moves to the next field in spec order, look-ups open by key, grids add rows on Enter, Save and Print by key, Esc backs out. Flag any field that traps focus or needs the mouse.
- Key hints show on buttons and in the status bar.
- Focus is always visible.

## Roles
- Starter roles: Owner / Admin, Accountant, Billing Operator, Purchase / Stock Clerk, Job-Work Clerk, Production Clerk, Counter Sales, Viewer. A user sees only what their rights allow, and the server blocks the rest.

## Entry flows
- Defaults match the spec (date = last used or today, series, party defaults such as agent and transporter).
- Credit-limit, hold and stock warnings say the number and what the user can do ("Save anyway?" only where the setting allows).
- Unsaved changes warn before closing a window or signing out.
- After Save the user sees the document number and is offered Print.
- Edit / cancel / delete rules are explained when blocked (lock date, used master, GST invoice can only be cancelled).

## Wording
- Labels follow SilkSol captions; business words from `docs/GLOSSARY.md`. No internal codes or database IDs on screen.
- Error messages say what is wrong and what to do.

## Accessibility
- Every input has a visible label; required fields marked; errors linked to the field; grids use `role="grid"`.

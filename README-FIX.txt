FIX — Inventory tab not updating when you switch month tabs
================================================================

WHAT WAS ACTUALLY WRONG (different bug than the last one)
Switching between month tabs (June, July, etc.) refreshed the Expenses,
Shows, Employees, and Dashboard tabs — but never told the Inventory tab
to redraw itself. So Inventory just kept showing whatever it last
rendered, regardless of which month tab you had open. That's why a June
purchase looked like it was "in July too" — you were looking at stale
June data while the tab said July.

This was a separate bug from the timezone issue fixed earlier. That fix
was correct and still applies; this was a second, unrelated bug in the
same area.

FIX
Every action that can change which month you're viewing — switching
tabs, or adding an entry that auto-jumps you to a different month — now
runs through one shared function that refreshes ALL tabs (Expenses,
Shows, Employees, Inventory, Dashboard) together, not a hand-picked
list per action. That was the actual root cause: each place that
changed the month had its own separate list of "which tabs to refresh,"
and it's easy for one of those lists to miss a tab. Now there's exactly
one list, used everywhere, so this can't happen again in some other tab
later.

WHAT TO CHECK
Your June entry should now show up correctly under June only, and
disappear from July's view. If it's still showing wrong after this
deploy, tell me exactly: which tab were you on, what date is on the
entry itself (open it/check), and does a hard refresh of the page fix
it — that'll tell me if it's a caching issue instead.

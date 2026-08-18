FIX — Inventory (and Expenses/Shows) landing in the wrong month
===================================================================

ROOT CAUSE
The date that auto-fills when you open the Inventory (or Expenses/Shows)
form was being calculated using UTC time instead of your actual local
time. You're in a US timezone, which is several hours behind UTC — so
for a chunk of every evening/night, UTC has already rolled over to the
next day (or next month), even though it's still, say, June 30th where
you are. That's exactly why entries were landing a day/month off.

Important: this only affected the auto-filled default date shown when
you open the form. If you ever manually clicked a date in the picker,
that value was always correct — the bug was purely in what got
pre-filled for you.

FIXED
The app now uses your browser's actual local date everywhere it decides
"what day/month is it right now" — for Inventory, Expenses, and Shows.
Going forward, whatever month tab you're on, new entries will correctly
default into that month.

WHAT TO DO ABOUT ENTRIES ALREADY IN THE WRONG MONTH
This fix only prevents it going forward — it can't retroactively know
which existing entries were misdated. There's no "edit date" button yet
(only delete), so to fix any entry that landed in the wrong month:
1. Go to the month it wrongly landed in (e.g. July) and find it.
2. Delete it.
3. Switch to the correct month (e.g. June) and re-add it, using the
   date picker to manually confirm the exact date.

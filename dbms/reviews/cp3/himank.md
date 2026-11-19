# SplitBuddy CP3 check — Himank

## Check

Verify that custom expense shares add up to the total and persist after reload.

## Steps run

On 18 November 2026, an assistant ran this check on Himank’s behalf using the local app and a fresh PostgreSQL demo database. As Asha, the assistant opened **Weekend trip → Add expense**, entered `Custom split check` for ₹100.00, chose **Custom**, and selected Asha and Bina.

1. Entered ₹60 for Asha and ₹30 for Bina, then tried to save.
2. Changed Bina's share to ₹40 and saved.
3. Reloaded as Asha, opened the group and expanded **View shares** in History.

## Observed result

The ₹60 + ₹30 attempt showed: “Choose group members and make the shares add up to the total.” History remained unchanged. The ₹60 + ₹40 attempt showed “Expense saved.” After reloading, History contained `Custom split check` for ₹100.00 and showed Asha at ₹60.00 and Bina at ₹40.00. This was an isolated demo, not a test of deployed production data.

## Code traced

In [dbms/04_queries.sql](../../04_queries.sql), W4 validates group membership, participant and share counts, non-negative shares, and that their sum equals the total before inserting the expense and shares. R6 retrieves the stored members and share amounts for an authorized group viewer. The app's custom-expense handler calls W4 and shows the validation error when no rows are saved.

## Dream11 R13 context

The original `NOT IN` subquery includes NULL wallet references. SQL's three-valued logic makes unmatched comparisons unknown, so the original audit returned zero rows. The corrected audit uses `NOT EXISTS` and checks for a successful entry-fee debit. The team compared it with an independent oracle; both returned 192 rows on the seed data. See the [R13 analysis](https://github.com/Mradul2006-Dev/DE_Project_2026/tree/main/analysis/r13) and [equivalence results](https://github.com/Mradul2006-Dev/DE_Project_2026/blob/main/benchmarks/results/optimized/equivalence.txt). The interpretation assumes a successful transaction is the required debit.

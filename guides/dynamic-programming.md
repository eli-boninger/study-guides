## Dynamic programming

Combining solutions to subproblems to solve a larger problem, primarily when different subproblems' parts overlap such that we need to avoid computing the same thing twice.

Subproblem solutions are saved in a table which we can reference rather later.

Steps (take directly from CLRS page 362):
1. Characterize the structure of an optimal solution.
2. Recursively define the value of an optimal solution.
3. Compute the value of an optimal solution, typically in a bottom-up fashion.
4. Construct an optimal solution from computed information.

*Optimal Sub-structure*: optimal solutions for a problem are made up of optimal solutions of the subproblems
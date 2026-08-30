# Final Constrained Pareto Set

`pareto_front_constrained.csv` stores the **100 surrogate non-dominated candidates** produced by the final safeguarded NSGA-II search.

The final search is bounded to the empirical per-variable extrema of the 285 retained LTspice rows and applies a 1 µV non-negative ripple objective floor. These rows are candidate designs predicted by the corrected surrogate; they are not 100 independently LTspice-validated or hardware-validated designs.

Representative minimum-ripple, maximum surrogate-predicted-efficiency, and balanced candidates are discussed in the manuscript and reproduced from this stored set.

# Reference LTspice Circuit

`Draft1.asc` is the common/reference buck-converter schematic used by the automated switching-level sweep.

Nominal reference values include Vin=12 V, L1=10 µH, C1=100 µF, Rload=2.5 Ω, a fixed-duty PWM near 5/12, a voltage-controlled switch model and a simplified diode model.

The automation workflow edits L, C, switching frequency/load and switch Ron for matched design coordinates and uses the original 4–5 ms measurement window documented in `../../notebooks/spice_automation.ipynb`.

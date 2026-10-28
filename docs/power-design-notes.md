# Power revision

AP63205WU-7 / AP63203WU-7 from Diodes datasheet Figure 21 and Tables 2/3. Two diode-OR inputs prevent USB backfeed; USB target enable JP1 is normally open. No fixed source priority.

Regulated 12 V bench adapter only. USB operation requires a verified total host current budget, including debugger. Current limit of the buck IC is not permission to draw 2 A from USB.

Inductors: 4.7 uH / 3.9 uH, Isat >=4 A and Irms >=2 A, low DCR; footprint is provisional until exact manufacturer part is selected. Capacitors require effective capacitance verification under DC bias. Check diode losses, fuse curves, PCB thermal performance and UVLO margin.

Unselected exact inductor, fuse, jack and capacitor MPNs block production release. No PCB routing or physical testing performed.

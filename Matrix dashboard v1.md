``` 
!pip install shapely  # Uncomment if using Colab

import matplotlib.pyplot as plt
import matplotlib.patches as patches
from matplotlib.patches import Polygon
from shapely.geometry import box
from shapely.ops import unary_union
from collections import defaultdict
import random

=== Generate 100 random risks ===
risks = [(i + 1, random.randint(1, 5), random.randint(1, 6)) for i in range(100)]

=== Define Top-Heavy Material Triangle (Impact 6 = top row) ===
material_cells = [
    (x, y)
    for y in range(6, 1, -1)
    for x in range(7 - y, 6)
]

=== Axis Labels ===
x_labels = [
    "1- Rare\n(>20 yrs)",
    "2- Unlikely\n(10–20 yrs)",
    "3- Possible\n(5 yrs)",
    "4- Likely\n(1–5 yrs)",
    "5- Very Likely\n(<1 yr)"
]

y_labels = [
    "5 – Major\n(> $2bn)",
    "4 – Significant\n($1bn–2bn)",
    "3 – Moderate\n($500m–1bn)",
    "2 – Minor\n($200m–500m)",
    "1 – Low\n($100m–200m)",
    "0 – Extr. Low\n(< $100m)"
]

=== Helper: generate outer outline and FLIP vertically ===
def get_outline_from_material_cells(material_cells):
    cell_polys = [box(x - 1, y - 1, x, y) for (x, y) in material_cells]
    merged = unary_union(cell_polys)

    if hasattr(merged, 'exterior'):
        coords = list(merged.exterior.coords)
    else:
        coords = list(merged.geoms[0].exterior.coords)

    Flip Y = 6 - y to match plot
    flipped = [(x, 6 - y) for (x, y) in coords]
    return flipped

=== Setup plot ===
fig, ax = plt.subplots(figsize=(16, 9))

=== Draw grid ===
for i in range(6):
    ax.axhline(i, color='black', linewidth=0.5)
    ax.axvline(i, color='black', linewidth=0.5)

=== Draw gray cells (flipped vertically) ===
for x, y in material_cells:
    display_y = 6 - y
    ax.add_patch(patches.Rectangle((x - 1, display_y), 1, 1, color='gray', alpha=0.4))
    ax.text(x - 0.5, display_y + 0.5, 'Material', ha='center', va='center', fontsize=7, color='white')

=== Draw RED outline (flipped coords) ===
outline_coords = get_outline_from_material_cells(material_cells)
polygon = Polygon(outline_coords, closed=True, fill=False, edgecolor='red', linewidth=2)
ax.add_patch(polygon)

=== Draw risk bubbles (top-left aligned, flipped Y) ===
cell_risks = defaultdict(list)
for rid, x, y in risks:
    cell_risks[(x, y)].append(rid)

bubble_radius = 0.08
max_per_row = 6
spacing_x = 0.15
spacing_y = 0.18

for (x, y), rids in cell_risks.items():
    display_y = 6 - y
    is_material = (x, y) in material_cells
    edge_color = 'red' if is_material else 'black'

    for idx, rid in enumerate(rids):
        col = idx % max_per_row
        row = idx // max_per_row
        px = x - 1 + spacing_x * col + 0.1
        py = display_y + spacing_y * row + bubble_radius

        circle = plt.Circle((px, py), bubble_radius, facecolor='white', edgecolor=edge_color, lw=1.2)
        ax.add_patch(circle)
        ax.text(px, py, str(rid), ha='center', va='center', fontsize=6.5)

=== Axis Labels and Layout ===
ax.set_xticks([i + 0.5 for i in range(5)])
ax.set_xticklabels(x_labels, fontsize=9, ha='center', rotation=30)
ax.set_yticks([i + 0.5 for i in range(6)])
ax.set_yticklabels(list(reversed(y_labels)), fontsize=9)

=== Flip Y-Axis to match high-impact top ===
ax.set_ylim(6, 0)
ax.set_xlim(0, 5)
ax.set_aspect('equal')
ax.tick_params(left=False, bottom=False)
ax.set_title("Flipped-Upward Material Triangle with Correct Red Outline", fontsize=14, weight='bold')

plt.subplots_adjust(left=0.05, right=0.75, top=0.95, bottom=0.1)
plt.savefig("risk_matrix_flipped_outline.png", dpi=300)
plt.show()
]

  

y_labels = [

"5 – Major\n(> $2bn)",

"4 – Significant\n($1bn–2bn)",

"3 – Moderate\n($500m–1bn)",

"2 – Minor\n($200m–500m)",

"1 – Low\n($100m–200m)",

"0 – Extr. Low\n(< $100m)"

]

  

=== Helper: generate outer outline and FLIP vertically ===

def get_outline_from_material_cells(material_cells):

cell_polys = [box(x - 1, y - 1, x, y) for (x, y) in material_cells]

merged = unary_union(cell_polys)

  

if hasattr(merged, 'exterior'):

coords = list(merged.exterior.coords)

else:

coords = list(merged.geoms[0].exterior.coords)

  

Flip Y = 6 - y to match plot

flipped = [(x, 6 - y) for (x, y) in coords]

return flipped

  

=== Setup plot ===

fig, ax = plt.subplots(figsize=(16, 9))

  

=== Draw grid ===

for i in range(6):

ax.axhline(i, color='black', linewidth=0.5)

ax.axvline(i, color='black', linewidth=0.5)

  

=== Draw gray cells (flipped vertically) ===

for x, y in material_cells:

display_y = 6 - y

ax.add_patch(patches.Rectangle((x - 1, display_y), 1, 1, color='gray', alpha=0.4))

ax.text(x - 0.5, display_y + 0.5, 'Material', ha='center', va='center', fontsize=7, color='white')

  

=== Draw RED outline (flipped coords) ===

outline_coords = get_outline_from_material_cells(material_cells)

polygon = Polygon(outline_coords, closed=True, fill=False, edgecolor='red', linewidth=2)

ax.add_patch(polygon)

  

=== Draw risk bubbles (top-left aligned, flipped Y) ===

cell_risks = defaultdict(list)

for rid, x, y in risks:

cell_risks[(x, y)].append(rid)

  

bubble_radius = 0.08

max_per_row = 6

spacing_x = 0.15

spacing_y = 0.18

  

for (x, y), rids in cell_risks.items():

display_y = 6 - y

is_material = (x, y) in material_cells

edge_color = 'red' if is_material else 'black'

  

for idx, rid in enumerate(rids):

col = idx % max_per_row

row = idx // max_per_row

px = x - 1 + spacing_x * col + 0.1

py = display_y + spacing_y * row + bubble_radius

  

circle = plt.Circle((px, py), bubble_radius, facecolor='white', edgecolor=edge_color, lw=1.2)

ax.add_patch(circle)

ax.text(px, py, str(rid), ha='center', va='center', fontsize=6.5)

  

=== Axis Labels and Layout ===

ax.set_xticks([i + 0.5 for i in range(5)])

ax.set_xticklabels(x_labels, fontsize=9, ha='center', rotation=30)

ax.set_yticks([i + 0.5 for i in range(6)])

ax.set_yticklabels(list(reversed(y_labels)), fontsize=9)

  

=== Flip Y-Axis to match high-impact top ===

ax.set_ylim(6, 0)

ax.set_xlim(0, 5)

ax.set_aspect('equal')

ax.tick_params(left=False, bottom=False)

ax.set_title("Flipped-Upward Material Triangle with Correct Red Outline", fontsize=14, weight='bold')

  

plt.subplots_adjust(left=0.05, right=0.75, top=0.95, bottom=0.1)

plt.savefig("risk_matrix_flipped_outline.png", dpi=300)

plt.show()
```
```
# Part 2: Load CSV and generate risk matrix plot
import pandas as pd
import matplotlib.pyplot as plt
import matplotlib.patches as patches
from matplotlib.patches import Polygon
from shapely.geometry import box
from shapely.ops import unary_union
from collections import defaultdict

# === Load data ===
df_risks = pd.read_csv("generated_risks.csv")

# === Define material triangle ===
material_cells = [(x, y) for y in range(6, 1, -1) for x in range(7 - y, 6)]

x_labels = [
    "1- Rare\n(>20 yrs)", "2- Unlikely\n(10–20 yrs)", "3- Possible\n(5 yrs)",
    "4- Likely\n(1–5 yrs)", "5- Very Likely\n(<1 yr)"
]
y_labels = [
    "5 – Major\n(> $2bn)", "4 – Significant\n($1bn–2bn)", "3 – Moderate\n($500m–1bn)",
    "2 – Minor\n($200m–500m)", "1 – Low\n($100m–200m)", "0 – Extr. Low\n(< $100m)"
]

# === Red outline generator ===
def get_outline_from_material_cells(material_cells):
    cell_polys = [box(x - 1, y - 1, x, y) for (x, y) in material_cells]
    merged = unary_union(cell_polys)
    coords = list(merged.exterior.coords) if hasattr(merged, 'exterior') else list(merged.geoms[0].exterior.coords)
    return [(x, 6 - y) for (x, y) in coords]

# === Setup plot ===
fig, ax = plt.subplots(figsize=(16, 9))
ax.set_xlim(0, 5)
ax.set_ylim(6, 0)
ax.set_aspect('equal')
ax.tick_params(left=False, bottom=False)

# Draw grid
for i in range(6):
    ax.axvline(i, color='black', linewidth=0.5)
for i in range(7):
    ax.axhline(i, color='black', linewidth=0.5)

# Gray material cells
for x, y in material_cells:
    display_y = 6 - y
    ax.add_patch(patches.Rectangle((x - 1, display_y), 1, 1, color='gray', alpha=0.4))
    ax.text(x - 0.5, display_y + 0.5, 'Material', ha='center', va='center', fontsize=7, color='white')

# Red outline
outline_coords = get_outline_from_material_cells(material_cells)
polygon = Polygon(outline_coords, closed=True, fill=False, edgecolor='red', linewidth=2)
ax.add_patch(polygon)

# Bubbles per cell
display_risks = defaultdict(list)
for _, row in df_risks.iterrows():
    display_risks[(row['Likelihood'], row['Impact'])].append((row['Index'], row['Risk Name']))

bubble_radius = 0.08
max_per_row = 6
spacing_x = 0.15
spacing_y = 0.18

for (x, y), risks in display_risks.items():
    display_y = 6 - y
    is_material = (x, y) in material_cells
    edge_color = 'red' if is_material else 'black'

    for idx, (rid, _) in enumerate(risks):
        col = idx % max_per_row
        row = idx // max_per_row
        px = x - 1 + spacing_x * col + 0.1
        py = display_y + spacing_y * row + bubble_radius
        circle = plt.Circle((px, py), bubble_radius, facecolor='white', edgecolor=edge_color, lw=1.2)
        ax.add_patch(circle)
        ax.text(px, py, str(rid), ha='center', va='center', fontsize=6.5)

# Axis labels
ax.set_xticks([i + 0.5 for i in range(5)])
ax.set_xticklabels(x_labels, fontsize=9, ha='center', rotation=30)
ax.set_yticks([i + 0.5 for i in range(6)])
ax.set_yticklabels(list(reversed(y_labels)), fontsize=9)

ax.set_title("Risk Matrix from CSV Dataset", fontsize=14, weight='bold')
plt.subplots_adjust(left=0.05, right=0.75, top=0.95, bottom=0.1)
plt.savefig("risk_matrix_from_csv.png", dpi=300)
plt.show()

```
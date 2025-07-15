```
import matplotlib.pyplot as plt

import matplotlib.patches as patches

from matplotlib.patches import Polygon

from shapely.geometry import box

from shapely.ops import unary_union

from collections import defaultdict

import pandas as pd

from pptx import Presentation

from pptx.util import Inches, Pt

from pptx.enum.text import MSO_AUTO_SIZE

from pptx.dml.color import RGBColor

  

# Load data

df = pd.read_csv("generated_risks.csv")

df = df.sort_values(by=['Business Division', 'Risk Type', 'Index'])

  

# Generate chart image from CSV

fig, ax = plt.subplots(figsize=(10, 6))

  

# Axis labels

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

  

# Material cells

material_cells = [(x, y) for y in range(6, 1, -1) for x in range(7 - y, 6)]

  

# Flip coordinates

def get_outline_from_material_cells(material_cells):

cell_polys = [box(x - 1, y - 1, x, y) for (x, y) in material_cells]

merged = unary_union(cell_polys)

coords = list(merged.exterior.coords) if hasattr(merged, 'exterior') else list(merged.geoms[0].exterior.coords)

return [(x, 6 - y) for (x, y) in coords]

  

# Grid

for i in range(6):

ax.axhline(i, color='black', linewidth=0.5)

ax.axvline(i, color='black', linewidth=0.5)

  

# Material cells

for x, y in material_cells:

display_y = 6 - y

ax.add_patch(patches.Rectangle((x - 1, display_y), 1, 1, color='gray', alpha=0.4))

ax.text(x - 0.5, display_y + 0.5, 'Material', ha='center', va='center', fontsize=6, color='white')

  

# Red outline

outline_coords = get_outline_from_material_cells(material_cells)

polygon = Polygon(outline_coords, closed=True, fill=False, edgecolor='red', linewidth=2)

ax.add_patch(polygon)

  

# Risk bubbles

df['Likelihood'] = pd.to_numeric(df['Likelihood'], errors='coerce')

df['Impact'] = pd.to_numeric(df['Impact'], errors='coerce')

cell_risks = defaultdict(list)

for _, row in df.iterrows():

cell_risks[(row['Likelihood'], row['Impact'])].append(row['Index'])

  

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

  

# Axis

ax.set_xticks([i + 0.5 for i in range(5)])

ax.set_xticklabels(x_labels, fontsize=8, ha='center', rotation=30)

ax.set_yticks([i + 0.5 for i in range(6)])

ax.set_yticklabels(list(reversed(y_labels)), fontsize=8)

ax.set_ylim(6, 0)

ax.set_xlim(0, 5)

ax.set_aspect('equal')

ax.tick_params(left=False, bottom=False)

plt.tight_layout()

plt.savefig("risk_matrix_from_csv.png", dpi=300, bbox_inches='tight')

plt.close()

  

# Create presentation

prs = Presentation()

prs.slide_width = Inches(13.33)

prs.slide_height = Inches(7.5)

slide = prs.slides.add_slide(prs.slide_layouts[5])

  

# Text areas (No indent)

df_first_half = df.iloc[:25]

df_second_half = df.iloc[25:]

positions = [(0.3, df_first_half), (4.4, df_second_half)]

for left_inch, df_group in positions:

txBox = slide.shapes.add_textbox(Inches(left_inch), Inches(0.5), Inches(3.8), Inches(6.5))

tf = txBox.text_frame

tf.word_wrap = True

tf.auto_size = MSO_AUTO_SIZE.SHAPE_TO_FIT_TEXT

for div, div_df in df_group.groupby('Business Division'):

p0 = tf.add_paragraph()

p0.text = div

p0.level = 0

p0.font.size = Pt(14)

p0.font.bold = True

for rtype, r_df in div_df.groupby('Risk Type'):

p1 = tf.add_paragraph()

p1.text = rtype

p1.level = 1

p1.font.size = Pt(12)

p1.font.color.rgb = RGBColor(200, 0, 0)

for _, row in r_df.iterrows():

p2 = tf.add_paragraph()

p2.text = f"{row['Index']}. {row['Risk Name']}"

p2.level = 2

p2.font.size = Pt(10)

  

# Dashboard image

img_path = "risk_matrix_from_csv.png"

slide.shapes.add_picture(img_path, Inches(8.5), Inches(0.6), height=Inches(6.3))

  

# Save

pptx_path = "11Risk_Slide_3Part_Indented_16x9.pptx"

prs.save(pptx_path)
```
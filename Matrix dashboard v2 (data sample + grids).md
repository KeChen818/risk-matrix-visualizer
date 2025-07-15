```
import pandas as pd

import matplotlib.pyplot as plt

import matplotlib.patches as patches

from matplotlib.patches import Polygon

from shapely.geometry import box

from shapely.ops import unary_union

from collections import defaultdict

import random

  

# === Risk Data Generation ===

risk_types = [

'Credit Risk', 'Market Risk', 'Operational Risk', 'Liquidity Risk',

'Compliance Risk', 'Reputational Risk', 'Model Risk', 'Strategic Risk'

]

  

business_units = [

'Retail Banking', 'Investment Bank', 'Wealth Management',

'Group Treasury', 'Asset Management', 'Group Risk', 'Technology & Ops'

]

  

risk_name_templates = {

'Credit Risk': [

'Mortgage default risk due to {}',

'Concentration in {} lending portfolio',

'Counterparty risk in {}',

'Default risk from {} sector exposure',

'{} downgrade triggering collateral calls'

],

'Market Risk': [

'Volatility in {} market positions',

'Basis risk between {} instruments',

'Losses from unexpected movements in {}',

'FX exposure from {} subsidiaries',

'Interest rate sensitivity due to {}'

],

'Operational Risk': [

'Process failure in {} operations',

'Human error leading to {} loss',

'Inadequate controls over {}',

'Outsourcing risk in {}',

'Fraudulent activity in {} unit'

],

'Liquidity Risk': [

'Stress on intraday liquidity due to {}',

'Funding gap from {} mismatches',

'Increased liquidity buffer due to {}',

'Fire sale risk in {} assets',

'Inability to rollover {} funding'

],

'Compliance Risk': [

'AML control gaps in {} operations',

'Delayed KYC review for {} clients',

'Regulatory breach in {} jurisdiction',

'Sanction screening failure in {}',

'Mis-selling risk in {} products'

],

'Reputational Risk': [

'Public scrutiny due to {} incident',

'Negative media coverage of {}',

'Client backlash from {} decision',

'Social media spread of {} rumor',

'High-profile exit in {} division'

],

'Model Risk': [

'Overreliance on {} assumptions',

'Backtesting failure for {} models',

'Outdated risk model for {} portfolio',

'Model drift in {} calculations',

'Improper model governance in {}'

],

'Strategic Risk': [

'Unsuccessful execution of {} strategy',

'M&A integration risk from {} deal',

'Revenue drop due to {} market shift',

'Geopolitical disruption to {} plans',

'Talent loss affecting {} growth'

]

}

  

placeholders = [

'emerging markets', 'cyber threat', 'retail products', 'real estate', 'energy sector',

'Asia-Pacific region', 'client onboarding', 'interest rates', 'climate risk',

'legacy systems', 'AI modeling', 'crypto exposure', 'social unrest'

]

  

# Create risk DataFrame

risks = []

for i in range(1, 51):

rtype = random.choice(risk_types)

bunit = random.choice(business_units)

template = random.choice(risk_name_templates[rtype])

placeholder = random.choice(placeholders)

rname = template.format(placeholder)

likelihood = random.randint(1, 5)

impact = random.randint(1, 6)

risks.append([i, rtype, bunit, rname, likelihood, impact])

  

df_risks = pd.DataFrame(risks, columns=['Index', 'Risk Type', 'Business Division', 'Risk Name', 'Likelihood', 'Impact'])

  

# === Plot Setup ===

material_cells = [(x, y) for y in range(6, 1, -1) for x in range(7 - y, 6)]

x_labels = [

"1- Rare\n(>20 yrs)", "2- Unlikely\n(10–20 yrs)", "3- Possible\n(5 yrs)",

"4- Likely\n(1–5 yrs)", "5- Very Likely\n(<1 yr)"

]

y_labels = [

"5 – Major\n(> $2bn)", "4 – Significant\n($1bn–2bn)", "3 – Moderate\n($500m–1bn)",

"2 – Minor\n($200m–500m)", "1 – Low\n($100m–200m)", "0 – Extr. Low\n(< $100m)"

]

  

def get_outline_from_material_cells(material_cells):

cell_polys = [box(x - 1, y - 1, x, y) for (x, y) in material_cells]

merged = unary_union(cell_polys)

coords = list(merged.exterior.coords) if hasattr(merged, 'exterior') else list(merged.geoms[0].exterior.coords)

return [(x, 6 - y) for (x, y) in coords]

  

fig, ax = plt.subplots(figsize=(16, 9))

ax.set_xlim(0, 5)

ax.set_ylim(6, 0)

ax.set_aspect('equal')

ax.tick_params(left=False, bottom=False)

  

# Grid (solid)

for i in range(6):

ax.axvline(i, color='black', linewidth=0.5)

for i in range(7):

ax.axhline(i, color='black', linewidth=0.5)

  

# Material cells

for x, y in material_cells:

display_y = 6 - y

ax.add_patch(patches.Rectangle((x - 1, display_y), 1, 1, color='gray', alpha=0.4))

ax.text(x - 0.5, display_y + 0.5, 'Material', ha='center', va='center', fontsize=7, color='white')

  

# Red outline

outline_coords = get_outline_from_material_cells(material_cells)

polygon = Polygon(outline_coords, closed=True, fill=False, edgecolor='red', linewidth=2)

ax.add_patch(polygon)

  

# Risk bubbles

cell_risks = defaultdict(list)

for _, row in df_risks.iterrows():

cell_risks[(row['Likelihood'], row['Impact'])].append((row['Index'], row['Risk Name']))

  

bubble_radius = 0.08

max_per_row = 6

spacing_x = 0.15

spacing_y = 0.18

  

for (x, y), risks_in_cell in cell_risks.items():

display_y = 6 - y

is_material = (x, y) in material_cells

edge_color = 'red' if is_material else 'black'

  

for idx, (rid, _) in enumerate(risks_in_cell):

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

  

# Title

ax.set_title("Risk Matrix with 50 Labeled Risks from Dataset", fontsize=14, weight='bold')

  

plt.subplots_adjust(left=0.05, right=0.65, top=0.95, bottom=0.1)

plt.savefig("risk_matrix_cleaned.png", dpi=300)

plt.show()
```
```
import pandas as pd
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

# === Create risk DataFrame ===
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

df_risks = pd.DataFrame(risks, columns=[
    'Index', 'Risk Type', 'Business Division', 'Risk Name', 'Likelihood', 'Impact'
])

# === Save to CSV ===
df_risks.to_csv("/mnt/data/generated_risks.csv", index=False)

# Optional preview
df_risks.head()

```
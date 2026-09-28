---
layout: about
title: about
permalink: /
subtitle: Master in Public Policy candidate, <a href='https://www.hks.harvard.edu/'>Harvard Kennedy School</a>. Energy markets, utility regulation, and grid finance.

profile:
  align: right
  image: prof_pic.jpg
  image_circular: false # crops the image to make it circular
  more_info: >
    <p>Cambridge, MA</p>

selected_papers: false # includes a list of papers marked as "selected={true}"
social: true # includes social icons at the bottom of the page

announcements:
  enabled: false # includes a list of news items

latest_posts:
  enabled: false
---

I'm Kathy, a Master in Public Policy candidate at Harvard Kennedy School. I work on electricity markets, utility regulation, and how new grid resources get financed.

At [Seattle City Light](https://www.seattle.gov/city-light), I developed the utility's virtual power plant valuation framework and led carbon compliance analysis under Washington's Climate Commitment Act, work that saved ratepayers more than $1M. I also represented the utility in stakeholder processes on EDAM and Markets+.

At HKS, I'm a research assistant at the Belfer Center studying how data centers affect water and power systems. Earlier this year, I worked with the Town of Acton on non-pipeline alternatives to gas infrastructure, building a benefit-cost analysis of heat pump pilots with National Grid.

At UC Berkeley, I studied environmental science and did econometric research on how power plant outages affect CAISO prices at the [Energy Institute at Haas](https://haas.berkeley.edu/energy-institute/). I also worked with the California Air Resources Board on equitable home electrification, and on Goldman School client projects for the California Energy Commission and the Office of Energy Infrastructure Safety.

I'm looking for full-time roles with utilities, regulators, and energy consulting firms after I graduate in May 2027.

## Selected work

<div class="projects">
  <div class="row row-cols-1 row-cols-md-2">
  {% assign featured_projects = site.projects | where_exp: "p", "p.featured" | sort: "featured" %}
  {% for project in featured_projects %}
    {% include projects.liquid %}
  {% endfor %}
  </div>
</div>

## Current research

At the Belfer Center, I'm studying what data center developers should disclose about public resources, and whether permitting captures the water used by the power plants that serve them.

## If you're hiring for

- **A utility or ISO/RTO:** see [utility & market operations]({{ '/projects/' | relative_url }}#utility-market-operations), including carbon compliance and allowance market strategy.
- **An energy consultancy:** see [grid & financial modeling]({{ '/projects/' | relative_url }}#grid-financial-modeling), including VPP valuation, project finance, and siting optimization.
- **A regulator or policy office:** see [policy & regulatory analysis]({{ '/projects/' | relative_url }}#policy-regulatory-analysis) and my [writing samples]({{ '/writing/' | relative_url }}).

My full background is on my [CV]({{ '/cv/' | relative_url }}).

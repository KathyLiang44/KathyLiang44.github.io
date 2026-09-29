---
layout: about
title: about
permalink: /
subtitle: Master in Public Policy candidate, <a href='https://www.hks.harvard.edu/'>Harvard Kennedy School</a>. Background in energy markets, grid-level research, ISO & state stakeholder engagement, and state and local energy policy.

profile:
  align: right
  image: prof_pic.jpg
  image_circular: false # crops the image to make it circular
  more_info: >
    <p>Currently in Cambridge, MA</p>

selected_papers: false # includes a list of papers marked as "selected={true}"
social: true # includes social icons at the bottom of the page

announcements:
  enabled: false # includes a list of news items

latest_posts:
  enabled: false
---

I'm Kathy, a Master in Public Policy candidate at Harvard Kennedy School. I've worked on grid decarbonization from several sides of the table: resource procurement under greenhouse gas compliance obligations at an electric utility, grid research using machine learning and optimization, regulatory analysis for state agencies and municipalities, and ratepayer advocacy with a California nonprofit.

Seeing the same problem from each of those seats taught me that robust, data-driven analysis is only the first step. Lasting solutions also need people who understand the public interest, what regulators can approve, and how industry puts plans into practice, and who can build support across all three for a grid that is more reliable, affordable, and climate resilient. That's the kind of practitioner I want to be.

After I graduate in May 2027, I'm looking for full-time roles that combine data-driven analysis, regulation, and stakeholder engagement, whether at a utility, consultancy, regulator, independent power producer, or transmission developer.

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

---
layout: page
title: projects
permalink: /projects/
description: Selected analysis and writing on electricity markets, transmission, and grid finance.
nav: true
nav_order: 2
display_categories: [utility & market operations, grid & financial modeling, policy & regulatory analysis]
horizontal: false
---

Projects are grouped by the kind of work they show:

- **[Utility & market operations](#utility-market-operations)**: carbon compliance and allowance markets. Most relevant for utilities, ISOs/RTOs, and trading teams.
- **[Grid & financial modeling](#grid-financial-modeling)**: VPP valuation, project finance, and resource siting optimization. Most relevant for energy consulting, resource planning, and regulatory staff roles.
- **[Policy & regulatory analysis](#policy-regulatory-analysis)**: client reports for state agencies. Most relevant for regulators, legislative offices, and advocacy organizations.

<!-- pages/projects.md -->
<div class="projects">
{% if site.enable_project_categories and page.display_categories %}
  <!-- Display categorized projects -->
  {% for category in page.display_categories %}
  <a id="{{ category | slugify }}" href=".#{{ category | slugify }}">
    <h2 class="category">{{ category }}</h2>
  </a>
  {% assign categorized_projects = site.projects | where: "category", category %}
  {% assign sorted_projects = categorized_projects | sort: "importance" %}
  <!-- Generate cards for each project -->
  {% if page.horizontal %}
  <div class="container">
    <div class="row row-cols-1 row-cols-md-2">
    {% for project in sorted_projects %}
      {% include projects_horizontal.liquid %}
    {% endfor %}
    </div>
  </div>
  {% else %}
  <div class="row row-cols-1 row-cols-md-3">
    {% for project in sorted_projects %}
      {% include projects.liquid %}
    {% endfor %}
  </div>
  {% endif %}
  {% endfor %}

{% else %}

<!-- Display projects without categories -->

{% assign sorted_projects = site.projects | sort: "importance" %}

  <!-- Generate cards for each project -->

{% if page.horizontal %}

  <div class="container">
    <div class="row row-cols-1 row-cols-md-2">
    {% for project in sorted_projects %}
      {% include projects_horizontal.liquid %}
    {% endfor %}
    </div>
  </div>
  {% else %}
  <div class="row row-cols-1 row-cols-md-3">
    {% for project in sorted_projects %}
      {% include projects.liquid %}
    {% endfor %}
  </div>
  {% endif %}
{% endif %}
</div>

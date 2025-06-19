---
layout: page
permalink: /supervision/
title: supervision
description: "My thesis supervisions, past and present."
nav: true
nav_order: 7
---

<div class="publications">
{%- assign students_by_category = site.data.supervision | group_by: 'category' -%}
{%- for category_group in students_by_category -%}
  {%- if category_group.name -%}
    <h4 class="font-weight-bolder mt-5 mb-4">{{ category_group.name }}</h4>
  {%- endif -%}

  {%- assign students_by_year = category_group.items | group_by: 'year' | sort: 'name' | reverse -%}
  {%- for year_group in students_by_year -%}
    <h2 class="year">{{ year_group.name }}</h2>
    {%- for student in year_group.items -%}
      <div class="row" style="margin-bottom: 1.5rem;">
        <div class="col-sm-10">

          <div class="title"><strong>{{ student.name }}</strong></div>

          <div class="author">{{ student.info }}</div>

          {%- if student.thesis and student.thesis != "" -%}
          <div class="periodical">
            <em>Thesis title:</em> {{ student.thesis }}
          </div>
          {%- endif -%}

          {%- if student.note and student.note != "" -%}
          <div class="periodical">
            <em>Note:</em> {{ student.note }}
          </div>
          {%- endif -%}

          {%- if student.abstract -%}
          <div class="links">
            <a class="abstract btn btn-sm z-depth-0" role="button">Details</a>
          </div>
          <div class="abstract hidden">
            <p>{{ student.abstract | markdownify }}</p>
          </div>
          {%- endif -%}

        </div>
      </div>
    {%- endfor -%}
  {%- endfor -%}
{%- endfor -%}
</div>
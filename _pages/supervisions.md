---
layout: page
permalink: /supervision/
title: supervision
description: "My thesis supervisions, past and present."
nav: true
nav_order: 7
---

<div class="publications">
{%- assign students_by_year = site.data.supervision | group_by: 'year' | sort: 'name' | reverse -%}
{%- assign has_photos = false -%}
{%- for year_group in students_by_year -%}
  {%- for student in year_group.items -%}
    {%- if student.photo -%}
      {%- assign has_photos = true -%}
      {%- break -%}
    {%- endif -%}
  {%- endfor -%}
  {%- if has_photos -%}
    {%- break -%}
  {%- endif -%}
{%- endfor -%}

{%- for year_group in students_by_year -%}
  <h2 class="year">{{ year_group.name }}</h2>
  {%- for student in year_group.items -%}
    <div id="{{ student.name | slugify }}" class="student-entry">
      {%- if student.photo -%}
        <!-- Two-column layout with photo -->
        <div class="student-row">
          <!-- Student Photo -->
          <div class="col-sm-2 preview" style="padding-right: 0px;">
            <img class="preview z-depth-1 rounded" src="{{ student.photo | prepend: '/assets/img/student_photos/' | relative_url }}" alt="{{ student.name }}">
          </div>

          <!-- Student Details -->
          <div class="col-sm-10">
            <!-- Student Name -->
            <div class="title"><strong>{{ student.name }}</strong></div>
            
            <!-- Institution Info -->
            <div class="author">{{ student.info }}</div>

            <!-- Thesis Title -->
            {%- if student.thesis and student.thesis != "" -%}
            <div class="periodical">
              <em>Thesis title:</em> {{ student.thesis }}
            </div>
            {%- endif -%}

            <!-- Co-supervision Note -->
            {%- if student.note and student.note != "" -%}
            <div class="periodical">
              <em>Note:</em> {{ student.note }}
            </div>
            {%- endif -%}

            <!-- Interactive Buttons -->
            <div class="links">
            {%- if student.abstract and student.abstract != "" -%}
              <a class="abstract btn btn-sm z-depth-0" role="button">Abstract</a>
            {%- endif -%}
            {%- if student.thesis_url -%}
              <a href="{{ student.thesis_url }}" class="btn btn-sm z-depth-0" role="button" target="_blank" rel="external nofollow noopener">Thesis</a>
            {%- endif -%}
            {%- if student.linkedin -%}
              <a href="{{ student.linkedin }}" class="btn btn-sm z-depth-0" role="button" target="_blank" rel="external nofollow noopener">LinkedIn</a>
            {%- endif -%}
            {%- if student.github -%}
              <a href="{{ student.github }}" class="btn btn-sm z-depth-0" role="button" target="_blank" rel="external nofollow noopener">GitHub</a>
            {%- endif -%}
            {%- if student.cv -%}
              <a href="{{ student.cv }}" class="btn btn-sm z-depth-0" role="button" target="_blank" rel="external nofollow noopener">CV</a>
            {%- endif -%}
            {%- if student.website -%}
              <a href="{{ student.website }}" class="btn btn-sm z-depth-0" role="button" target="_blank" rel="external nofollow noopener">Website</a>
            {%- endif -%}
            </div>

            <!-- Hidden Content Blocks -->
            {%- if student.abstract and student.abstract != "" -%}
            <div class="abstract hidden">
              <p>{{ student.abstract }}</p>
            </div>
            {%- endif -%}
          </div>
        </div>
      {%- else -%}
        <!-- Clean single-column layout without photos/badges -->
        <!-- Student Name -->
        <div class="title"><strong>{{ student.name }}</strong></div>
        
        <!-- Institution Info -->
        <div class="author">{{ student.info }}</div>

        <!-- Thesis Title -->
        {%- if student.thesis and student.thesis != "" -%}
        <div class="periodical">
          <em>Thesis title:</em> {{ student.thesis }}
        </div>
        {%- endif -%}

        <!-- Co-supervision Note -->
        {%- if student.note and student.note != "" -%}
        <div class="periodical">
          <em>Note:</em> {{ student.note }}
        </div>
        {%- endif -%}

        <!-- Interactive Buttons -->
        <div class="links">
        {%- if student.abstract and student.abstract != "" -%}
          <a class="abstract btn btn-sm z-depth-0" role="button">Abstract</a>
        {%- endif -%}
        {%- if student.thesis_url -%}
          <a href="{{ student.thesis_url }}" class="btn btn-sm z-depth-0" role="button" target="_blank" rel="external nofollow noopener">Thesis</a>
        {%- endif -%}
        {%- if student.linkedin -%}
          <a href="{{ student.linkedin }}" class="btn btn-sm z-depth-0" role="button" target="_blank" rel="external nofollow noopener">LinkedIn</a>
        {%- endif -%}
        {%- if student.github -%}
          <a href="{{ student.github }}" class="btn btn-sm z-depth-0" role="button" target="_blank" rel="external nofollow noopener">GitHub</a>
        {%- endif -%}
        {%- if student.cv -%}
          <a href="{{ student.cv }}" class="btn btn-sm z-depth-0" role="button" target="_blank" rel="external nofollow noopener">CV</a>
        {%- endif -%}
        {%- if student.website -%}
          <a href="{{ student.website }}" class="btn btn-sm z-depth-0" role="button" target="_blank" rel="external nofollow noopener">Website</a>
        {%- endif -%}
        </div>

        <!-- Hidden Content Blocks -->
        {%- if student.abstract and student.abstract != "" -%}
        <div class="abstract hidden">
          <p>{{ student.abstract }}</p>
        </div>
        {%- endif -%}
      {%- endif -%}
    </div>
  {%- endfor -%}
{%- endfor -%}
</div>
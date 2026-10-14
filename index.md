---
layout: default
title: Technical Documentation Overview
---

# doc f

Enterprise technical documentation platform with interactive navigation tree, multi-language snippets, and zero-dependency static deployment.

## 📚 Technical Guides by Category

{% assign doc_groups = site.docs | group_by: "category" %}
{% if doc_groups.size > 0 %}
  <div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(260px, 1fr)); gap: 16px; margin: 24px 0;">
    {% for group in doc_groups %}
      <div style="background: var(--surface-subtle); border: 1px solid var(--border); border-radius: 10px; padding: 18px;">
        <h3 style="margin-top: 0; font-size: 16px; display: flex; align-items: center; justify-content: space-between;">
          <span>{{ group.name }}</span>
          <span style="font-size: 11px; font-weight: 600; padding: 2px 7px; border-radius: 9999px; background: var(--surface); border: 1px solid var(--border);">
            {{ group.items.size }}
          </span>
        </h3>
        <ul style="padding-left: 18px; margin: 8px 0 0 0; font-size: 13px;">
          {% for doc in group.items %}
            <li style="margin-bottom: 6px;">
              <a href="{{ doc.url | relative_url }}" style="color: var(--accent); text-decoration: none; font-weight: 500;">
                {{ doc.title }}
              </a>
              {% if doc.read_time %}
                <span style="color: var(--text-dim); font-size: 11px;">({{ doc.read_time }})</span>
              {% endif %}
            </li>
          {% endfor %}
        </ul>
      </div>
    {% endfor %}
  </div>
{% else %}
  {% for item in site.docs %}
  - **[{{ item.title }}]({{ item.url | relative_url }})** — *{{ item.description }}* ({{ item.read_time }})
  {% endfor %}
{% endif %}

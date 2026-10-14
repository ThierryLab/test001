---
layout: default
title: Documentation & Cookbook Overview
---

# DocuCraft Documentation

Modern technical guides and cookbook recipes for high-performance systems.

## 📚 Technical Guides
{% for item in site.docs %}
- **[{{ item.title }}]({{ item.url | relative_url }})** — *{{ item.description }}* ({{ item.read_time }})
{% endfor %}

## 🍳 Cookbook Recipes
{% for recipe in site.recipes %}
- **[{{ recipe.title }}]({{ recipe.url | relative_url }})** — {{ recipe.difficulty }} ({{ recipe.read_time }})
{% endfor %}

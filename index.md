---
layout: default
title: Technical Documentation Overview
---

# DODOC

Enterprise technical documentation platform with interactive navigation tree, multi-language snippets, and zero-dependency static deployment.

## 📚 Technical Guides
{% for item in site.docs %}
- **[{{ item.title }}]({{ item.url | relative_url }})** — *{{ item.description }}* ({{ item.read_time }})
{% endfor %}

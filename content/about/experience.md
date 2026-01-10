---
# An instance of the Experience widget.
# Documentation: https://docs.hugoblox.com/page-builder/
widget: experience

# This file represents a page section.
headless: true

# Order that this section appears on the page.
weight: 20

title: Experience
subtitle:

# Date format for experience
#   Refer to https://docs.hugoblox.com/customization/#date-format
date_format: Jan 2006

# Experiences.
#   Add/remove as many `experience` items below as you like.
#   Required fields are `title`, `company`, and `date_start`.
#   Leave `date_end` empty if it's your current employer.
#   Begin multi-line descriptions with YAML's `|2-` multi-line prefix.
experience:
  - title: Robotics Engineer
    company: Telexistence
    company_url: 'https://tx-inc.com/en/home/'
    company_logo: tx
    location: Tokyo, Japan
    date_start: '2024-09-25'
    date_end: '2025-07-11'
    description: |2-
      * Worked on Fleet Management systems for Autonomous Forklifts
      * Implemented error recovery pipelines

  - title: Robotics Engineer
    company: Addverb Technologies
    company_url: 'https://addverb.com/'
    company_logo: addverb
    location: Noida, India
    date_start: '2022-08-01'
    date_end: '2024-06-21'
    description: |2-
      * Worked on Multi-Agent Path Planning for AGVs
      * Implemented behaviour trees for easier handling of states
      * Deployed over 150 robots across 4 countries
      * Worked independently on two greenfield projects

design:
  columns: '1'
---

---
title: AP IB Study Guide
type: entity
sources: [ib-competitive-landscape-apibstudyguide.md, claude-usage-patterns-across-repos.md]
created: 2026-04-04
updated: 2026-04-04
---

# AP IB Study Guide

IB study guide site (kulisama81/apibstudyguide) built with Astro 6, MDX, KaTeX, and Mermaid. The most advanced Claude integration — 100% Claude-driven via a [[multi-agent-architecture]] with 6 specialized agents. Active Mar-Apr 2026.

## What It Does

Free, no-signup study guides for IB Biology HL, Math AA HL, and Chemistry SL aligned to the 2025 IB syllabus. Each topic page includes inline diagrams, exam alert boxes for mark-scheme traps, and a free PDF download.

## Why It Matters to the Wiki

This project represents the culmination of Claude integration evolution:
- **Phase 3 architecture**: 6 agents (planner, autoresearch, creator, validator, reviewer, ux-reviewer)
- **Automated quality gates**: PreToolUse hooks block ticket closure without clean UX review
- **Analytics-driven prioritization**: Tier 1-4 scoring for ticket selection
- **[[tkt]] MCP server**: Native ticket management within Claude sessions

## Content Strategy

Competitive analysis of 10 IB resource sites identified the key differentiator: 2025 syllabus alignment (most free content is still 2016 spec). Distribution via Reddit r/IBO with specific timing and engagement rules. See [[source-ib-competitive-landscape]].

See also: [[multi-agent-architecture]], [[source-ib-competitive-landscape]], [[source-claude-usage-patterns]]

# Contributing to Simulacra

## Adding a New Ghost Persona

1. Fork the repo
2. Add your persona to `skills/simulacra/references/` as a markdown file
3. Follow the existing persona structure (Perspective, Hunts for, Voice)
4. Submit a PR with a demo showing the persona finding a real bug

## Improving Skill Scorer Dimensions

1. Open an issue describing the dimension change
2. Include calibration examples (what scores higher/lower and why)
3. Submit a PR with updated SKILL.md and recalibrated anchors

## Bug Reports

Open an issue with:
- The code snippet you tested
- What Simulacra said
- What it should have said (false positive or false negative)

## Style Guide

- Skills are pure markdown. No binary dependencies.
- Cross-agent compatibility is mandatory. Test with at least Claude Code + one other agent.
- Ghost names should be memorable but not offensive.
- Every claimed bug must reference a specific line or function.

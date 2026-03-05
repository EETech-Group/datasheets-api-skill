# Datasheets API Skill

This repository contains an Agents skill for searching electronic components with the Datasheets.com Search API.

## What Is Included

- `skills/datasheets-api-skill/SKILL.md`: skill instructions, API rules, and workflow
- `skills/datasheets-api-skill/references/examples.md`: request examples in cURL, JavaScript, TypeScript, and Python

## API Used By This Skill

- Base URL: `https://www.datasheets.com`
- Endpoint: `GET /api/v1/search`
- Auth: Bearer token authentication injected by runtime credentials (not from chat context)

If you need credentials, create/manage them at:
`https://www.datasheets.com/account/api`

Security model for this skill:

- Do not ask users to paste tokens into chat
- Do not store raw tokens in repository files
- Use environment variables or a secret manager at execution time

## Repository Structure

```text
skills/
  datasheets-api-skill/
    SKILL.md
    references/
      examples.md
```

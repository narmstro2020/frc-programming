# FRC 829 Programming

Source for <https://narmstro2020.github.io/robotics/> &mdash; the programming-side
teaching site for FRC Team 829.

Everything here is student-facing. There are no answer keys in this repo, and the
robot code itself lives elsewhere (in the team's private repositories).

## Working on it

```bash
pip install -r requirements.txt
mkdocs serve            # http://127.0.0.1:8000/robotics/
```

Pages are plain Markdown under `docs/`. Add a page, add it to `nav:` in
`mkdocs.yml`, done.

## Publishing

Pushing to `main` runs `.github/workflows/publish-site.yml`, which builds the site
and pushes it into `narmstro2020/narmstro2020.github.io` under `robotics/`, alongside
the `pathway/` site. The repo root there is untouched.

Authentication is a deploy key: the private half is the `ACTIONS_DEPLOY_KEY` secret
here, and the public half is registered on the `narmstro2020.github.io` repo with
write access. It is scoped to that one repository and does not expire.

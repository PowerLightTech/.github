# PowerLightTech .github

This repository contains organization-wide community health defaults and
issue templates intended for the PowerLightTech GitHub organization. Do
not publish to GitHub until templates have been reviewed and tested.

Repository structure
- `ISSUE_TEMPLATE/`: issue form templates
  (`bug_report.yml`, `enhancement.yml`, `blank.yml`)
- `ISSUE_TEMPLATE/config.yml`: controls template order and other
  settings

How to edit and test
- Edit YAML files under `ISSUE_TEMPLATE/` to update templates.
- Test changes in a non-production repository before publishing.

Publishing notes
- When ready to apply organization-wide defaults, create a repository
  named `.github` in the PowerLightTech organization, push these files,
  and make the repo public so GitHub can surface the templates
  organization-wide.

Notes
- Individual repositories can override these defaults by adding their
  own templates.
- Adjust `ISSUE_TEMPLATE/config.yml` to set the preferred template
  order and contact links.
- For more details, check with TJ or refer to [GitHub's documentation on issue templates](https://docs.github.com/en/communities/using-templates-to-encourage-useful-issues-and-pull-requests/configuring-issue-templates-for-your-repository#creating-issue-templates).

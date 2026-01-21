# Automatic Issue to Project Assignment

This repository includes a GitHub Actions workflow that automatically adds newly created issues to a GitHub Project.

## How It Works

When a new issue is opened in any repository that uses this workflow, it will automatically be added to the configured GitHub Project.

## Deploying to Repositories

Since this is a `.github-private` repository, the workflow file needs to be copied to each repository where you want automatic issue-to-project assignment. You can:

1. **Manual Copy**: Copy `.github/workflows/add-issue-to-project.yml` to the `.github/workflows/` directory of each target repository
2. **Template Repository**: Use this as a template when creating new repositories
3. **Automated Sync** (Advanced): Use a separate workflow to sync this file across multiple repositories

The issue templates in this repository (`.github/ISSUE_TEMPLATE/`) are automatically inherited by all repositories in the organization that don't have their own issue templates.

## Setup Instructions

> **Note**: An initial organization-level token has been created and deployed for this workflow. This token will need to be periodically renewed and redeployed to maintain workflow functionality.

### 1. Create a Personal Access Token (PAT)

The workflow requires a PAT with permissions to add issues to projects.

#### Recommended: Fine-Grained Personal Access Token

Fine-grained tokens are more secure and provide better access control:

1. Go to GitHub Settings > Developer settings > Personal access tokens > Fine-grained tokens
2. Click "Generate new token"
3. Configure the token:
   - **Token name**: `GitHub Actions - Add to Project`
   - **Expiration**: Choose an appropriate expiration (recommend 90 days or less)
   - **Resource owner**: Select `PowerLightTech` organization
   - **Repository access**: Choose "All repositories" or select specific repositories
4. Set **Permissions**:
   - **Repository permissions**:
     - `Issues`: Read-only (to read issue data)
     - `Metadata`: Read-only (automatically selected)
   - **Organization permissions**:
     - `Projects`: Read and write (to add items to projects)
5. Click "Generate token" and copy the generated token

> **Note**: Fine-grained tokens have some [limitations](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens#fine-grained-personal-access-tokens-limitations). If you encounter issues, you may need to use a classic token instead.

#### Alternative: Classic Personal Access Token

If fine-grained tokens don't work for your use case:

1. Go to GitHub Settings > Developer settings > Personal access tokens > Tokens (classic)
2. Generate a new token with the following scopes:
   - `repo` (Full control of private repositories)
   - `project` (Full control of projects - for organization projects)
   - `org:read` (Read org and team membership - for organization projects)
3. Copy the generated token

### 2. Add the PAT as an Organization Secret

For organization-wide use:

1. Go to your organization's Settings > Secrets and variables > Actions
2. Click "New organization secret"
3. Name: `ADD_TO_PROJECT_PAT`
4. Value: Paste the PAT you created
5. Choose repository access (recommended: select specific repositories that need this functionality)

Alternatively, for a single repository:

1. Go to the repository's Settings > Secrets and variables > Actions
2. Click "New repository secret"
3. Name: `ADD_TO_PROJECT_PAT`
4. Value: Paste the PAT you created

### 3. Configure the Project URL

#### Option A: Use Default Project (Organization-wide)

The workflow is configured with a default project URL:
```
https://github.com/orgs/PowerLightTech/projects/1
```

To use a different default, edit `.github/workflows/add-issue-to-project.yml` and change the default project URL.

#### Option B: Configure Per Repository

To override the default and use a different project for a specific repository:

1. Go to the repository's Settings > Secrets and variables > Actions > Variables tab
2. Click "New repository variable"
3. Name: `PROJECT_URL`
4. Value: Your project URL (e.g., `https://github.com/orgs/PowerLightTech/projects/2`)
5. Click "Add variable"

### 4. Find Your Project URL

To find your GitHub Project URL:

1. Navigate to your GitHub Project
2. The URL format is:
   - Organization project: `https://github.com/orgs/YOUR_ORG/projects/PROJECT_NUMBER`
   - User project: `https://github.com/users/YOUR_USERNAME/projects/PROJECT_NUMBER`

## Usage

Once configured, the workflow runs automatically:

1. A new issue is created in the repository
2. The workflow triggers on the `issues: opened` event
3. The issue is automatically added to the configured GitHub Project
4. Team members can triage the issue in the project board

## Labels and Issue Templates

The existing issue templates already include labels:
- Bug reports: `bug`, `needs-triage`
- Enhancements: `enhancement`, `needs-triage`
- Blank issues: No labels

These labels help with automatic categorization in your project views.

## Troubleshooting

If issues are not being added to the project:

1. **Check the workflow runs**: Go to Actions tab and check for failures
2. **Verify the PAT**: Ensure `ADD_TO_PROJECT_PAT` secret is set and has correct permissions
3. **Verify the project URL**: Ensure the PROJECT_URL variable (if set) or default URL is correct
4. **Check permissions**: The PAT must have access to both the repository and the project

## Advanced Configuration

### Adding Issues Based on Labels

You can modify the workflow to only add issues with specific labels:

```yaml
jobs:
  add-to-project:
    runs-on: ubuntu-latest
    if: contains(github.event.issue.labels.*.name, 'needs-triage')
    steps:
      # ... rest of the workflow
```

### Multiple Projects

To add issues to different projects based on labels:

```yaml
jobs:
  add-to-project-bugs:
    runs-on: ubuntu-latest
    if: contains(github.event.issue.labels.*.name, 'bug')
    steps:
      - name: Add bug to bugs project
        uses: actions/add-to-project@v0.5.0
        with:
          project-url: https://github.com/orgs/PowerLightTech/projects/1
          github-token: ${{ secrets.ADD_TO_PROJECT_PAT }}
  
  add-to-project-enhancements:
    runs-on: ubuntu-latest
    if: contains(github.event.issue.labels.*.name, 'enhancement')
    steps:
      - name: Add enhancement to enhancements project
        uses: actions/add-to-project@v0.5.0
        with:
          project-url: https://github.com/orgs/PowerLightTech/projects/2
          github-token: ${{ secrets.ADD_TO_PROJECT_PAT }}
```

## References

- [GitHub Actions: add-to-project](https://github.com/actions/add-to-project)
- [GitHub Projects documentation](https://docs.github.com/en/issues/planning-and-tracking-with-projects)
- [Creating a fine-grained personal access token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens#creating-a-fine-grained-personal-access-token)
- [Creating a classic personal access token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)

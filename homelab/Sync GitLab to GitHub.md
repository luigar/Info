# Sync GitLab Repository to GitHub

This guide explains how to set up automatic synchronization of your GitLab repository to GitHub using GitLab CI/CD.

---

## 1. Set Up GitHub as a Remote in GitLab

### Create a Personal Access Token on GitHub:

1. Go to your GitHub account settings → Developer settings → Personal Access Tokens → Generate new token.
2. Give it the necessary `repo` scope for accessing your repositories.
3. Copy the token for later use.

### Add GitHub as a Remote in GitLab:

1. In your GitLab project, navigate to **Settings** → **CI/CD** → **Variables**.
2. Add the following variables:
   - `GITHUB_URL`: The GitHub repository URL (e.g., `https://github.com/yourusername/your-repo.git`).
   - `GITHUB_TOKEN`: The GitHub Personal Access Token.

---

## 2. Create a GitLab CI/CD Pipeline

Add a `.gitlab-ci.yml` file to your repository for syncing changes to GitHub.

### Example `.gitlab-ci.yml`:

```yaml
stages:
  - backup

backup_to_github:
  stage: backup
  script:
    - git config --global user.name "GitLab Backup Bot"
    - git config --global user.email "backup@yourdomain.com"
    - git remote add github $GITHUB_URL
    - git push --mirror https://$GITHUB_TOKEN@github.com/yourusername/your-repo.git
  only:
    - main
```

---

## 3. Explanation of the Pipeline

### Stage: Backup:

- The `backup_to_github` job runs in the `backup` stage.

### Git Configuration:

- Set Git user information for the sync operation.

### Push Mirror:

- The `git push --mirror` command ensures that all branches, tags, and references are mirrored to the GitHub repository.

### Trigger:

- This job will run only on changes to the `main` branch, but you can modify this to trigger on other branches or tags.

---

## 4. Validate the Setup

1. Push changes to your GitLab repository.
2. Check the GitHub repository to ensure it reflects the latest updates.

---

## 5. Automate with Schedules (Optional)

If you want the job to run periodically, even without changes, configure a schedule in GitLab:

1. Go to your GitLab project → **CI/CD** → **Schedules**.
2. Add a schedule to trigger the pipeline at regular intervals (e.g., daily).

---

## 6. Benefits

- **Continuous Backup**: Your GitHub repository will always have the latest updates from GitLab.
- **Disaster Recovery**: If something happens to your GitLab instance, you can recover from GitHub.

---

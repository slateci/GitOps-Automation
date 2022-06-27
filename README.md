# GitOps automation for SLATE instances

This repository provides a reference implementation of GitOps style automation for SLATE instances. This automation consists of:

-A GitHub Actions workflow for downloading our Python updater script, detecting changes, and running the Python updater

The workflow looks for files that match `values.yaml` and `instance.yaml`. When a file associated with an instance is modified the workflow will update that instance. If a file is added, it will be deployed as a new instance.

## Setup

Place the Values file `values.yaml` into a directory that represents the instance for each instance you want to manage. The directory can have any name, and can be nested. The values file must be called exactly `values.yaml`. Files at the repository root will be ignored. You will also need an `instance.yaml` for each instance.

Example:

```shell
├── <instance-one>
│   ├── instance.yaml
│   ├── values.yaml
├── <instance-two>
│   ├── instance.yaml
│   ├── values.yaml
...
...
```

### Notification Emails

Open `./.github/workflows/slate-deployment.yml` and update `mailgun_send_to` with a comma-delimited list of email addresses for those you wish notified when changes are made.

### SLATE Token

You will need to add your SLATE user token (obtained from the [SLATE Portal](https://portal.slateci.io/cli)) as a repository secret on your GitHub repository. To do this:

1. Navigate to `Settings` on the top bar of the GitHub repository interface
2. Choose `Secrets --> Actions` from the left-hand sidebar menu
3. Click the button that says `New repository secret` upper right
4. Name the secret `SLATE_API_TOKEN`

### Mailgun API Key

You will need to add your Mailgun API Key (obtained from the SLATE team) as a repository secret in your GitHub repository. Use the steps described above for `SLATE_API_TOKEN` to create a new `MAILGUN_API_KEY` secret.

### instance.yaml

For each instance, you must also have a file called `instance.yaml` that contains some details about the instance. For existing instances, only the `instance` field is needed to provide the ID. Info about the cluster, group, and app can be used to automatically deploy new instances too.

Optionally you can specify and update the version of the SLATE application with the `version` field in `instance.yaml`. If `version` is unspecified the latest version is the default.

#### New Instances

To deploy new instances you must include the cluster, group, and app. Version is optional.

```yaml
cluster: uutah-prod
group: slate-dev
app: nginx
version: 1.2.0
```

#### Existing instances

To manage existing instances you only need to specify a SLATE instanceID.

```yaml
instance: instance_BrX9HtpP1L0
```

## Git force pushes

Force pushes rewrite history in git, and can corrupt the state of your instances. Force pushing should be avoided.

## Git pre-commit hooks

This project uses [pre-commit](https://pre-commit.com) to lint all YAML files before allowing `git commit` to proceed.

### Installation

1. Create and activate your preferred Python interpreter (conda, virtualenv, etc.)
2. Install the `pre-commit` Python package:
   ```shell
   pip install -r requirements.txt
   ```
3. Check that `pre-commit` is properly installed:
   ```shell
   $ pre-commit --version
   pre-commit 2.17.0
   ```
4. Install the git hook scripts:
   ```shell
   $ pre-commit install
   pre-commit installed at .git/hooks/pre-commit
   ```

### Usage

1. Activate the Python interpreter for this project.
2. Develop normally and experience the pre-commit hook during `git commit`.

## Other issues

* If instance deletion is desired, it must currently be performed manually.
* The automation will write back the instance ID for instances that get deployed, this should be refactored to happen on a branch.

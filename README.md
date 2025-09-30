# Terraform Drift Integration Agent Action

This GitHub Action uses Google Gemini CLI to automatically remediate Terraform configuration drift by editing HCL files based on `terraform plan` output.

## Features

- Analyzes Terraform plan output to detect configuration drift
- Uses Gemini AI to suggest and apply fixes to Terraform HCL files
- Runs in YOLO mode, auto-approving agent actions for file edits and commands


## Inputs

| Name          | Description                                                                 | Required | Default |
|---------------|-----------------------------------------------------------------------------|----------|---------|
| `plan-command`| Shell command to run Terraform plan (e.g., `terraform plan -no-color -detailed-exitcode || true`) | Yes | - |
| `workdir`     | Working directory for Terraform files                                       | No      | `.`   |
| `gemini-model`| Gemini model to use (e.g., `gemini-2.5-flash`)                              | No      | `gemini-2.5-flash` |
| `extra-prompt`| Additional instructions to append to the prompt                              | No      | `""`  |
| `gemini-api-key` | API key for Google Gemini                                                  | Yes     | -     |

## Caveats

### Infrastructure Access

Even though Gemini has been instructed to never run `terraform apply`, this may not always be obeyed perfectly. Ensure the environment running this action has **read-only access** to your infrastructure to prevent any accidental modifications.

### Accuracy

Gemini may not perfectly understand complex Terraform configurations or drift scenarios. Always review changes before committing, and consider running `terraform plan` after remediation to verify no additional drift remains.

### Cost

Using Gemini API will incur costs based on model usage. Monitor your Google Cloud billing accordingly.


## Outputs

| Name          | Description                                                                 |
|---------------|-----------------------------------------------------------------------------|
| `has_changes` | `true` if the action modified any Terraform files                            |

## Usage

### Basic Usage

```yaml
- name: Remediate drift with Gemini
  uses: sooraj-satheesh/tf-drift-integrator-agent@main
  with:
    plan-command: 'terraform plan -no-color -detailed-exitcode || true'
    gemini-api-key: ${{ secrets.GEMINI_API_KEY }}
```

### Advanced Usage

```yaml
- name: Remediate drift with custom settings
  uses: sooraj-satheesh/tf-drift-integrator-agent@main
  with:
    plan-command: 'terraform plan -no-color -detailed-exitcode || true'
    workdir: './terraform'  # Custom working directory
    gemini-model: 'gemini-2.5-pro'  # Specify model, make sure that your API key has access to it
    gemini-api-key: ${{ secrets.GEMINI_API_KEY }}
```

If `workdir` and `gemini-model` are omitted, they default to `./` and `gemini-2.5-flash` respectively.

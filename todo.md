# Code-Keeper Checklist

## Foundation

- [ ] Create separate repositories for inventory, billing, API gateway, and infrastructure.
- [ ] Use the provided `crud-master` applications and a `cloud-design` or `cloud-kube` architecture.
- [ ] Provision equivalent staging and production environments on a cloud platform.

## GitLab Platform

- [x] Write an Ansible playbook to install and configure GitLab.
- [x] Deploy GitLab and make it accessible to the team.
- [ ] Configure authentication, project, and CI/CD settings.
- [x] Install and register GitLab Runners.
- [ ] Confirm runners execute jobs for every repository.
- [x] Prepare a demonstration using `ansible-playbook --list-tasks` and service status commands.

## Infrastructure Repository

- [ ] Keep Terraform infrastructure in its own repository.
- [ ] Configure remote/state backends appropriately.
- [ ] Implement Terraform pipeline stages:
- [ ] `init`
- [ ] `validate`
- [ ] `plan`
- [ ] apply to staging
- [ ] manual production approval
- [ ] apply to production
- [ ] Verify staging and production have comparable resources and services.

## Application CI: Each Repository

- [ ] Trigger CI on every push and merge/pull request.
- [ ] Build and package the application.
- [ ] Run unit and integration tests.
- [ ] Scan source code and dependencies for vulnerabilities and quality issues.
- [ ] Build a Docker image from a Dockerfile.
- [ ] Push the image to a container registry.

## Application CD: Each Repository

- [ ] Deploy the CI-built image to staging automatically.
- [ ] Validate the staging deployment.
- [ ] Require manual approval for production.
- [ ] Deploy the approved image to production.
- [ ] Ensure production rollout has no downtime.
- [ ] Confirm a source change rebuilds, deploys to staging, and reaches production only after approval.

## Application Integration

- [ ] Run inventory service connected to its database.
- [ ] Run billing service connected to its database.
- [ ] Configure billing to consume RabbitMQ messages.
- [ ] Run API gateway and forward requests to the internal services.

## Security

- [x] Restrict deploy-capable pipelines to protected branches.
- [x] Store secrets outside code and Terraform files.
- [ ] Use CI/CD variables or a secret-management solution.
- [ ] Apply least-privilege access for users, runners, cloud identities, and services.
- [ ] Maintain a process for dependency and CI tool updates.
- [ ] Monitor security advisories.

## Documentation And Audit

- [ ] Add a complete `README.md`.
- [ ] Document prerequisites, architecture, setup, configuration, usage, pipelines, secrets, and rollback/deployment process.
- [ ] Include clear architecture and pipeline diagrams.
- [ ] Commit CI/CD definitions, deployment scripts, Terraform, and Ansible artifacts.
- [ ] Demonstrate successful application and infrastructure updates through pipelines.
- [ ] Prepare explanations of DevOps, IaC, GitLab/Runners, Ansible, Terraform, CI/CD, security, tradeoffs, challenges, and cost optimization.

## Optional Bonus

- [ ] Add `tfsec` to the infrastructure pipeline.
- [ ] Add Infracost estimates.
- [ ] Use Terragrunt for environment management.
- [ ] Extend or replace the provided `crud-master` implementation.

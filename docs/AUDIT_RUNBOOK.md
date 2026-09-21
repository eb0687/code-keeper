# Code-Keeper Audit Runbook

## Local GitLab Evidence

```sh
ansible-playbook -i ansible/inventories/vagrant/hosts.yml ansible/playbooks/gitlab.yml --list-tasks
vagrant ssh -c "sudo docker ps"
vagrant ssh -c "sudo docker exec gitlab-runner gitlab-runner verify"
vagrant ssh -c "sudo docker exec gitlab-runner-ci gitlab-runner verify"
```

Show the GitLab group `code-keeper`, its four private projects, protected
`main` branches, runner tags, and the successful smoke-test/CI jobs.

## Infrastructure Evidence

From `../cloud-design/terraform`:

```sh
terraform init -backend=false
terraform fmt -check -recursive
terraform validate
```

For a real cloud audit, show separate staging and production state keys,
Terraform plans, the staging apply job, manual production approval, and the
production apply job. Do not expose AWS credentials or the Terraform secret
file.

## Application Evidence

For each application repository, show a pipeline with build, test, scan,
containerization, staging deploy, manual production approval, and production
deploy jobs. Show that all deployment images are tagged with the same commit
SHA and that the production job promotes rather than rebuilds the staging image.

## Design Explanation

- GitLab is local because the brief explicitly permits a local GitLab instance.
- Merge-request CI uses an unprotected runner without cloud credentials.
- Cloud publishing and deployment use a protected runner and protected `main`.
- Each cloud environment has independent Terraform state and matching EKS
  services.
- PostgreSQL and RabbitMQ are private Kubernetes services; only the API gateway
  is public through the AWS load balancer.
- Production gateway and inventory updates use rolling updates with two replicas
  and `maxUnavailable: 0`.

ShowInSidebar: false
---

# Minimal environment

* 1 Consul host
* 1 Vault host
* 1 Jenkins master
* 1 Build agent

Additionaly you may want

* 1 artefact server

Note that there is no redundancy in this environment so failure of any of the machines
will break the environment.

## Environments

- Only one environment with a minimum of 4 machines
- Updates can be tricky because there's only one of each resource (especially for consul)

## Repositories

- Need repositories for the code you want to build
- Ideally all configurations would be stored in one or more repositories as well but you could
  apply them once manually. The only catch is that you then don't have backups
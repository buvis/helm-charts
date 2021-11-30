# MkDocs git sync
Use this chart to deploy MkDocs site generated from a git repo. When source repository changes, the site will be rebuilt.

## TL;DR

```console
helm repo add buvis https://charts.buvis.net
helm repo update
helm install mkdocs buvis/mkdocs-git-sync
```

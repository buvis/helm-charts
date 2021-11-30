[Helm](https://helm.sh) must be installed to use the charts.  Please refer to
Helm's [documentation](https://helm.sh/docs) to get started.

Once Helm has been set up correctly, add the repo as follows:
``` bash
helm repo add buvis https://charts.buvis.net
```

If you had already added this repo earlier, run `helm repo update` to retrieve
the latest versions of the packages.  You can then run `helm search repo
buvis` to see the charts.

## Charts

### mkdocs-git-sync
Use this chart to deploy MkDocs site generated from a git repo. When source repository changes, the site will be rebuilt.

``` bash
helm repo add buvis https://charts.buvis.net
helm repo update
helm show values buvis/mkdocs-git-sync > values.yaml
vim values.yaml  # make changes to config section
helm install mkdocs buvis/mkdocs-git-sync -f values.yaml
```

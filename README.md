## buvis helm charts

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

### mopidy

Use this chart to deploy [Mopidy music server](https://mopidy.com/).

``` bash
helm repo add buvis https://charts.buvis.net
helm repo update
helm show values buvis/mopidy > values.yaml
vim values.yaml  # make changes to config section
helm install mkdocs buvis/mopidy -f values.yaml
```

### snapserver

Use this chart to deploy [Snapcast server](https://github.com/badaix/snapcast).

`` bash
helm repo add buvis https://charts.buvis.net
helm repo update
helm show values buvis/snapserver > values.yaml
vim values.yaml  # make changes to config section
helm install mkdocs buvis/snapserver -f values.yaml
```

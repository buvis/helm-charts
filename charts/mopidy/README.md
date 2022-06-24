# Mopidy

Use this chart to deploy [Mopidy music server](https://mopidy.com/).

## TL;DR

``` bash
helm repo add buvis https://charts.buvis.net
helm repo update
helm show values buvis/mopidy > values.yaml
vim values.yaml  # make changes to config section
helm install mkdocs buvis/mopidy -f values.yaml
```

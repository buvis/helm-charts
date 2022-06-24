# Snapserver

Use this chart to deploy [Snapcast server](https://github.com/badaix/snapcast).

## TL;DR

``` bash
helm repo add buvis https://charts.buvis.net
helm repo update
helm show values buvis/snapserver > values.yaml
vim values.yaml  # make changes to config section
helm install mkdocs buvis/snapserver -f values.yaml
```

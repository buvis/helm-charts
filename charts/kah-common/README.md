# Purpose
This is a generic chart powered by k8s at home library chart. It can be used to deploy applications providing docker images, but no helm charts. It was inspired by https://github.com/bjw-s/k8s-gitops/tree/main/charts/kah-common-chart.

# Update
When k8s at home releases a new version of the library chart, you will receive an email.

1. Download the package from https://github.com/k8s-at-home/library-charts/releases directly to charts directory (avoid Downloads folder, because the file will get unzipped and renamed)
2. Remove the old version of the chart from charts directory
3. Update Chart.yaml dependency version

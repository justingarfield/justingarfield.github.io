# Projects -> Kubernetes -> Talos Linux

```bash
talosctl gen secrets -o secrets.yaml

talosctl gen config --with-secrets secrets.yaml hybrid-cluster https://192.168.68.8:6443


```
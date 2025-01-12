```bash
# normal installation
helm install demo ./

# uninstall to run more tests
helm uninstall demo

# Replace value from command line
helm install demo ./ --set Service.type=NodePort

```
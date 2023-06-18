## Queue a local context as a Linux build, tag it, and push it to the registry.

- az acr build --image sample/hello-world:{{.Run.ID}} --registry MyRegistry .

- az acr build --image myorgprodacr.azurecr.io/nginx:v1 --registry myorgprodacr -g test-rg .
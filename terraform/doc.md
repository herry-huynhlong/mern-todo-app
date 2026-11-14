```bash
docker run --name ubuntu-terraform -d -it -v ./terraform:/root/terraform ubuntu

docker exec -it ubuntu-terraform bash

mv terraform /usr/local/bin/

bash install.sh --system

apt update && apt install -y curl unzip

aws --version

aws configure

aws sts get-caller-identity

apt update && apt install -y less

terraform fmt

terraform init

terraform validate

terraform apply

terraform plan

terraform destroy
```
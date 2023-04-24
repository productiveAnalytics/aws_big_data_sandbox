# Terraform script to create 2 VPCs and establish Peering Network

Refer: https://github.com/linuxacademy/content-deploying-to-aws-ansible-terraform.git

## Check TF version
`terraform version`

## Initialize TF
`terraform init`

## Format TF
`terraform fmt`

## Validate TF script
`terraform validate`

## Plan (save output in "network_setup_tf.out")
`terraform plan -out network_setup_tf.out`

# Execute
`terraform apply network_setup_tf.out`

# See the output
`terraform show`

# [Optional] Tear down
`terraform destroy`

# F5-XC-AZURE-MULTINODE

This repository consists of Terraform templates to bring up a F5XC Azure multi node environment.

## Usage

- Clone this repo with: `git clone --recurse-submodules https://github.com/cklewar/f5-xc-azure-multinode`
- Enter repository directory with: `cd f5-xc-azure-multinode`
- Obtain F5XC API certificate file from Console and save it to `cert` directory
- Pick and choose from below examples and add mandatory input data and copy data into file `main.tf.example`.
- Rename file __main.tf.example__ to __main.tf__ with: `rename main.tf.example main.tf`
- Initialize with: `terraform init`
- Apply with: `terraform apply -auto-approve` or destroy with: `terraform destroy -auto-approve`

### Example Output

```bash
Plan: 2 to add, 0 to change, 0 to destroy.
module.azure_multi_node.volterra_azure_vnet_site.vnet: Creating...
module.azure_multi_node.volterra_azure_vnet_site.vnet: Creation complete after 1s [id=b0573f45-9d5a-483b-a027-ca433977fb38]
module.azure_multi_node.volterra_tf_params_action.azure_vnet_action: Creating...
module.azure_multi_node.volterra_tf_params_action.azure_vnet_action: Still creating... [10s elapsed]
module.azure_multi_node.volterra_tf_params_action.azure_vnet_action: Still creating... [20s elapsed]
module.azure_multi_node.volterra_tf_params_action.azure_vnet_action: Still creating... [30s elapsed]
module.azure_multi_node.volterra_tf_params_action.azure_vnet_action: Still creating... [40s elapsed]
module.azure_multi_node.volterra_tf_params_action.azure_vnet_action: Still creating... [50s elapsed]
module.azure_multi_node.volterra_tf_params_action.azure_vnet_action: Still creating... [1m0s elapsed]
module.azure_multi_node.volterra_tf_params_action.azure_vnet_action: Still creating... [1m10s elapsed]
module.azure_multi_node.volterra_tf_params_action.azure_vnet_action: Still creating... [1m20s elapsed]
module.azure_multi_node.volterra_tf_params_action.azure_vnet_action: Still creating... [1m30s elapsed]
```

## Single NIC module usage example

````hcl
module "azure_multi_node_single_nic_new_vnet" {
  source                       = "./modules/f5xc/site/azure"
  f5xc_api_p12_file            = var.f5xc_api_p12_file
  f5xc_api_url                 = var.f5xc_api_url
  f5xc_namespace               = var.f5xc_namespace
  f5xc_tenant                  = var.f5xc_tenant
  f5xc_azure_cred              = var.f5xc_azure_cred
  f5xc_azure_region            = var.f5xc_azure_region
  f5xc_azure_site_name         = format("%s-m-node-s-nic-new-vnet-%s", var.project_prefix, var.project_suffix)
  f5xc_azure_vnet_primary_ipv4 = "192.168.168.0/21"
  f5xc_azure_ce_gw_type        = "single_nic"
  f5xc_azure_az_nodes          = {
    node0 : { f5xc_azure_az = "1", f5xc_azure_vnet_local_subnet = "192.168.168.0/24" },
    node1 : { f5xc_azure_az = "1", f5xc_azure_vnet_local_subnet = "192.168.170.0/24" },
    node2 : { f5xc_azure_az = "1", f5xc_azure_vnet_local_subnet = "192.168.172.0/24" }
  }
  f5xc_azure_default_blocked_services = false
  f5xc_azure_default_ce_sw_version    = true
  f5xc_azure_default_ce_os_version    = true
  f5xc_azure_no_worker_nodes          = false
  f5xc_azure_total_worker_nodes       = 2
  azure_client_id                     = var.azure_client_id
  azure_client_secret                 = var.azure_client_secret
  azure_tenant_id                     = var.azure_tenant_id
  azure_subscription_id               = var.azure_subscription_id
  public_ssh_key                      = var.public_ssh_key
}
````

## Multi NIC module usage example

````hcl
module "azure_multi_node_multi_nic_new_vnet" {
  source                       = "./modules/f5xc/site/azure"
  f5xc_api_p12_file            = var.f5xc_api_p12_file
  f5xc_api_url                 = var.f5xc_api_url
  f5xc_namespace               = var.f5xc_namespace
  f5xc_tenant                  = var.f5xc_tenant
  f5xc_azure_cred              = var.f5xc_azure_cred
  f5xc_azure_region            = var.f5xc_azure_region
  f5xc_azure_site_name         = format("%s-m-node-m-nic-new-vnet-%s", var.project_prefix, var.project_suffix)
  f5xc_azure_vnet_primary_ipv4 = "100.64.16.0/20"
  f5xc_azure_ce_gw_type        = "multi_nic"
  f5xc_azure_az_nodes          = {
    node0 : {
      f5xc_azure_az                  = "1", f5xc_azure_vnet_inside_subnet = "100.64.16.0/24",
      f5xc_azure_vnet_outside_subnet = "100.64.17.0/24"
    }
  }
  f5xc_azure_default_blocked_services = false
  f5xc_azure_default_ce_sw_version    = true
  f5xc_azure_default_ce_os_version    = true
  f5xc_azure_no_worker_nodes          = true
  f5xc_azure_total_worker_nodes       = 0
  azure_client_id                     = var.azure_client_id
  azure_client_secret                 = var.azure_client_secret
  azure_tenant_id                     = var.azure_tenant_id
  azure_subscription_id               = var.azure_subscription_id
  public_ssh_key                      = var.public_ssh_key
}
````


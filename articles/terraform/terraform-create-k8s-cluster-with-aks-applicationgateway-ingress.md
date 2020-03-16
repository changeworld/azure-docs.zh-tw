---
title: 教學課程 - 在 Azure Kubernetes Service 中建立應用程式閘道輸入控制器
description: 在本教學課程中，您會使用 Azure Kubernetes Service 建立 Kubernetes 叢集，並以應用程式閘道作為輸入控制器
keywords: azure devops terraform 應用程式閘道輸入 aks kubernetes
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 6b48d0acb654f0b0643c0754e53f6bc6ea76bb45
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2020
ms.locfileid: "78945327"
---
# <a name="tutorial-create-an-application-gateway-ingress-controller-in-azure-kubernetes-service"></a>教學課程：在 Azure Kubernetes Service 中建立應用程式閘道輸入控制器

[Azure Kubernetes Service (AKS)](/azure/aks/) 會管理裝載的 Kubernetes 環境。 AKS 可快速且輕鬆地部署和管理容器化的應用程式，而不需要容器協調流程專業知識。 AKS 也消除了讓應用程式離線進行操作和維護工作的負擔。 使用 AKS，您可以視需要完成這些工作，包括佈建、升級和調整資源。

輸入控制器會針對 Kubernetes 服務提供各種功能。 這些功能包括反向 Proxy、可設定的流量路由和 TLS 終止。 Kubernetes 輸入資源可用來設定個別 Kubernetes 服務的輸入規則。 透過輸入控制器和輸入規則，單一 IP 位址可以將流量路由至 Kubernetes 叢集中的多個服務。 所有這些功能都由 Azure [應用程式閘道](/azure/Application-Gateway/)所提供，因此成為 Azure 上的 Kubernetes 的理想輸入控制器。 

在本教學課程中，您將了解如何執行下列工作：

> [!div class="checklist"]
> * 使用 AKS 搭配應用程式閘道作為輸入控制器，建立 [Kubernetes](https://www.redhat.com/en/topics/containers/what-is-kubernetes) 叢集。
> * 使用 HCL (HashiCorp 語言) 定義 Kubernetes 叢集。
> * 使用 Terraform 建立應用程式閘道資源。
> * 使用 Terraform 和 AKS 建立 Kubernetes 叢集。
> * 使用 kubectl 工具測試 Kubernetes 叢集的可用性。

## <a name="prerequisites"></a>Prerequisites

- **Azure 訂用帳戶**：如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

- **設定 Terraform**：請遵循 [Terraform 和設定 Azure 的存取](terraform-install-configure.md)一文中的指示

- **Azure 資源群組**：如果您沒有可用於示範的 Azure 資源群組，[請建立 Azure 資源群組](/azure/azure-resource-manager/manage-resource-groups-portal#create-resource-groups)。 請記下資源群組名稱和位置，因為示範時會用到這些值。

- **Azure 服務主體**：請遵循[使用 Azure CLI 建立 Azure 服務主體](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)一文中**建立服務主體**一節的指示。 記下 appId、displayName 和密碼的值。

- **取得服務主體物件識別碼**：在 Cloud Shell 中執行下列命令︰`az ad sp list --display-name <displayName>`

## <a name="create-the-directory-structure"></a>建立目錄結構

第一個步驟是先建立目錄，用來存放供練習使用的 Terraform 組態檔。

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)。

1. 開啟 [Azure Cloud Shell](/azure/cloud-shell/overview)。

1. 切換至 `clouddrive` 目錄。

    ```bash
    cd clouddrive
    ```

1. 建立名為 `terraform-aks-appgw-ingress` 的目錄。

    ```bash
    mkdir terraform-aks-appgw-ingress
    ```

1. 將目錄變更為新的目錄：

    ```bash
    cd terraform-aks-appgw-ingress
    ```

## <a name="declare-the-azure-provider"></a>宣告 Azure 提供者

建立宣告 Azure 提供者的 Terraform 組態檔。

1. 在 Cloud Shell 中建立名稱為 `main.tf` 的檔案。

    ```bash
    code main.tf
    ```

1. 將下列程式碼貼到編輯器中：

    ```hcl
    provider "azurerm" {
      # The "feature" block is required for AzureRM provider 2.x. 
      # If you are using version 1.x, the "features" block is not allowed.
      version = "~>2.0"
      features {}
    }

    terraform {
        backend "azurerm" {}
    }
    ```

1. 儲存檔案 ( **&lt;Ctrl>S**) 並結束編輯器 ( **&lt;Ctrl>Q**)。

## <a name="define-input-variables"></a>定義輸入變數

建立可列出此部署所需所有變數的 Terraform 組態檔。

1. 在 Cloud Shell 中建立名稱為 `variables.tf` 的檔案。

    ```bash
    code variables.tf
    ```

1. 將下列程式碼貼到編輯器中：
    
    ```hcl
    variable "resource_group_name" {
      description = "Name of the resource group."
    }

    variable "location" {
      description = "Location of the cluster."
    }

    variable "aks_service_principal_app_id" {
      description = "Application ID/Client ID  of the service principal. Used by AKS to manage AKS related resources on Azure like vms, subnets."
    }

    variable "aks_service_principal_client_secret" {
      description = "Secret of the service principal. Used by AKS to manage Azure."
    }

    variable "aks_service_principal_object_id" {
      description = "Object ID of the service principal."
    }

    variable "virtual_network_name" {
      description = "Virtual network name"
      default     = "aksVirtualNetwork"
    }

    variable "virtual_network_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.0.0.0/8"
    }

    variable "aks_subnet_name" {
      description = "AKS Subnet Name."
      default     = "kubesubnet"
    }

    variable "aks_subnet_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.0.0.0/16"
    }

    variable "app_gateway_subnet_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.1.0.0/16"
    }

    variable "app_gateway_name" {
      description = "Name of the Application Gateway."
      default = "ApplicationGateway1"
    }

    variable "app_gateway_sku" {
      description = "Name of the Application Gateway SKU."
      default = "Standard_v2"
    }

    variable "app_gateway_tier" {
      description = "Tier of the Application Gateway SKU."
      default = "Standard_v2"
    }

    variable "aks_name" {
      description = "Name of the AKS cluster."
      default     = "aks-cluster1"
    }
    variable "aks_dns_prefix" {
      description = "Optional DNS prefix to use with hosted Kubernetes API server FQDN."
      default     = "aks"
    }

    variable "aks_agent_os_disk_size" {
      description = "Disk size (in GB) to provision for each of the agent pool nodes. This value ranges from 0 to 1023. Specifying 0 applies the default disk size for that agentVMSize."
      default     = 40
    }

    variable "aks_agent_count" {
      description = "The number of agent nodes for the cluster."
      default     = 3
    }

    variable "aks_agent_vm_size" {
      description = "The size of the Virtual Machine."
      default     = "Standard_D3_v2"
    }

    variable "kubernetes_version" {
      description = "The version of Kubernetes."
      default     = "1.11.5"
    }

    variable "aks_service_cidr" {
      description = "A CIDR notation IP range from which to assign service cluster IPs."
      default     = "10.0.0.0/16"
    }

    variable "aks_dns_service_ip" {
      description = "Containers DNS server IP address."
      default     = "10.0.0.10"
    }

    variable "aks_docker_bridge_cidr" {
      description = "A CIDR notation IP for Docker bridge."
      default     = "172.17.0.1/16"
    }

    variable "aks_enable_rbac" {
      description = "Enable RBAC on the AKS cluster. Defaults to false."
      default     = "false"
    }

    variable "vm_user_name" {
      description = "User name for the VM"
      default     = "vmuser1"
    }

    variable "public_ssh_key_path" {
      description = "Public key path for SSH."
      default     = "~/.ssh/id_rsa.pub"
    }

    variable "tags" {
      type = "map"

      default = {
        source = "terraform"
      }
    }
    ```

1. 儲存檔案 ( **&lt;Ctrl>S**) 並結束編輯器 ( **&lt;Ctrl>Q**)。

## <a name="define-the-resources"></a>定義資源 
建立可建立所有資源的 Terraform 組態檔。 

1. 在 Cloud Shell 中建立名稱為 `resources.tf` 的檔案。

    ```bash
    code resources.tf
    ```

1. 貼上下列程式碼區塊，以建立要重複使用之計算變數的區域變數區塊：

    ```hcl
    # # Locals block for hardcoded names. 
    locals {
        backend_address_pool_name      = "${azurerm_virtual_network.test.name}-beap"
        frontend_port_name             = "${azurerm_virtual_network.test.name}-feport"
        frontend_ip_configuration_name = "${azurerm_virtual_network.test.name}-feip"
        http_setting_name              = "${azurerm_virtual_network.test.name}-be-htst"
        listener_name                  = "${azurerm_virtual_network.test.name}-httplstn"
        request_routing_rule_name      = "${azurerm_virtual_network.test.name}-rqrt"
        app_gateway_subnet_name = "appgwsubnet"
    }
    ```

1. 貼上下列程式碼區塊，以建立資源群組、新使用者身分識別的資料來源：

    ```hcl
    data "azurerm_resource_group" "rg" {
      name = var.resource_group_name
    }

    # User Assigned Identities 
    resource "azurerm_user_assigned_identity" "testIdentity" {
      resource_group_name = data.azurerm_resource_group.rg.name
      location            = data.azurerm_resource_group.rg.location

      name = "identity1"

      tags = var.tags
    }
    ```

1. 貼上下列程式碼區塊，以建立基礎網路資源：

    ```hcl
    resource "azurerm_virtual_network" "test" {
      name                = var.virtual_network_name
      location            = data.azurerm_resource_group.rg.location
      resource_group_name = data.azurerm_resource_group.rg.name
      address_space       = [var.virtual_network_address_prefix]

      subnet {
        name           = var.aks_subnet_name
        address_prefix = var.aks_subnet_address_prefix
      }

      subnet {
        name           = "appgwsubnet"
        address_prefix = var.app_gateway_subnet_address_prefix
      }

      tags = var.tags
    }

    data "azurerm_subnet" "kubesubnet" {
      name                 = var.aks_subnet_name
      virtual_network_name = azurerm_virtual_network.test.name
      resource_group_name  = data.azurerm_resource_group.rg.name
    }

    data "azurerm_subnet" "appgwsubnet" {
      name                 = "appgwsubnet"
      virtual_network_name = azurerm_virtual_network.test.name
      resource_group_name  = data.azurerm_resource_group.rg.name
    }

    # Public Ip 
    resource "azurerm_public_ip" "test" {
      name                         = "publicIp1"
      location                     = data.azurerm_resource_group.rg.location
      resource_group_name          = data.azurerm_resource_group.rg.name
      allocation_method            = "Static"
      sku                          = "Standard"

      tags = var.tags
    }
    ```

1. 貼上下列程式碼區塊，以建立應用程式閘道資源：

    ```hcl
    resource "azurerm_application_gateway" "network" {
      name                = var.app_gateway_name
      resource_group_name = data.azurerm_resource_group.rg.name
      location            = data.azurerm_resource_group.rg.location

      sku {
        name     = var.app_gateway_sku
        tier     = "Standard_v2"
        capacity = 2
      }

      gateway_ip_configuration {
        name      = "appGatewayIpConfig"
        subnet_id = data.azurerm_subnet.appgwsubnet.id
      }

      frontend_port {
        name = local.frontend_port_name
        port = 80
      }

      frontend_port {
        name = "httpsPort"
        port = 443
      }

      frontend_ip_configuration {
        name                 = local.frontend_ip_configuration_name
        public_ip_address_id = azurerm_public_ip.test.id
      }

      backend_address_pool {
        name = local.backend_address_pool_name
      }

      backend_http_settings {
        name                  = local.http_setting_name
        cookie_based_affinity = "Disabled"
        port                  = 80
        protocol              = "Http"
        request_timeout       = 1
      }

      http_listener {
        name                           = local.listener_name
        frontend_ip_configuration_name = local.frontend_ip_configuration_name
        frontend_port_name             = local.frontend_port_name
        protocol                       = "Http"
      }

      request_routing_rule {
        name                       = local.request_routing_rule_name
        rule_type                  = "Basic"
        http_listener_name         = local.listener_name
        backend_address_pool_name  = local.backend_address_pool_name
        backend_http_settings_name = local.http_setting_name
      }

      tags = var.tags

      depends_on = ["azurerm_virtual_network.test", "azurerm_public_ip.test"]
    }
    ```

1. 貼上下列程式碼區塊，以建立角色指派：

    ```hcl
    resource "azurerm_role_assignment" "ra1" {
      scope                = data.azurerm_subnet.kubesubnet.id
      role_definition_name = "Network Contributor"
      principal_id         = var.aks_service_principal_object_id 

      depends_on = ["azurerm_virtual_network.test"]
    }

    resource "azurerm_role_assignment" "ra2" {
      scope                = azurerm_user_assigned_identity.testIdentity.id
      role_definition_name = "Managed Identity Operator"
      principal_id         = var.aks_service_principal_object_id
      depends_on           = ["azurerm_user_assigned_identity.testIdentity"]
    }

    resource "azurerm_role_assignment" "ra3" {
      scope                = azurerm_application_gateway.network.id
      role_definition_name = "Contributor"
      principal_id         = azurerm_user_assigned_identity.testIdentity.principal_id
      depends_on           = ["azurerm_user_assigned_identity.testIdentity", "azurerm_application_gateway.network"]
    }

    resource "azurerm_role_assignment" "ra4" {
      scope                = data.azurerm_resource_group.rg.id
      role_definition_name = "Reader"
      principal_id         = azurerm_user_assigned_identity.testIdentity.principal_id
      depends_on           = ["azurerm_user_assigned_identity.testIdentity", "azurerm_application_gateway.network"]
    }
    ```

1. 貼上下列程式碼區塊，以建立 Kubernetes 叢集：

    ```hcl
    resource "azurerm_kubernetes_cluster" "k8s" {
      name       = var.aks_name
      location   = data.azurerm_resource_group.rg.location
      dns_prefix = var.aks_dns_prefix

      resource_group_name = data.azurerm_resource_group.rg.name

      linux_profile {
        admin_username = var.vm_user_name

        ssh_key {
          key_data = file(var.public_ssh_key_path)
        }
      }

      addon_profile {
        http_application_routing {
          enabled = false
        }
      }

      default_node_pool {
        name            = "agentpool"
        node_count      = var.aks_agent_count
        vm_size         = var.aks_agent_vm_size
        os_disk_size_gb = var.aks_agent_os_disk_size
        vnet_subnet_id  = data.azurerm_subnet.kubesubnet.id
      }

      service_principal {
        client_id     = var.aks_service_principal_app_id
        client_secret = var.aks_service_principal_client_secret
      }

      network_profile {
        network_plugin     = "azure"
        dns_service_ip     = var.aks_dns_service_ip
        docker_bridge_cidr = var.aks_docker_bridge_cidr
        service_cidr       = var.aks_service_cidr
      }

      depends_on = ["azurerm_virtual_network.test", "azurerm_application_gateway.network"]
      tags       = var.tags
    }

    ```

1. 儲存檔案 ( **&lt;Ctrl>S**) 並結束編輯器 ( **&lt;Ctrl>Q**)。

此區段中呈現的程式碼可設定叢集名稱、位置和 resource_group_name。 也能設定屬於完整網域名稱 (FQDN) 一部分的 `dns_prefix` 值 (完整網域名稱可用來存取叢集)。

`linux_profile` 記錄可讓您設定使用 SSH 登入背景工作角色節點的設定。

透過 AKS，您只需要為背景工作角色節點付費。 `agent_pool_profile` 記錄可設定這些背景工作角色節點的詳細資料。 `agent_pool_profile record` 涵蓋要建立的背景工作角色節點數量，以及背景工作角色節點的類型。 若您日後需要擴大或縮小叢集，可修改此記錄中的 `count` 值。

## <a name="create-a-terraform-output-file"></a>建立 Terraform 輸出檔

[Terraform 輸出檔](https://www.terraform.io/docs/configuration/outputs.html)可讓您定義 Terraform 套用方案後要對使用者醒目提示的值，以及讓您能使用 `terraform output` 命令查詢此輸出檔。 在本節中，您可建立允許透過 [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/) 存取叢集的輸出檔。

1. 在 Cloud Shell 中建立名稱為 `output.tf` 的檔案。

    ```bash
    code output.tf
    ```

1. 將下列程式碼貼到編輯器中：

    ```hcl
    output "client_key" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.client_key
    }

    output "client_certificate" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.client_certificate
    }

    output "cluster_ca_certificate" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.cluster_ca_certificate
    }

    output "cluster_username" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.username
    }

    output "cluster_password" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.password
    }

    output "kube_config" {
        value = azurerm_kubernetes_cluster.k8s.kube_config_raw
    }

    output "host" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.host
    }

    output "identity_resource_id" {
        value = azurerm_user_assigned_identity.testIdentity.id
    }

    output "identity_client_id" {
        value = azurerm_user_assigned_identity.testIdentity.client_id
    }
    ```

1. 儲存檔案 ( **&lt;Ctrl>S**) 並結束編輯器 ( **&lt;Ctrl>Q**)。

## <a name="configure-azure-storage-to-store-terraform-state"></a>設定 Azure 儲存體以儲存 Terraform 狀態

Terraform 可透過 `terraform.tfstate` 檔案在本機追蹤狀態。 此模式在單一人員環境中運作良好。 不過，在更常見的多人環境中，您必須追蹤使用 [Azure 儲存體](/azure/storage/)的伺服器的狀態。 在本節中，您會了解如何取得必要的儲存體帳戶資訊，並建立儲存體容器。 然後，Terraform 狀態資訊會儲存在該容器中。

1. 在 Azure 入口網站的 [Azure 服務]  底下，選取 [儲存體帳戶]  。 (如果主頁上未顯示**儲存體帳戶**選項，請選取 [更多服務]  ，然後尋找並選取儲存體帳戶。)

1. 在 [儲存體帳戶]  頁面中，選取要讓 Terraform 儲存狀態的帳戶名稱。 例如，您可以使用第一次開啟 Cloud Shell 時建立的儲存體帳戶。  Cloud Shell 建立的儲存體帳戶名稱通常會以 `cs` 開頭，其後加上由數字和字母組成的隨機字串。 

    請記下您所選儲存體帳戶的名稱，您稍後需要用到。

1. 在 [儲存體帳戶] 頁面上，選取 [存取金鑰]  。

    ![[儲存體帳戶] 功能表](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account.png)

1. 請記下 **key1** 的**金鑰**值。 (選取金鑰右邊的圖示，將值複製到剪貼簿。)

    ![儲存體帳戶存取金鑰](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account-access-key.png)

1. 透過 Cloud Shell，在 Azure 儲存體帳戶中建立容器。 以 Azure 儲存體帳戶的適當值取代預留位置。

    ```azurecli
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>建立 Kubernetes 叢集
在本節中，您可了解如何使用 `terraform init` 命令建立資源，即您在前述小節中建立的組態檔所定義的資源。

1. 在 Cloud Shell 中，初始化 Terraform。 以 Azure 儲存體帳戶的適當值取代預留位置。

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
  
    `terraform init` 命令會顯示後端和提供者外掛程式已成功初始化：

    ![「terraform 初始化」結果範例](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-init-complete.png)

1. 在 Cloud Shell 中建立名稱為 `terraform.tfvars` 的檔案：

    ```bash
    code terraform.tfvars
    ```

1. 將稍早建立的下列變數貼到編輯器中。 若要取得環境的位置值，請使用 `az account list-locations`。

    ```hcl
    resource_group_name = "<Name of the Resource Group already created>"

    location = "<Location of the Resource Group>"
      
    aks_service_principal_app_id = "<Service Principal AppId>"
      
    aks_service_principal_client_secret = "<Service Principal Client Secret>"
      
    aks_service_principal_object_id = "<Service Principal Object Id>"
        
    ```

1. 儲存檔案 ( **&lt;Ctrl>S**) 並結束編輯器 ( **&lt;Ctrl>Q**)。

1. 執行 `terraform plan` 命令以建立用來定義基礎結構元素的 Terraform 方案。 

    ```bash
    terraform plan -out out.plan
    ```

    `terraform plan` 命令可顯示您執行 `terraform apply` 命令時將會建立的資源：

    ![「terraform 方案」結果範例](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-plan-complete.png)

1. 執行 `terraform apply` 命令以套用方案，並建立 Kubernetes 叢集。 建立 Kubernetes 叢集的程序可能需要數分鐘，而這會導致 Cloud Shell 工作階段逾時。如果 Cloud Shell 工作階段逾時，請遵循「從 Cloud Shell 逾時復原」一節中的步驟以完成教學課程。

    ```bash
    terraform apply out.plan
    ```

    `terraform apply` 命令可顯示建立資源 (即組態檔中定義的資源) 的結果：

    ![「terraform 套用」結果範例](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-apply-complete.png)

1. 在 Azure 入口網站中，選取左側功能表中的 [資源群組]  以查看在選取的資源群組中，針對新 Kubernetese 叢集建立的資源。

    ![Cloud Shell 提示](./media/terraform-k8s-cluster-appgw-with-tf-aks/k8s-resources-created.png)

## <a name="recover-from-a-cloud-shell-timeout"></a>從 Cloud Shell 逾時復原

如果 Cloud Shell 工作階段逾時，您可以使用下列步驟復原：

1. 開啟 Cloud Shell 工作階段。

1. 變更為包含 Terraform 組態檔的目錄。

    ```bash
    cd /clouddrive/terraform-aks-k8s
    ```

1. 執行以下命令：

    ```bash
    export KUBECONFIG=./azurek8s
    ```
  
## <a name="test-the-kubernetes-cluster"></a>測試 Kubernetes 叢集
Kubernetes 工具可用來驗證新建立的叢集。

1. 從 Terraform 狀態取得 Kubernetes 組態，並將其儲存在 kubectl 可讀取的檔案中。

    ```bash
    echo "$(terraform output kube_config)" > ./azurek8s
    ```

1. 設定環境變數，以便 kubectl 挑選正確的組態。

    ```bash
    export KUBECONFIG=./azurek8s
    ```

1. 驗證叢集的健康情況。

    ```bash
    kubectl get nodes
    ```

    您可查看背景工作角色節點的詳細資料，這些節點應該都處於 [就緒]  狀態，如下圖所示：

    ![kubectl 工具可讓您驗證 Kubernetes 叢集的健康情況](./media/terraform-k8s-cluster-appgw-with-tf-aks/kubectl-get-nodes.png)

## <a name="install-azure-ad-pod-identity"></a>安裝 Azure AD Pod 身分識別

Azure Active Directory Pod 身分識別會對 [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview) 提供以權杖為基礎的存取。

[Azure AD Pod 身分識別](https://github.com/Azure/aad-pod-identity)會將下列元件新增至您的 Kubernetes 叢集：

  - Kubernetes [CRD](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/)：`AzureIdentity`、`AzureAssignedIdentity`、`AzureIdentityBinding`
  - [受控識別控制器 (MIC)](https://github.com/Azure/aad-pod-identity#managed-identity-controllermic) 元件
  - [節點受控識別 (NMI)](https://github.com/Azure/aad-pod-identity#node-managed-identitynmi) 元件

如果 RBAC 為**啟用狀態**，請執行下列命令，將 Azure AD Pod 身分識別安裝到您的叢集：

```bash
kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
```

如果 RBAC 為**停用狀態**，請執行下列命令，將 Azure AD Pod 身分識別安裝到您的叢集：

```bash
kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
```

## <a name="install-helm"></a>安裝 Helm

此區段中的程式碼使用 [Helm](/azure/aks/kubernetes-helm) (Kubernetes 套件管理員) 安裝 `application-gateway-kubernetes-ingress` 套件：

1. 如果 RBAC 為**啟用狀態**，請執行下列一組命令來安裝並設定 Helm：

    ```bash
    kubectl create serviceaccount --namespace kube-system tiller-sa
    kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
    helm init --tiller-namespace kube-system --service-account tiller-sa
    ```

1. 如果 RBAC 為**停用狀態**，請執行下列命令來安裝並設定 Helm：

    ```bash
    helm init
    ```

1. 新增 AGIC Helm 存放庫：

    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

## <a name="install-ingress-controller-helm-chart"></a>安裝輸入控制器 Helm 圖表

1. 下載 `helm-config.yaml` 以設定 AGIC：

    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```

1. 編輯 `helm-config.yaml`，並為 `appgw` 和 `armAuth` 區段輸入適當的值。

    ```bash
    code helm-config.yaml
    ```

    這些步驟的描述如下：

    - `verbosityLevel`:設定 AGIC 記錄基礎結構的詳細資訊層級。 如需可能的值，請參閱[記錄層級](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/463a87213bbc3106af6fce0f4023477216d2ad78/docs/troubleshooting.md#logging-levels) \(英文\)。
    - `appgw.subscriptionId`:應用程式閘道的 Azure 訂用帳戶識別碼。 範例： `a123b234-a3b4-557d-b2df-a0bc12de1234`
    - `appgw.resourceGroup`:建立應用程式閘道所在 Azure 資源群組的名稱。 
    - `appgw.name`:應用程式閘道的名稱。 範例： `applicationgateway1`.
    - `appgw.shared`:此布林值旗標應該預設為 `false`。 如果您需要 [共用應用程式閘道](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-existing.md#multi-cluster--shared-app-gateway)，請將設定為 `true`。
    - `kubernetes.watchNamespace`:指定 AGIC 應該監看的命名空間。 命名空間可以是單一字串值，或逗號分隔的命名空間清單。 將此變數保留為註解，或將其設定為空白或空字串，會讓輸入控制器看到所有可存取的命名空間。
    - `armAuth.type`:`aadPodIdentity` 或 `servicePrincipal` 的值。
    - `armAuth.identityResourceID`:受控識別的資源識別碼。
    - `armAuth.identityClientId`:身分識別的用戶端識別碼。
    - `armAuth.secretJSON`:只有在選擇服務主體秘密類型 (當 `armAuth.type` 已設定為 `servicePrincipal`) 時才需要。

    重要注意事項：
    - `identityResourceID` 值是在 terraform 指令碼中建立的，而且可以透過執行 `echo "$(terraform output identity_resource_id)"` 找到。
    - `identityClientID` 值是在 terraform 指令碼中建立的，而且可以透過執行 `echo "$(terraform output identity_client_id)"` 找到。
    - `<resource-group>` 值是應用程式閘道的資源群組。
    - `<identity-name>` 值是所建立身分識別的名稱。
    - 指定訂用帳戶的所有身分識別都可以使用 `az identity list` 列出。

1. 安裝應用程式閘道輸入控制器套件：

    ```bash
    helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure
    ```

### <a name="install-a-sample-app"></a>安裝範例應用程式

安裝應用程式閘道、AKS 和 AGIC 之後，您可以透過 [Azure Cloud Shell](https://shell.azure.com/) 安裝範例應用程式：

1. 使用 curl 命令下載 YAML 檔案：

    ```bash
    curl https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml -o aspnetapp.yaml
    ```

2. 套用 YAML 檔案：

    ```bash
    kubectl apply -f aspnetapp.yaml
    ```

## <a name="clean-up-resources"></a>清除資源

如果不再需要本文中建立的資源，請予以刪除。  

使用適當的值來取代預留位置。 指定資源群組內的所有資源都將刪除。

```azurecli
az group delete -n <resource-group>
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"] 
> [應用程式閘道輸入控制器](https://azure.github.io/application-gateway-kubernetes-ingress/)
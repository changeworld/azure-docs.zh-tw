---
title: 教學課程 - 使用 Azure Kubernetes Service (AKS) 和 Terraform 建立 Kubernetes 叢集
description: 在本教學課程中，您會以 Azure Kubernetes Service 和 Terraform 建立 Kubernetes 叢集
keywords: azure devops terraform aks kubernetes
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 0a193c8da6441a04f742894797521fe92f26b2e1
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2020
ms.locfileid: "78945300"
---
# <a name="tutorial-create-a-kubernetes-cluster-with-azure-kubernetes-service-using-terraform"></a>教學課程：使用 Azure Kubernetes Service 和 Terraform 建立 Kubernetes 叢集

[Azure Kubernetes Service (AKS)](/azure/aks/) 會管理裝載的 Kubernetes 環境。 AKS 可讓您部署和管理容器化應用程式，而無須具備容器協調流程專業知識。 AKS 也可讓您直接執行許多常見的維護作業，而不需要先讓應用程式離線。 這些作業包括視需要佈建、升級及調整資源。

在本教學課程中，您將了解如何執行下列工作：

> [!div class="checklist"]
> * 使用 HCL (HashiCorp 語言) 定義 Kubernetes 叢集
> * 使用 Terraform 和 AKS 建立 Kubernetes 叢集
> * 使用 kubectl 工具測試 Kubernetes 叢集的可用性

## <a name="prerequisites"></a>Prerequisites

- **Azure 訂用帳戶**：如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

- **設定 Terraform**：請遵循 [Terraform 和設定 Azure 的存取](terraform-install-configure.md)一文中的指示

- **Azure 服務主體**：請遵循[使用 Azure CLI 建立 Azure 服務主體](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)一文中**建立服務主體**一節的指示。 記下 appId、displayName、密碼和租用戶欄位內的值。

## <a name="create-the-directory-structure"></a>建立目錄結構

第一個步驟是先建立目錄，用來存放供練習使用的 Terraform 組態檔。

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)。

1. 開啟 [Azure Cloud Shell](/azure/cloud-shell/overview)。 如果您先前未選取環境，請選取 **Bash** 作為您的環境。

    ![Cloud Shell 提示](./media/terraform-create-k8s-cluster-with-tf-and-aks/azure-portal-cloud-shell-button-min.png)

1. 切換至 `clouddrive` 目錄。

    ```bash
    cd clouddrive
    ```

1. 建立名為 `terraform-aks-k8s` 的目錄。

    ```bash
    mkdir terraform-aks-k8s
    ```

1. 將目錄變更為新的目錄：

    ```bash
    cd terraform-aks-k8s
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

## <a name="define-a-kubernetes-cluster"></a>定義 Kubernetes 叢集

建立宣告 Kubernetes 叢集資源的 Terraform 組態檔。

1. 在 Cloud Shell 中建立名稱為 `k8s.tf` 的檔案。

    ```bash
    code k8s.tf
    ```

1. 將下列程式碼貼到編輯器中：

    ```hcl
    resource "azurerm_resource_group" "k8s" {
        name     = var.resource_group_name
        location = var.location
    }
    
    resource "random_id" "log_analytics_workspace_name_suffix" {
        byte_length = 8
    }

    resource "azurerm_log_analytics_workspace" "test" {
        # The WorkSpace name has to be unique across the whole of azure, not just the current subscription/tenant.
        name                = "${var.log_analytics_workspace_name}-${random_id.log_analytics_workspace_name_suffix.dec}"
        location            = var.log_analytics_workspace_location
        resource_group_name = azurerm_resource_group.k8s.name
        sku                 = var.log_analytics_workspace_sku
    }

    resource "azurerm_log_analytics_solution" "test" {
        solution_name         = "ContainerInsights"
        location              = azurerm_log_analytics_workspace.test.location
        resource_group_name   = azurerm_resource_group.k8s.name
        workspace_resource_id = azurerm_log_analytics_workspace.test.id
        workspace_name        = azurerm_log_analytics_workspace.test.name

        plan {
            publisher = "Microsoft"
            product   = "OMSGallery/ContainerInsights"
        }
    }

    resource "azurerm_kubernetes_cluster" "k8s" {
        name                = var.cluster_name
        location            = azurerm_resource_group.k8s.location
        resource_group_name = azurerm_resource_group.k8s.name
        dns_prefix          = var.dns_prefix

        linux_profile {
            admin_username = "ubuntu"

            ssh_key {
                key_data = file(var.ssh_public_key)
            }
        }

        default_node_pool {
            name            = "agentpool"
            node_count      = var.agent_count
            vm_size         = "Standard_DS1_v2"
        }

        service_principal {
            client_id     = var.client_id
            client_secret = var.client_secret
        }

        addon_profile {
            oms_agent {
            enabled                    = true
            log_analytics_workspace_id = azurerm_log_analytics_workspace.test.id
            }
        }

        tags = {
            Environment = "Development"
        }
    }
    ```

    上述程式碼可設定叢集名稱、位置和資源群組名稱。 此外也會設定完整網域名稱 (FQDN) 的前置詞。 FQDN 可用來存取叢集。

    `linux_profile` 記錄可讓您設定使用 SSH 登入背景工作角色節點的設定。

    透過 AKS，您只需要為背景工作角色節點付費。 `default_node_pool` 記錄可設定這些背景工作角色節點的詳細資料。 `default_node_pool record` 涵蓋要建立的背景工作角色節點數量，以及背景工作角色節點的類型。 若您日後需要擴大或縮小叢集，可修改此記錄中的 `count` 值。

1. 儲存檔案 ( **&lt;Ctrl>S**) 並結束編輯器 ( **&lt;Ctrl>Q**)。

## <a name="declare-the-variables"></a>宣告變數

1. 在 Cloud Shell 中建立名稱為 `variables.tf` 的檔案。

    ```bash
    code variables.tf
    ```

1. 將下列程式碼貼到編輯器中：

    ```hcl
    variable "client_id" {}
    variable "client_secret" {}

    variable "agent_count" {
        default = 3
    }

    variable "ssh_public_key" {
        default = "~/.ssh/id_rsa.pub"
    }

    variable "dns_prefix" {
        default = "k8stest"
    }

    variable cluster_name {
        default = "k8stest"
    }

    variable resource_group_name {
        default = "azure-k8stest"
    }

    variable location {
        default = "Central US"
    }

    variable log_analytics_workspace_name {
        default = "testLogAnalyticsWorkspaceName"
    }

    # refer https://azure.microsoft.com/global-infrastructure/services/?products=monitor for log analytics available regions
    variable log_analytics_workspace_location {
        default = "eastus"
    }

   # refer https://azure.microsoft.com/pricing/details/monitor/ for log analytics pricing 
   variable log_analytics_workspace_sku {
        default = "PerGB2018"
   }
    ```

1. 儲存檔案 ( **&lt;Ctrl>S**) 並結束編輯器 ( **&lt;Ctrl>Q**)。

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
    ```

1. 儲存檔案 ( **&lt;Ctrl>S**) 並結束編輯器 ( **&lt;Ctrl>Q**)。

## <a name="set-up-azure-storage-to-store-terraform-state"></a>設定 Azure 儲存體以儲存 Terraform 狀態

Terraform 可透過 `terraform.tfstate` 檔案在本機追蹤狀態。 此模式在單一人員環境中運作良好。 在多人環境中，可使用 [Azure 儲存體](/azure/storage/)來追蹤狀態。

在本節中，您將了解如何執行下列工作：
- 擷取儲存體帳戶資訊 (帳戶名稱和帳戶金鑰)
- 建立用來儲存 Terraform 狀態資訊的儲存體容器。

1. 在 Azure 入口網站中，選取左側功能表中的 [所有服務]  。

1. 選取 [儲存體帳戶]  。

1. 在 [儲存體帳戶]  索引標籤中，選取要讓 Terraform 儲存狀態的帳戶名稱。 例如，您可以使用第一次開啟 Cloud Shell 時建立的儲存體帳戶。  Cloud Shell 建立的儲存體帳戶名稱通常會以 `cs` 開頭，其後加上由數字和字母組成的隨機字串。 請記下您所選儲存體帳戶的名稱。 後續將會用到此值。

1. 在 [儲存體帳戶] 索引標籤中，選取 [存取金鑰]  。

    ![[儲存體帳戶] 功能表](./media/terraform-create-k8s-cluster-with-tf-and-aks/storage-account.png)

1. 請記下 **key1** 的**金鑰**值。 (選取金鑰右邊的圖示，將值複製到剪貼簿。)

    ![儲存體帳戶存取金鑰](./media/terraform-create-k8s-cluster-with-tf-and-aks/storage-account-access-key.png)

1. 透過 Cloud Shell，在 Azure 儲存體帳戶中建立容器。 請將預留位置取代為您的環境適用的值。

    ```azurecli
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>建立 Kubernetes 叢集

在本節中，您可了解如何使用 `terraform init` 命令建立資源，即您在前述小節中建立的組態檔所定義的資源。

1. 在 Cloud Shell 中，初始化 Terraform。 請將預留位置取代為您的環境適用的值。

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
    
    `terraform init` 命令會顯示後端和提供者外掛程式已成功初始化：

    ![「terraform 初始化」結果範例](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-init-complete.png)

1. 匯出服務主體認證。 請將預留位置取代為您服務主體中的適當值。

    ```bash
    export TF_VAR_client_id=<service-principal-appid>
    export TF_VAR_client_secret=<service-principal-password>
    ```

1. 執行 `terraform plan` 命令以建立用來定義基礎結構元素的 Terraform 方案。 

    ```bash
    terraform plan -out out.plan
    ```

    `terraform plan` 命令可顯示您執行 `terraform apply` 命令時將會建立的資源：

    ![「terraform 方案」結果範例](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-plan-complete.png)

1. 執行 `terraform apply` 命令以套用方案，並建立 Kubernetes 叢集。 建立 Kubernetes 叢集的程序可能需要數分鐘，而這會導致 Cloud Shell 工作階段逾時。如果 Cloud Shell 工作階段逾時，請遵循「從 Cloud Shell 逾時復原」一節中的步驟以完成教學課程。

    ```bash
    terraform apply out.plan
    ```

    `terraform apply` 命令可顯示建立資源 (即組態檔中定義的資源) 的結果：

    ![「terraform 套用」結果範例](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-apply-complete.png)

1. 在 Azure 入口網站中，選取左側功能表中的 [所有資源]  ，以查看針對新的 Kuberneteses 叢集建立的資源。

    ![Azure 入口網站中的所有資源](./media/terraform-create-k8s-cluster-with-tf-and-aks/k8s-resources-created.png)

## <a name="recover-from-a-cloud-shell-timeout"></a>從 Cloud Shell 逾時復原

如果 Cloud Shell 工作階段逾時，您可以執行下列步驟加以復原：

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

    ![kubectl 工具可讓您驗證 Kubernetes 叢集的健康情況](./media/terraform-create-k8s-cluster-with-tf-and-aks/kubectl-get-nodes.png)

## <a name="monitor-health-and-logs"></a>監視健康情況和記錄

建立 AKS 叢集時，啟用了監視功能來擷取叢集節點和 pod 的健康狀態計量。 在 Azure 入口網站中可取得這些健康狀態度量。 如需容器健康情況監視的詳細資訊，請參閱[監視 Azure Kubernetes Service 健康情況](/azure/azure-monitor/insights/container-insights-overview)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"] 
> [深入了解如何使用 Azure 中的 Terraform](/azure/terraform)
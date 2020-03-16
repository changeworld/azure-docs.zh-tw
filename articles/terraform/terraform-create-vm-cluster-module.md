---
title: 教學課程 - 使用模組登錄以 Terraform 建立 Azure VM 叢集
description: 在本教學課程中，您會在 Azure 中使用 Terraform 模組建立 Windows 虛擬機器叢集
keywords: azure devops terraform vm 虛擬機器叢集模組登錄
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 9faeee9bb2f0fb6dc148a3868f6fc0dae3833a2a
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2020
ms.locfileid: "78945280"
---
# <a name="tutorial-create-an-azure-vm-cluster-with-terraform-using-the-module-registry"></a>教學課程：使用模組登錄以 Terraform 建立 Azure VM 叢集

本文逐步引導您使用 Terraform [Azure 計算模組](https://registry.terraform.io/modules/Azure/compute/azurerm/1.0.2)建立小 VM 叢集。 在本教學課程中，您將了解如何： 

> [!div class="checklist"]
> * 使用 Azure 設定驗證
> * 建立 Terraform 範本
> * 使用 plan 視覺化變更
> * 套用設定以建立 VM 叢集

如需 Terraform 的詳細資訊，請參閱 [Terraform 文件](https://www.terraform.io/docs/index.html)。

## <a name="set-up-authentication-with-azure"></a>使用 Azure 設定驗證

> [!TIP]
> 如果您[使用 Terraform 環境變數](terraform-install-configure.md)或在 [Azure Cloud Shell](/azure/cloud-shell/overview) 中執行本教學課程，請略過此步驟。

 檢閱[安裝 Terraform 並設定對 Azure 的存取權限](terraform-install-configure.md)，來建立 Azure 服務主體。 使用這個服務主體與下列程式碼，將新檔案 `azureProviderAndCreds.tf` 植入空的目錄中：

```hcl
variable subscription_id {}
variable tenant_id {}
variable client_id {}
variable client_secret {}

provider "azurerm" {
    version = "~>1.40"

    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

## <a name="create-the-template"></a>建立範本

使用下列程式碼，建立名為 `main.tf` 的新 Terraform 範本：

```hcl
module mycompute {
    source = "Azure/compute/azurerm"
    resource_group_name = "myResourceGroup"
    location = "East US 2"
    admin_password = "ComplxP@assw0rd!"
    vm_os_simple = "WindowsServer"
    is_windows_image = "true"
    remote_port = "3389"
    nb_instances = 2
    public_ip_dns = ["unique_dns_name"]
    vnet_subnet_id = module.network.vnet_subnets[0]
}

module "network" {
    source = "Azure/network/azurerm"
    location = "East US 2"
    resource_group_name = "myResourceGroup"
}

output "vm_public_name" {
    value = module.mycompute.public_ip_dns_name
}

output "vm_public_ip" {
    value = module.mycompute.public_ip_address
}

output "vm_private_ips" {
    value = module.mycompute.network_interface_private_ip
}
```

在設定目錄中執行`terraform init`。 使用至少 0.10.6 版的 Terraform 來顯示下列輸出：

![Terraform 初始化](media/terraformInitWithModules.png)

## <a name="visualize-the-changes-with-plan"></a>使用 plan 視覺化變更

執行 `terraform plan`，預覽範本所建立的虛擬機器基礎結構。

![Terraform Plan](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)


## <a name="create-the-virtual-machines-with-apply"></a>使用 apply 建立虛擬機器

在 Azure 上執行 `terraform apply` 以佈建 VM。

![Terraform Apply](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"] 
> [瀏覽 Azure Terraform 模組清單](https://registry.terraform.io/modules/Azure)
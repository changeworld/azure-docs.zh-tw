---
title: 教學課程 - 使用 Terraform 和 HCL 建立 Azure VM 叢集
description: 使用 Terraform 和 HCL，在 Azure 中建立具有負載平衡器的 Linux 虛擬機器叢集
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 39e9857ad0119c08e949bbe5f6accb07432f3469
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/19/2020
ms.locfileid: "77470865"
---
# <a name="tutorial-create-an-azure-vm-cluster-with-terraform-and-hcl"></a>教學課程：使用 Terraform 和 HCL 建立 Azure VM 叢集

在本教學課程中，您會了解如何使用 [HCL](https://www.terraform.io/docs/configuration/syntax.html) 建立小型計算叢集。 

您將了解如何執行下列工作：

> [!div class="checklist"]
> * 設定 Azure 驗證。
> * 建立 Terraform 組態檔。
> * 使用 Terraform 組態檔建立負載平衡器。
> * 使用 Terraform 組態檔將兩個 Linux VM 部署在可用性設定組中。
> * 初始化 Terraform。
> * 建立 Terraform 執行計畫。
> * 套用 Terraform 執行計畫以建立 Azure 資源。

## <a name="1-set-up-azure-authentication"></a>1.設定 Azure 驗證

> [!NOTE]
> 如果您是[使用 Terraform 環境變數](terraform-install-configure.md)，或是在 [Azure Cloud Shell](terraform-cloud-shell.md) 中執行本教學課程，請略過此小節。

在本節中，您會產生一個 Azure 服務主體，以及兩個包含來自安全性主體之認證的 Terraform 設定檔。

1. [設定 Azure AD 服務主體](terraform-install-configure.md#set-up-terraform-access-to-azure)以讓 Terraform 將資源佈建至 Azure。 建立主體時，請記下訂用帳戶識別碼、租用戶、應用程式識別碼及密碼的值。

2. 開啟命令提示字元。

3. 建立空目錄以在其中儲存您的 Terraform 檔案。

4. 建立會保存您變數宣告的新檔案。 您可以隨意命名此檔案，再加上 `.tf` 副檔名。

5. 將下列程式碼複製到您的變數宣告檔案：

   ```hcl
   variable subscription_id {}
   variable tenant_id {}
   variable client_id {}
   variable client_secret {}
  
   provider "azurerm" {
      subscription_id = var.subscription_id
      tenant_id = var.tenant_id
      client_id = var.client_id
      client_secret = var.client_secret
   }
   ```

6. 建立包含您 Terraform 變數值的新檔案。 常見的作法是將您的 Terraform 變數檔案命名為 `terraform.tfvars`，因為 Terraform 會自動載入目前目錄中名為 `terraform.tfvars` (或遵循 `*.auto.tfvars` 的模式) 的檔案 (如果存在的話)。 

7. 將下列程式碼複製到您的變數檔案。 請務必依照下列方式取代預留位置：針對 `subscription_id`，請使用您在執行 `az account set` 時所指定的 Azure 訂用帳戶識別碼。 針對 `tenant_id`，請使用 `az ad sp create-for-rbac` 所傳回的 `tenant` 值。 針對 `client_id`，請使用 `az ad sp create-for-rbac` 所傳回的 `appId` 值。 針對 `client_secret`，請使用 `az ad sp create-for-rbac` 所傳回的 `password` 值。

   ```hcl
   subscription_id = "<azure-subscription-id>"
   tenant_id = "<tenant-returned-from-creating-a-service-principal>"
   client_id = "<appId-returned-from-creating-a-service-principal>"
   client_secret = "<password-returned-from-creating-a-service-principal>"
   ```

## <a name="2-create-a-terraform-configuration-file"></a>2.建立 Terraform 設定檔

在本節中，您會建立包含您基礎結構之資源定義的檔案。

1. 建立名為 `main.tf` 的新檔案。 

2. 將下列範例資源定義複製到新建立的 `main.tf` 檔案： 

   ```hcl
   resource "azurerm_resource_group" "test" {
    name     = "acctestrg"
    location = "West US 2"
   }

   resource "azurerm_virtual_network" "test" {
    name                = "acctvn"
    address_space       = ["10.0.0.0/16"]
    location            = azurerm_resource_group.test.location
    resource_group_name = azurerm_resource_group.test.name
   }

   resource "azurerm_subnet" "test" {
    name                 = "acctsub"
    resource_group_name  = azurerm_resource_group.test.name
    virtual_network_name = azurerm_virtual_network.test.name
    address_prefix       = "10.0.2.0/24"
   }

   resource "azurerm_public_ip" "test" {
    name                         = "publicIPForLB"
    location                     = azurerm_resource_group.test.location
    resource_group_name          = azurerm_resource_group.test.name
    allocation_method            = "Static"
   }

   resource "azurerm_lb" "test" {
    name                = "loadBalancer"
    location            = azurerm_resource_group.test.location
    resource_group_name = azurerm_resource_group.test.name

    frontend_ip_configuration {
      name                 = "publicIPAddress"
      public_ip_address_id = azurerm_public_ip.test.id
    }
   }

   resource "azurerm_lb_backend_address_pool" "test" {
    resource_group_name = azurerm_resource_group.test.name
    loadbalancer_id     = azurerm_lb.test.id
    name                = "BackEndAddressPool"
   }

   resource "azurerm_network_interface" "test" {
    count               = 2
    name                = "acctni${count.index}"
    location            = azurerm_resource_group.test.location
    resource_group_name = azurerm_resource_group.test.name

    ip_configuration {
      name                          = "testConfiguration"
      subnet_id                     = azurerm_subnet.test.id
      private_ip_address_allocation = "dynamic"
      load_balancer_backend_address_pools_ids = [azurerm_lb_backend_address_pool.test.id]
    }
   }

   resource "azurerm_managed_disk" "test" {
    count                = 2
    name                 = "datadisk_existing_${count.index}"
    location             = azurerm_resource_group.test.location
    resource_group_name  = azurerm_resource_group.test.name
    storage_account_type = "Standard_LRS"
    create_option        = "Empty"
    disk_size_gb         = "1023"
   }

   resource "azurerm_availability_set" "avset" {
    name                         = "avset"
    location                     = azurerm_resource_group.test.location
    resource_group_name          = azurerm_resource_group.test.name
    platform_fault_domain_count  = 2
    platform_update_domain_count = 2
    managed                      = true
   }

   resource "azurerm_virtual_machine" "test" {
    count                 = 2
    name                  = "acctvm${count.index}"
    location              = azurerm_resource_group.test.location
    availability_set_id   = azurerm_availability_set.avset.id
    resource_group_name   = azurerm_resource_group.test.name
    network_interface_ids = [element(azurerm_network_interface.test.*.id, count.index)]
    vm_size               = "Standard_DS1_v2"

    # Uncomment this line to delete the OS disk automatically when deleting the VM
    # delete_os_disk_on_termination = true

    # Uncomment this line to delete the data disks automatically when deleting the VM
    # delete_data_disks_on_termination = true

    storage_image_reference {
      publisher = "Canonical"
      offer     = "UbuntuServer"
      sku       = "16.04-LTS"
      version   = "latest"
    }

    storage_os_disk {
      name              = "myosdisk${count.index}"
      caching           = "ReadWrite"
      create_option     = "FromImage"
      managed_disk_type = "Standard_LRS"
    }

    # Optional data disks
    storage_data_disk {
      name              = "datadisk_new_${count.index}"
      managed_disk_type = "Standard_LRS"
      create_option     = "Empty"
      lun               = 0
      disk_size_gb      = "1023"
    }

    storage_data_disk {
      name            = element(azurerm_managed_disk.test.*.name, count.index)
      managed_disk_id = element(azurerm_managed_disk.test.*.id, count.index)
      create_option   = "Attach"
      lun             = 1
      disk_size_gb    = element(azurerm_managed_disk.test.*.disk_size_gb, count.index)
    }

    os_profile {
      computer_name  = "hostname"
      admin_username = "testadmin"
      admin_password = "Password1234!"
    }

    os_profile_linux_config {
      disable_password_authentication = false
    }

    tags = {
      environment = "staging"
    }
   }
   ```

## <a name="3-initialize-terraform"></a>3.初始化 Terraform 

[terraform init 命令](https://www.terraform.io/docs/commands/init.html) \(英文\) 是用來初始化包含 Terraform 設定檔 (也就是您在先前小節建立的檔案) 的目錄。 撰寫新的 Terraform 組態之後，最好一律執行 `terraform init` 命令。 

> [!TIP]
> `terraform init` 命令具有等冪性，表示它能夠被重複呼叫並不斷產生相同的結果。 因此，如果您是在共同作業環境中工作，且認為設定檔可能已經被他人變更，最好先一律呼叫 `terraform init` 命令，然後再執行或套用計畫。

若要初始化 Terraform，請執行下列命令：

  ```bash
  terraform init
  ```

  ![正在初始化 Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-init.png)

## <a name="4-create-a-terraform-execution-plan"></a>4.建立 Terraform 執行計畫

[terraform plan 命令](https://www.terraform.io/docs/commands/plan.html) \(英文\) 是用來建立執行計畫。 若要產生執行計畫，Terraform 會彙總目前目錄中的所有 `.tf` 檔案。 

[-out 參數](https://www.terraform.io/docs/commands/plan.html#out-path)會將執行計畫儲存至輸出檔案。 這項功能可解決多重開發環境中常見的並行問題。 下列案例是輸出檔案所解決的其中一個問題：

1. 開發人員 1 建立了組態檔。
1. 開發人員 2 修改了組態檔。
1. 開發人員 1 套用 (執行) 了組態檔。
1. 開發人員 1 不知道開發人員 2 已修改組態，而取得了非預期的結果。

指定輸出檔案的開發人員 1 使開發人員 2 無法對開發人員 1 產生影響。 

如果您不需要儲存執行計畫，請執行下列命令：

  ```bash
  terraform plan
  ```

如果您需要儲存執行計畫，請執行下列命令。 請將預留位置取代為您的環境適用的值。

  ```bash
  terraform plan -out=<path>
  ```

另一個有用的參數是 [-var-file](https://www.terraform.io/docs/commands/plan.html#var-file-foo)。

根據預設，Terraform 會嘗試尋找您的變數檔案，如下所示：
- 名為 `terraform.tfvars` 的檔案
- 使用下列模式命名的檔案：`*.auto.tfvars`

不過，您的變數檔案不需要遵循上述兩項慣例中的任一個。 在此情況下，請使用 `-var-file` 參數來指定變數檔案名稱；其中的變數檔案名稱不含副檔名。 下列範例會說明這一點：

```hcl
terraform plan -var-file <my-variables-file>
```

Terraform 會判斷要達成設定檔中指定的狀態所需的動作。

![正在建立 Terraform 執行計畫](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)

## <a name="5-apply-the-terraform-execution-plan"></a>5.套用 Terraform 執行計畫

本教學課程的最後一個步驟是使用 [terraform apply 命令](https://www.terraform.io/docs/commands/apply.html) \(英文\) 來套用由 `terraform plan` 命令所產生的一組動作。

如果您想要套用最新的執行計畫，請執行下列命令：

  ```bash
  terraform apply
  ```

如果您想要套用先前儲存的執行計畫，請執行下列命令。 請將預留位置取代為您的環境適用的值：

  ```bash
  terraform apply <path>
  ```

![正在套用 Terraform 執行計畫](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"] 
> [使用 Terraform 建立 Azure 虛擬機器擴展集](terraform-create-vm-scaleset-network-disks-hcl.md)

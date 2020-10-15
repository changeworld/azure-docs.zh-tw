---
title: 教學課程 - 使用 Terraform 佈建 Azure Spring Cloud 執行個體
description: 使用 Terraform 佈建 Azure Spring Cloud 執行個體。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 06/26/2020
ms.custom: devx-track-java
ms.openlocfilehash: 4ef08ea03cab711901225cab227a0f5f84055d7a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90906808"
---
# <a name="tutorial-provision-an-azure-spring-cloud-instance-with-terraform"></a>教學課程：使用 Terraform 佈建 Azure Spring Cloud 執行個體

**本文適用於：** ✔️ Java ✔️ C#

本教學課程會使用 Terraform 建立 Azure Spring Cloud 執行個體。 這些程序會逐步引導您建立下列資源：

> [!div class="checklist"]
> * 資源群組
> * Azure Spring Cloud 執行個體
> * 適用於 Log Analytics 的 Azure 儲存體

> [!NOTE]
> 如需 Terraform 特定支援，請使用 HashiCorp 對 Terraform 的其中一個社群支援通道：
>
> * 問題、使用案例和有用的模式：[HashiCorp 社群入口網站的 Terraform 區段](https://discuss.hashicorp.com/c/terraform-core)
> * 提供者相關的問題：[HashiCorp 社群入口網站的 Terraform 提供者區段](https://discuss.hashicorp.com/c/terraform-providers)

## <a name="prerequisites"></a>必要條件

- **Azure 訂用帳戶**：如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

## <a name="create-configuration-file"></a>建立組態檔

1. 登入 [Azure 入口網站](https://go.microsoft.com/fwlink/p/?LinkID=525040)。

1. 開啟 [Azure Cloud Shell](https://docs.microsoft.com/azure/app-service/quickstart-java#use-azure-cloud-shell)。

1. 啟動 Cloud Shell 編輯器：

    ```bash
    code main.tf
    ```

1. 此步驟中的設定會為 Azure 資源建立模型 (包括 Azure 資源群組和 Azure Spring Cloud 執行個體)。

    ```hcl
    provider "azurerm" {
        features {}
    }

    resource "azurerm_resource_group" "example" {
      name     = "username"
      location = "eastus"
    }

    resource "azurerm_spring_cloud_service" "example" {
      name                = "usernamesp"
      resource_group_name = azurerm_resource_group.example.name
      location            = azurerm_resource_group.example.location

      config_server_git_setting {
        uri          = "https://github.com/Azure-Samples/piggymetrics-config"
        label        = "master"
        search_paths = ["."]
      }
    }
    ```

1. 儲存檔案 ( **&lt;Ctrl>S**) 並結束編輯器 ( **&lt;Ctrl>Q**)。

## <a name="apply-the-configuration"></a>套用設定

在本節中，您會使用數個 Terraform 命令來執行組態。

1. [terraform init](https://www.terraform.io/docs/commands/init.html) 命令會初始化工作目錄。 在 Cloud Shell 中執行下列命令︰

    ```bash
    terraform init
    ```

1. [terraform plan](https://www.terraform.io/docs/commands/plan.html) 命令可用來驗證設定語法。 `-out` 參數會將結果導向至檔案。 稍後可使用輸出檔案來套用組態。 在 Cloud Shell 中執行下列命令︰

    ```bash
    terraform plan --out plan.out
    ```

1. [terraform apply](https://www.terraform.io/docs/commands/apply.html) 命令可用來套用設定。 此命令會指定上一個步驟中的輸出檔案。 此命令會建立 Azure 資源。 在 Cloud Shell 中執行下列命令︰

    ```bash
    terraform apply plan.out
    ```

1. 若要確認 Azure 入口網站內的結果，請瀏覽至新的資源群組。 新的 **Azure Spring Cloud** 執行個體會顯示在新的資源群組中。

## <a name="update-configuration-to-config-logs-and-metrics"></a>更新設定以設定記錄和計量

本節說明如何使用 Azure 儲存體帳戶來更新設定，以啟用 Azure Spring Cloud 的記錄和計量。

1. 啟動 Cloud Shell 編輯器：

    ```bash
    code main.tf
    ```

1. 在檔案結尾處新增下列設定：

    ```hcl
    resource "azurerm_storage_account" "example" {
      name                     = "usernamest"
      resource_group_name      = azurerm_resource_group.example.name
      location                 = azurerm_resource_group.example.location
      account_tier             = "Standard"
      account_replication_type = "GRS"
    }

    resource "azurerm_monitor_diagnostic_setting" "example" {
      name               = "example"
      target_resource_id = "${azurerm_spring_cloud_service.example.id}"
      storage_account_id = "${azurerm_storage_account.example.id}"

      log {
        category = "SystemLogs"
        enabled  = true

        retention_policy {
          enabled = false
        }
      }

      metric {
        category = "AllMetrics"

        retention_policy {
          enabled = false
        }
      }
    }
    ```

1. 儲存檔案 ( **&lt;Ctrl>S**) 並結束編輯器 ( **&lt;Ctrl>Q**)。

1. 如上一節所示，執行下列命令以進行變更：

    ```bash
    terraform plan --out plan.out
    ```

1. 執行 `terraform apply` 命令以套用組態。

    ```bash
    terraform apply plan.out
    ```

## <a name="clean-up-resources"></a>清除資源

如果不再需要本文中建立的資源，請予以刪除。

執行 [terraform destroy](https://www.terraform.io/docs/commands/destroy.html) 命令，以移除在本教學課程中建立的 Azure 資源：

```bash
terraform destroy -auto-approve
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [安裝並設定 Terraform 以佈建 Azure 資源](https://docs.microsoft.com/azure/developer/terraform/getting-started-cloud-shell)。

---
title: 快速入門 - 建立 Azure 的 Terraform 組態
description: 部署 Azure Cosmos DB 和 Azure 容器執行個體，開始使用 Azure 上的 Terraform。
ms.topic: quickstart
ms.date: 10/26/2019
ms.openlocfilehash: 92f66fcfeac69b66eb8cdb0dc58b7a3171d45e10
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/19/2020
ms.locfileid: "77470016"
---
# <a name="quickstart-create-a-terraform-configuration-for-azure"></a>快速入門：建立 Azure 的 Terraform 組態

在本快速入門中，您將使用 Terraform 來建立 Azure 資源。 本文中的步驟會逐步引導您建立下列資源：

> [!div class="checklist"]
> * Azure Cosmos DB 執行個體
> * Azure 容器執行個體
> * 可跨這兩項資源運作的應用程式

## <a name="create-first-configuration"></a>建立第一個組態

在本節中，您將建立 Azure Cosmos DB 執行個體的組態。

1. 登入 [Azure 入口網站](https://go.microsoft.com/fwlink/p/?LinkID=525040)。

1. 開啟 Azure Cloud Shell。

1. 啟動 Cloud Shell 編輯器：

    ```bash
    code main.tf
    ```

1. 此步驟中的組態會建立幾項 Azure 資源的模型。 這些資源包括 Azure 資源群組和 Azure Cosmos DB 執行個體。 系統會使用隨機整數來建立唯一的 Cosmos DB 執行個體名稱。 此外也會設定數個 Cosmos DB 設定。 如需詳細資訊，請參閱 [Cosmos DB Terraform 參考](https://www.terraform.io/docs/providers/azurerm/r/cosmosdb_account.html)。 將下列 Terraform 組態複製並貼到文字編輯器中：

    ```hcl
    resource "azurerm_resource_group" "vote-resource-group" {
      name     = "vote-resource-group"
      location = "westus"
    }

    resource "random_integer" "ri" {
      min = 10000
      max = 99999
    }

    resource "azurerm_cosmosdb_account" "vote-cosmos-db" {
      name                = "tfex-cosmos-db-${random_integer.ri.result}"
      location            = azurerm_resource_group.vote-resource-group.location
      resource_group_name = azurerm_resource_group.vote-resource-group.name
      offer_type          = "Standard"
      kind                = "GlobalDocumentDB"

      consistency_policy {
        consistency_level       = "BoundedStaleness"
        max_interval_in_seconds = 10
        max_staleness_prefix    = 200
      }

      geo_location {
        location          = "westus"
        failover_priority = 0
      }
    }
    ```

1. 儲存檔案 ( **&lt;Ctrl>S**) 並結束編輯器 ( **&lt;Ctrl>Q**)。

## <a name="run-the-configuration"></a>執行組態

在本節中，您會使用數個 Terraform 命令來執行組態。

1. [terraform init](https://www.terraform.io/docs/commands/init.html) 命令會初始化工作目錄。 在 Cloud Shell 中執行下列命令︰

    ```bash
    terraform init
    ```

1. [terraform plan](https://www.terraform.io/docs/commands/plan.html) 命令可用來驗證組態語法。 `-out` 參數會將結果導向至檔案。 稍後可使用輸出檔案來套用組態。 在 Cloud Shell 中執行下列命令︰

    ```bash
    terraform plan --out plan.out
    ```

1. [terraform apply](https://www.terraform.io/docs/commands/apply.html) 命令可用來套用組態。 指定上一個步驟的輸出檔案。 此命令會建立 Azure 資源。 在 Cloud Shell 中執行下列命令︰

    ```bash
    terraform apply plan.out
    ```

1. 若要確認 Azure 入口網站內的結果，請瀏覽至新的資源群組。 新的 Azure Cosmos DB 執行個體會位於新的資源群組中。

## <a name="update-configuration"></a>更新設定

本節說明如何更新組態以包含 Azure 容器執行個體。 容器會執行對 Cosmos DB 讀取和寫入資料的應用程式。

1. 啟動 Cloud Shell 編輯器：

    ```bash
    code main.tf
    ```

1. 此步驟中的組態會設定兩個環境變數：`COSMOS_DB_ENDPOINT` 和 `COSMOS_DB_MASTERKEY`。 這些變數會保存用來存取資料庫的位置和金鑰。 這些變數的值取自先前的步驟中建立的資料庫執行個體。 此程序稱為插補。 若要深入了解 Terraform 插補，請參閱[插補語法](https://www.terraform.io/docs/configuration/interpolation.html)。 該組態也包含一個輸出區塊，此區塊會傳回容器執行個體的完整網域名稱 (FQDN)。 將下列程式碼複製並貼到編輯器中：

    ```hcl
    resource "azurerm_container_group" "vote-aci" {
      name                = "vote-aci"
      location            = azurerm_resource_group.vote-resource-group.location
      resource_group_name = azurerm_resource_group.vote-resource-group.name
      ip_address_type     = "public"
      dns_name_label      = "vote-aci"
      os_type             = "linux"

      container {
        name   = "vote-aci"
        image  = "microsoft/azure-vote-front:cosmosdb"
        cpu    = "0.5"
        memory = "1.5"
        ports {
          port     = 80
          protocol = "TCP"
        }

        secure_environment_variables = {
          "COSMOS_DB_ENDPOINT"  = azurerm_cosmosdb_account.vote-cosmos-db.endpoint
          "COSMOS_DB_MASTERKEY" = azurerm_cosmosdb_account.vote-cosmos-db.primary_master_key
          "TITLE"               = "Azure Voting App"
          "VOTE1VALUE"          = "Cats"
          "VOTE2VALUE"          = "Dogs"
        }
      }
    }

    output "dns" {
      value = azurerm_container_group.vote-aci.fqdn
    }
    ```

1. 儲存檔案 ( **&lt;Ctrl>S**) 並結束編輯器 ( **&lt;Ctrl>Q**)。

1. 如同您在上一節所做的，執行下列命令，以將要進行的變更視覺化：

    ```bash
    terraform plan --out plan.out
    ```

1. 執行 `terraform apply` 命令以套用組態。

    ```bash
    terraform apply plan.out
    ```

1. 記下容器執行個體的 FQDN。

## <a name="test-application"></a>測試應用程式

若要測試應用程式，請瀏覽至容器執行個體的 FQDN。 您應該會看到類似下面輸出的結果：

![Azure 投票應用程式](media/terraform-quickstart/azure-vote.jpg)

## <a name="clean-up-resources"></a>清除資源

如果不再需要本文中建立的資源，請予以刪除。

執行 [terraform destroy](https://www.terraform.io/docs/commands/destroy.html) 命令，以移除在本教學課程中建立的 Azure 資源：

```bash
terraform destroy -auto-approve
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [安裝並設定 Terraform 以佈建 Azure 資源](terraform-install-configure.md)。
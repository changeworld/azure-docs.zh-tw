---
title: 教學課程 - 使用 Terraform 佈建具有 Azure 部署位置的基礎結構
description: 在本教學課程中，您會使用 Terraform 和 Azure 提供者部署位置
keywords: azure devops terraform 部署位置
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: ddd4d84ee8bf4ab1e90dd68da185cdd9075fe1e0
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943494"
---
# <a name="tutorial-provision-infrastructure-with-azure-deployment-slots-using-terraform"></a>教學課程：使用 Terraform 佈建具有 Azure 部署位置的基礎結構

您可以使用 [Azure 部署位置](/azure/app-service/deploy-staging-slots)在不同版本的應用程式之間交換。 這項功能可協助您將中斷部署的影響降到最低。 

本文逐步引導您透過 GitHub 和 Azure 部署兩個應用程式，以說明部署位置的使用範例。 一個應用程式會裝載在生產位置中。 第二個應用程式會裝載在預備位置中。 (「生產」和「預備」名稱可任意指定。 它們可以是您的案例適用的任何名稱。)設定部署位置後，您可以視需要使用 Terraform 來交換兩個位置。

## <a name="prerequisites"></a>Prerequisites

- **Azure 訂用帳戶**：如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

- **GitHub 帳戶**：您需要有 [GitHub](https://www.github.com) 帳戶，才能派生和使用測試 GitHub 存放庫。

## <a name="create-and-apply-the-terraform-plan"></a>建立和套用 Terraform 計劃

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)。

1. 開啟 [Azure Cloud Shell](/azure/cloud-shell/overview)。 如果您先前未選取環境，請選取 **Bash** 作為您的環境。

    ![Cloud Shell 提示](./media/terraform-slot-walkthru/azure-portal-cloud-shell-button-min.png)

1. 切換至 `clouddrive` 目錄。

    ```bash
    cd clouddrive
    ```

1. 建立名為 `deploy` 的目錄。

    ```bash
    mkdir deploy
    ```

1. 建立名為 `swap` 的目錄。

    ```bash
    mkdir swap
    ```

1. 使用 `ls` bash 命令來確認您已成功建立這兩個目錄。

    ![建立目錄之後的 Cloud Shell](./media/terraform-slot-walkthru/cloud-shell-after-creating-dirs.png)

1. 切換至 `deploy` 目錄。

    ```bash
    cd deploy
    ```

1. 在 Cloud Shell 中建立名稱為 `deploy.tf` 的檔案。

    ```bash
    code deploy.tf
    ```

1. 將下列程式碼貼到編輯器中：

    ```hcl
    # Configure the Azure provider
    provider "azurerm" { 
        # The "feature" block is required for AzureRM provider 2.x. 
        # If you are using version 1.x, the "features" block is not allowed.
        version = "~>2.0"
        features {}
    }

    resource "azurerm_resource_group" "slotDemo" {
        name = "slotDemoResourceGroup"
        location = "westus2"
    }

    resource "azurerm_app_service_plan" "slotDemo" {
        name                = "slotAppServicePlan"
        location            = azurerm_resource_group.slotDemo.location
        resource_group_name = azurerm_resource_group.slotDemo.name
        sku {
            tier = "Standard"
            size = "S1"
        }
    }

    resource "azurerm_app_service" "slotDemo" {
        name                = "slotAppService"
        location            = azurerm_resource_group.slotDemo.location
        resource_group_name = azurerm_resource_group.slotDemo.name
        app_service_plan_id = azurerm_app_service_plan.slotDemo.id
    }

    resource "azurerm_app_service_slot" "slotDemo" {
        name                = "slotAppServiceSlotOne"
        location            = azurerm_resource_group.slotDemo.location
        resource_group_name = azurerm_resource_group.slotDemo.name
        app_service_plan_id = azurerm_app_service_plan.slotDemo.id
        app_service_name    = azurerm_app_service.slotDemo.name
    }
    ```

1. 儲存檔案 ( **&lt;Ctrl>S**) 並結束編輯器 ( **&lt;Ctrl>Q**)。

1. 既然您已建立檔案，請確認其內容。

    ```bash
    cat deploy.tf
    ```

1. 初始化 Terraform。

    ```bash
    terraform init
    ```

1. 建立 Terraform 計劃。

    ```bash
    terraform plan
    ```

1. 佈建 `deploy.tf` 組態檔中定義的資源。 (在提示字元輸入 `yes`，以確認此動作。)

    ```bash
    terraform apply
    ```

1. 關閉 Cloud Shell 視窗。

1. 在 Azure 入口網站的主功能表上，選取 [資源群組]  。

    ![入口網站中的「資源群組」選取項目](./media/terraform-slot-walkthru/resource-groups-menu-option.png)

1. 在 [資源群組]  索引標籤上，選取 [slotDemoResourceGroup]  。

    ![Terraform 所建立的資源群組](./media/terraform-slot-walkthru/resource-group.png)

現在，您會看到 Terraform 已建立的所有資源。

![Terraform 所建立的資源](./media/terraform-slot-walkthru/resources.png)

## <a name="fork-the-test-project"></a>派生測試專案

您必須先從 GitHub 派生測試專案，才能測試部署位置的建立及換入和換出。

1. 瀏覽至 [GitHub 上的 awesome-terraform 存放庫](https://github.com/Azure/awesome-terraform)。

1. 派生 **awesome-terraform** 存放庫。

    ![派生 GitHub awesome-terraform 存放庫](./media/terraform-slot-walkthru/fork-repo.png)

1. 依照任何提示來派生您的環境。

## <a name="deploy-from-github-to-your-deployment-slots"></a>從 GitHub 部署至您的部署位置

派生測試專案存放庫之後，請透過下列步驟來設定部署位置：

1. 在 Azure 入口網站的主功能表上，選取 [資源群組]  。

1. 選取 [slotDemoResourceGroup]  。

1. 選取 [slotAppService]  。

1. 選取 [部署選項]  。

    ![App Service 資源的部署選項](./media/terraform-slot-walkthru/deployment-options.png)

1. 在 [部署選項]  索引標籤上，選取 [選擇來源]  ，然後選取 [GitHub]  。

    ![選取部署來源](./media/terraform-slot-walkthru/select-source.png)

1. 在 Azure 進行連線並顯示所有選項之後，請選取 [授權]  。

1. 在 [授權]  索引標籤上，選取 [授權]  ，並提供 Azure 存取您 GitHub 帳戶所需的認證。 

1. Azure 在驗證您的 GitHub 認證之後，系統會顯示一則訊息，指出已完成授權程序。 選取 [確定]  以關閉 [授權]  索引標籤。

1. 選取 [選擇您的組織]  並選取您的組織。

1. 選取 [選擇專案]  。

1. 在 [選擇專案]  索引標籤上，選取 [awesome-terraform]  專案。

    ![選擇 awesome-terraform 專案](./media/terraform-slot-walkthru/choose-project.png)

1. 選取 [選擇分支]  。

1. 在 [選擇分支]  索引標籤上，選取 [主要]  。

    ![選擇主要分支](./media/terraform-slot-walkthru/choose-branch-master.png)

1. 在 [部署選項]  索引標籤上，選取 [確定]  。

此時，您已部署生產位置。 若要部署預備位置，請執行先前的步驟，但做下列修改：

- 在步驟 3 中，選取 [slotAppServiceSlotOne]  資源。

- 在步驟 13 中，選取工作分支，而不是主要分支。

    ![選擇工作分支](./media/terraform-slot-walkthru/choose-branch-working.png)

## <a name="test-the-app-deployments"></a>測試應用程式部署

在前面幾節中，您設定了兩個位置--**slotAppService** 和 **slotAppServiceSlotOne**--以從 GitHub 中不同的分支進行部署。 讓我們來預覽 Web 應用程式，以驗證這些應用程式是否已部署成功。

1. 在 Azure 入口網站的主功能表上，選取 [資源群組]  。

1. 選取 [slotDemoResourceGroup]  。

1. 選取 [slotAppService]  或 [slotAppServiceSlotOne]  。

1. 在概觀頁面上，選取 [URL]  。

    ![選取概觀索引標籤上的 URL 來呈現應用程式](./media/terraform-slot-walkthru/resource-url.png)

1. 根據選取的應用程式，您會看到下列結果：
    - **slotAppService** Web 應用程式 - 一個藍色頁面，其頁面標題為「位置示範應用程式 1」  。 
    - **slotAppServiceSlotOne** Web 應用程式 - 一個綠色頁面，其頁面標題為「位置示範應用程式 2」  。

    ![預覽應用程式以測試它們是否已正確部署](./media/terraform-slot-walkthru/app-preview.png)

## <a name="swap-the-two-deployment-slots"></a>交換兩個部署位置

若要測試兩個部署位置的交換，請執行下列步驟：
 
1. 切換至執行 **slotAppService** 的瀏覽器索引標籤 (具有藍色頁面的應用程式)。 

1. 在不同的索引標籤中返回 Azure 入口網站。

1. 開啟 Cloud Shell。

1. 切換至 **clouddrive/swap** 目錄。

    ```bash
    cd clouddrive/swap
    ```

1. 在 Cloud Shell 中建立名稱為 `swap.tf` 的檔案。

    ```bash
    code swap.tf
    ```

1. 將下列程式碼貼到編輯器中：

    ```hcl
    # Configure the Azure provider
    provider "azurerm" { 
        # The "feature" block is required for AzureRM provider 2.x. 
        # If you are using version 1.x, the "features" block is not allowed.
        version = "~>2.0"
        features {}
    }

    # Swap the production slot and the staging slot
    resource "azurerm_app_service_active_slot" "slotDemoActiveSlot" {
        resource_group_name   = "slotDemoResourceGroup"
        app_service_name      = "slotAppService"
        app_service_slot_name = "slotappServiceSlotOne"
    }
    ```

1. 儲存檔案 ( **&lt;Ctrl>S**) 並結束編輯器 ( **&lt;Ctrl>Q**)。

1. 初始化 Terraform。

    ```bash
    terraform init
    ```

1. 建立 Terraform 計劃。

    ```bash
    terraform plan
    ```

1. 佈建 `swap.tf` 組態檔中定義的資源。 (在提示字元輸入 `yes`，以確認此動作。)

    ```bash
    terraform apply
    ```

1. 在 Terraform 交換位置後，請返回瀏覽器。 重新整理頁面。 

**slotAppServiceSlotOne** 預備位置中的 Web 應用程式已與生產位置交換，現在呈現綠色。 

![部署位置已經交換](./media/terraform-slot-walkthru/slots-swapped.png)

若要回到原始生產版本的應用程式，請重新套用從 `swap.tf` 組態檔建立的 Terraform 計劃。

```bash
terraform apply
```

交換之後，您就會看到原始組態。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"] 
> [深入了解如何使用 Azure 中的 Terraform](/azure/terraform)
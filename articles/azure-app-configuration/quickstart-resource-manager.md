---
title: 使用 Azure 應用程式組態進行自動化 VM 部署快速入門
description: 本快速入門示範如何使用 Azure PowerShell 模組和 Azure Resource Manager 範本來部署 Azure 應用程式組態存放區。 然後使用存放區中的值來部署 VM。
author: lisaguthrie
ms.author: lcozzens
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: azure-app-configuration
ms.custom:
- mvc
- subject-armqs
ms.openlocfilehash: 9b609d4571d6240f428a0210aa5108ff19dc753b
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88235174"
---
# <a name="quickstart-automated-vm-deployment-with-app-configuration-and-resource-manager-template-arm-template"></a>快速入門：使用應用程式組態和 Resource Manager 範本 (ARM 範本) 自動部署 VM

了解如何使用 Azure Resource Manager 範本和 Azure PowerShell 部署 Azure 應用程式組態存放區、如何將機碼值新增至存放區，以及如何使用存放區中的機碼值部署 Azure 資源，例如此範例中的 Azure 虛擬機器。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果您的環境符合必要條件，而且您很熟悉 ARM 範本，請選取 [部署至 Azure] 按鈕。 範本會在 Azure 入口網站中開啟。

[![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store%2Fazuredeploy.json)

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-templates"></a>檢閱範本

本快速入門中使用的範本是來自 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/)。 [第一個範本](https://azure.microsoft.comresources/templates/101-app-configuration-store/)會建立應用程式組態存放區：

:::code language="json" source="~/quickstart-templates/101-app-configuration-store/azuredeploy.json" range="1-37" highlight="27-35":::

此範本中已定義一項 Azure 資源：

- [Microsoft.AppConfiguration/configurationStores](/azure/templates/microsoft.appconfiguration/2019-10-01/configurationstores)：建立應用程式組態存放區。

[第二個範本](https://azure.microsoft.com/resources/templates/101-app-configuration/)會使用存放區中的機碼值來建立虛擬機器。 在此步驟之前，您需要使用入口網站或 Azure CLI 來新增機碼值。

:::code language="json" source="~/quickstart-templates/101-app-configuration/azuredeploy.json" range="1-217" highlight="77, 181,189":::

## <a name="deploy-the-templates"></a>部署範本

### <a name="create-an-app-configuration-store"></a>建立應用程式組態存放區

1. 選取以下影像來登入 Azure 並開啟範本。 範本會建立應用程式組態存放區。

    [![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store%2Fazuredeploy.json)

1. 選取或輸入下列值。

    - **訂用帳戶**：選取用來建立應用程式組態存放區的 Azure 訂用帳戶。
    - **資源群組**：選取 [新建] 以建立新的資源群組，除非您想使用現有的資源群組。
    - **區域**：選取資源群組的位置。  例如，「美國東部」。
    - **組態存放區名稱**：輸入新的應用程式組態存放區名稱。
    - **位置**：指定應用程式組態存放區的位置。  使用預設值。
    - **SKU 名稱**：指定應用程式組態存放區的 SKU 名稱。 使用預設值。

1. 選取 [檢閱 + 建立]。
1. 確認該頁面顯示**已傳遞驗證**，然後選取 [建立]。

記下資源群組名稱和應用程式組態存放區名稱。  您在部署虛擬機器時需要這些值
### <a name="add-vm-configuration-key-values"></a>新增 VM 組態索引鍵/值

建立應用程式組態存放區後，您可以使用 Azure 入口網站或 Azure CLI，將機碼值新增至存放區。

1. 登入 [Azure 入口網站](https://portal.azure.com)，並瀏覽至新建立的應用程式組態存放區。
1. 從左側功能表中，選取 [組態總管]。
1. 選取 [建立] 來新增下列機碼值組：

   |Key|值|標籤|
   |-|-|-|
   |windowsOsVersion|2019-Datacenter|template|
   |diskSizeGB|1023|template|

   將**內容類型**留白。

要使用 Azure CLI，請參閱[在 Azure 應用程式設定存放區中搭配使用機碼值](./scripts/cli-work-with-keys.md)。

### <a name="deploy-vm-using-stored-key-values"></a>使用儲存的索引鍵/值部署 VM

您現已將索引鍵/值新增至存放區，就可以使用 Azure Resource Manager 範本來部署 VM。 此範本會參考您所建立的 **windowsOsVersion** 和 **diskSizeGB** 金鑰。

> [!WARNING]
> ARM 範本無法參考已啟用 Private Link 的應用程式組態存放區中的金鑰。

1. 選取以下影像來登入 Azure 並開啟範本。 此範本會使用應用程式組態存放區中儲存的機碼值來建立虛擬機器。

    [![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration%2Fazuredeploy.json)

1. 選取或輸入下列值。

    - **訂用帳戶**：選取用來建立虛擬機器的 Azure 訂用帳戶。
    - **資源群組**：指定與應用程式組態存放區相同的資源群組，或選取 [建立新的] 以建立新的資源群組。
    - **區域**：選取資源群組的位置。  例如，「美國東部」。
    - **位置**：指定虛擬機器位置。 使用預設值。
    - **管理員使用者名稱**：指定虛擬機器的管理員使用者名稱。
    - **管理員密碼**：指定虛擬機器的管理員密碼。
    - **網域名稱標籤**：指定唯一的網域名稱。
    - **儲存體帳戶名稱**：指定與虛擬機器相關聯之儲存體帳戶的唯一名稱。
    - **應用程式組態存放區資源群組**：指定包含您應用程式組態存放區的資源群組。
    - **應用程式組態存放區名稱**：指定 Azure 應用程式組態存放區的名稱。
    - **VM SKU 金鑰**：指定  **windowsOsVersion**。  這是您新增至存放區的金鑰值名稱。
    - **磁碟大小金鑰**：指定 **diskSizeGB**。 這是您新增至存放區的金鑰值名稱。

1. 選取 [檢閱 + 建立]。
1. 確認該頁面顯示**已傳遞驗證**，然後選取 [建立]。

## <a name="review-deployed-resources"></a>檢閱已部署的資源

1. 登入 [Azure 入口網站](https://portal.azure.com)，並瀏覽至新建立的虛擬機器。
1. 從左側功能表中選取 [概觀]，並確認 **SKU** 為 **2019-Datacenter**。
1. 從左側功能表中選取 [磁碟]，然後確認資料磁碟的大小為 **2013**。

## <a name="clean-up-resources"></a>清除資源

若不再需要，可刪除資源群組、應用程式組態存放區、VM 和所有相關資源。 如果您打算在未來使用應用程式組態存放區或 VM，則可跳過刪除動作。 如果您不要繼續使用此作業，請執行下列 Cmdlet，以刪除本快速入門所建立的所有資源：

```azurepowershell-interactive
Remove-AzResourceGroup `
  -Name $resourceGroup
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已使用 Azure 應用程式組態中的 Azure Resource Manager 範本和索引鍵/值來部署 VM。

若要了解如何使用 Azure 應用程式組態來建立其他應用程式，請繼續閱讀以下文章：

> [!div class="nextstepaction"]
> [快速入門：使用 Azure 應用程式組態建立 ASP.NET Core 應用程式](quickstart-aspnet-core-app.md)

---
title: 快速入門：使用範本建立共用查詢
description: 在本快速入門中，您可以使用 Azure Resource Manager 範本 (ARM 範本) 來建立 Resource Graph 共用查詢，以便依 OS 來計算虛擬機器的數量。
ms.date: 07/06/2020
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: a05be04064df81373f856ea0e8ca59664078695f
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86252363"
---
# <a name="quickstart-create-a-shared-query-by-using-an-arm-template"></a>快速入門：使用 ARM 範本建立共用查詢

Resource Graph 查詢可以儲存為_私人查詢_或_共用查詢_。 私人查詢會儲存到個人的入口網站設定檔，其他人看不到。 共用查詢則是 Resource Manager 物件，可透過權限和角色型存取來與其他人共用。 共用查詢可讓您以通用且一致的方式執行資源探索。 本快速入門會使用 Azure Resource Manager 範本 (ARM 範本) 來建立共用查詢。

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

如果您的環境符合必要條件，而且您很熟悉 ARM 範本，請選取 [部署至 Azure] 按鈕。 範本會在 Azure 入口網站中開啟。

:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="部署 ARM 範本來建立 Azure 的共用查詢" border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fresourcegraph-sharedquery-countos%2Fazuredeploy.json":::

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

## <a name="review-the-template"></a>檢閱範本

在本快速入門中，您會建立名為_依 OS 計算 VM 數量_的共用查詢。 若要在 SDK 中試用此查詢，或是在入口網站中使用 Resource Graph 總管來試用此查詢，請參閱[範例 - 依 OS 類型計算虛擬機器數量](./samples/starter.md#count-os)。

本快速入門中使用的範本是來自 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/resourcegraph-sharedquery-countos/)。

:::code language="json" source="~/quickstart-templates/resourcegraph-sharedquery-countos/azuredeploy.json" highlight="28-37":::

範本中定義的資源為：

- [Microsoft.ResourceGraph/queries](/azure/templates/microsoft.resourcegraph/queries)

## <a name="deploy-the-template"></a>部署範本

> [!NOTE]
> Azure Resource Graph 服務是免費的。 如需詳細資訊，請參閱 [Azure Resource Graph 概觀](./overview.md)。

1. 選取以下影像來登入 Azure 入口網站並開啟範本：

   :::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="部署 ARM 範本來建立 Azure 的共用查詢" border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fresourcegraph-sharedquery-countos%2Fazuredeploy.json":::

1. 選取或輸入下列值：

   | 名稱 | 值 |
   |------|-------|
   | 訂用帳戶 | 選取 Azure 訂用帳戶。 |
   | 資源群組 | 選取 [新建]指定名稱，然後選取 [確定]。 |
   | Location | 選取區域。 例如，**美國中部**。 |
   | 查詢名稱 | 保留預設值**依 OS 計算 VM 數量**。 |
   | 查詢程式碼 | 保留預設值 `Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)` |
   | 查詢說明 | 保留預設值**此共用查詢會計算所有虛擬機器資源的數量並依 OS 類型摘要列出。** |
   | 我同意上方所述的條款及條件 | (選取) |

1. 選取 [購買]。

一些其他資源：

- 若要尋找更多範例範本，請參閱 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular)。
- 若要查看範本參考，請前往 [Azure 範本參考](/azure/templates/microsoft.resourcegraph/allversions)。
- 若要了解如何開發 ARM 範本，請參閱 [Azure Resource Manager 文件](../../azure-resource-manager/management/overview.md)。
- 若要了解訂用帳戶層級的部署，請參閱[在訂用帳戶層級建立資源群組和資源](../../azure-resource-manager/templates/deploy-to-subscription.md)。

## <a name="validate-the-deployment"></a>驗證部署

若要執行新的共用查詢，請遵循下列步驟：

1. 從入口網站的搜尋列中，搜尋 **Resource Graph 查詢**並加以選取。

1. 選取名為**依 OS 計算 VM 數量**的共用查詢，然後選取 [概觀] 頁面上的 [結果] 索引標籤。

或者，您也可以從 Resource Graph 總管開啟共用查詢：

1. 從入口網站的搜尋列中，搜尋 **Resource Graph 總管**並加以選取。

1. 選取 [開啟查詢] 按鈕。

1. 將 [類型] 變更為 [共用查詢]。 如果您在清單中沒看到**依 OS 計算 VM 數量**，請使用篩選方塊來限制結果。 一旦看到**依 OS 計算 VM 數量**共用查詢，請選取其名稱。

1. 查詢載入之後，請選取 [執行查詢] 按鈕。 結果會顯示在下面的 [結果] 索引標籤中。

## <a name="clean-up-resources"></a>清除資源

若要移除已建立的共用查詢，請遵循下列步驟：

1. 從入口網站的搜尋列中，搜尋 **Resource Graph 查詢**並加以選取。

1. 在名為**依 OS 計算 VM 數量**的共用查詢旁設定核取方塊。

1. 選取頁面頂端的 [刪除] 按鈕。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立 Resource Graph 共用查詢。

若要深入了解共用查詢，請繼續進行下列教學課程：

> [!div class="nextstepaction"]
> [在 Azure 入口網站中管理查詢](./tutorials/create-share-query.md)
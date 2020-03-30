---
title: 重寫門戶中的 HTTP 要求和回應標頭 - Azure 應用程式閘道
description: 瞭解如何使用 Azure 門戶配置 Azure 應用程式閘道以重寫通過閘道的請求和回應中的 HTTP 標頭
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: b90736b3ed1c1f69488fde4a386cf215d751c362
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74012848"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-portal"></a>使用 Azure 應用程式閘道重寫 HTTP 要求和回應標頭 - Azure 門戶

本文介紹如何使用 Azure 門戶配置[應用程式閘道 v2 SKU](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>)實例來重寫請求和回應中的 HTTP 標頭。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="before-you-begin"></a>開始之前

您需要有一個應用程式閘道 v2 SKU 實例才能完成本文中的步驟。 v1 SKU 不支援重寫標頭。 如果沒有 v2 SKU，請先創建[應用程式閘道 v2 SKU](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps)實例。

## <a name="create-required-objects"></a>創建所需物件

要配置 HTTP 標頭重寫，您需要完成這些步驟。

1. 創建 HTTP 標頭重寫所需的物件：

   - **重寫操作**：用於指定要重寫的請求和請求標頭欄位以及標頭的新值。 您可以將一個或多個重寫條件與重寫操作相關聯。

   - **重寫條件**：可選配置。 重寫條件評估 HTTP（S） 請求和回應的內容。 如果 HTTP（S） 請求或回應與重寫條件匹配，則將執行重寫操作。

     如果將多個條件與操作相關聯，則僅當滿足所有條件時才會執行該操作。 換句話說，該操作是一個邏輯的 AND 操作。

   - **重寫規則**：包含多個重寫操作/重寫條件組合。

   - **規則序列**：説明確定重寫規則的執行順序。 當您重寫集中有多個重寫規則時，此配置非常有用。 具有較低規則序列值的重寫規則首先運行。 如果將相同的規則序列值分配給兩個重寫規則，則執行順序是非確定性的。

   - **重寫集**：包含與請求路由規則關聯的多個重寫規則。

2. 將重寫集附加到路由規則。 重寫配置通過路由規則附加到源攔截器。 使用基本路由規則時，標頭重寫配置與源攔截器關聯，並且是全域標頭重寫。 使用基於路徑的路由規則時，將在 URL 路徑映射上定義標頭重寫配置。 在這種情況下，它僅適用于網站的特定路徑區域。

您可以創建多個 HTTP 標頭重寫集，並將每個重寫集應用於多個攔截器。 但是，您只能將一個重寫集應用於特定攔截器。

## <a name="sign-in-to-azure"></a>登入 Azure

使用您的 Azure 帳戶登入 [Azure 入口網站](https://portal.azure.com/) 。

## <a name="configure-header-rewrite"></a>配置標頭重寫

在此示例中，我們將通過重寫後端應用程式發送的 HTTP 回應中的位置標頭來修改重定向 URL。

1. 選擇**所有資源**，然後選擇應用程式閘道。

2. 在左側窗格中選擇 **"重寫**"。

3. 選擇**重寫集**：

   ![添加重寫集](media/rewrite-http-headers-portal/add-rewrite-set.png)

4. 提供重寫集的名稱並將其與路由規則相關聯：

   - 在 **"名稱"** 框中輸入重寫集的名稱。
   - 選擇 **"關聯路由規則**"清單中列出的一個或多個規則。 只能選擇未與其他重寫集關聯的規則。 已與其他重寫集關聯的規則將變暗。
   - 選取 [下一步]****。
   
     ![添加名稱和關聯](media/rewrite-http-headers-portal/name-and-association.png)

5. 創建重寫規則：

   - 選擇 **"添加重寫規則**"。

     ![添加重寫規則](media/rewrite-http-headers-portal/add-rewrite-rule.png)

   - 在 **"重寫規則名稱**"框中輸入重寫規則的名稱。 在 **"規則"序列**框中輸入數位。

     ![添加重寫規則名稱](media/rewrite-http-headers-portal/rule-name.png)

6. 在此示例中，我們僅在位置標頭包含對azurewebsites.net的引用時重寫它。 為此，請添加一個條件以評估回應中的位置標頭是否包含azurewebsites.net：

   - 選擇 **"添加條件**"，然後選擇包含 **"如果**"指令的框展開。

     ![新增條件](media/rewrite-http-headers-portal/add-condition.png)

   - 在**要檢查清單的變數類型中**，選擇**HTTP 標頭**。

   - 在**標題類型**清單中，選擇 **"回應**"。

   - 因為在此示例中，我們正在評估位置標頭（這是一個公共標頭），請在**標頭名稱**下選擇 **"公共標頭**"。

   - 在 **"常見標頭**"清單中，選擇 **"位置**"。

   - 在**區分大小寫**下，選擇 **"否**"。

   - 在 **"運算子"** 清單中，選擇**相等 （*）**。

   - 輸入正則運算式模式。 在此示例中，我們將使用 模式`(https?):\/\/.*azurewebsites\.net(.*)$`。

   - 選取 [確定]****。

     ![配置 If 條件](media/rewrite-http-headers-portal/condition.png)

7. 添加操作以重寫位置標頭：

   - 在 **"操作類型"** 清單中，選擇 **"設置**"。

   - 在**標題類型**清單中，選擇 **"回應**"。

   - 在**標題名稱**下，選擇 **"公共標頭**"。

   - 在 **"常見標頭**"清單中，選擇 **"位置**"。

   - 輸入標題值。 在此示例中，我們將用作`{http_resp_Location_1}://contoso.com{http_resp_Location_2}`標頭值。 此值將在位置標頭中用*contoso.com*替換*azurewebsites.net。*

   - 選取 [確定]****。

     ![新增動作](media/rewrite-http-headers-portal/action.png)

8. 選擇 **"創建**"以創建重寫集：

   ![選取 [建立]](media/rewrite-http-headers-portal/create.png)

9. 重寫集視圖將打開。 驗證您創建的重寫集是否位於重寫集清單中：

   ![重寫集視圖](media/rewrite-http-headers-portal/rewrite-set-list.png)

## <a name="next-steps"></a>後續步驟

要瞭解有關如何設置一些常見用例的更多，請參閱[常見標頭重寫方案](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)。
---
title: 在入口網站中重寫 HTTP 要求和回應標頭-Azure 應用程式閘道
description: 瞭解如何使用 Azure 入口網站設定 Azure 應用程式閘道，以重寫透過閘道傳遞之要求和回應中的 HTTP 標頭
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: b90736b3ed1c1f69488fde4a386cf215d751c362
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74012848"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-portal"></a>使用 Azure 應用程式閘道 Azure 入口網站重寫 HTTP 要求和回應標頭

本文說明如何使用 Azure 入口網站來設定[應用程式閘道 V2 SKU](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>)實例，以重寫要求和回應中的 HTTP 標頭。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="before-you-begin"></a>開始之前

您需要有應用程式閘道 v2 SKU 實例，才能完成本文中的步驟。 V1 SKU 不支援重寫標頭。 如果您沒有 v2 SKU，請在開始之前建立[應用程式閘道 V2 sku](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps)實例。

## <a name="create-required-objects"></a>建立必要的物件

若要設定 HTTP 標頭重寫，您需要完成這些步驟。

1. 建立 HTTP 標頭重寫所需的物件：

   - **重寫動作**：用來指定您想要重寫的要求和要求標頭欄位，以及標頭的新值。 您可以將一或多個重寫條件與重寫動作產生關聯。

   - **重寫條件**：選擇性設定。 重寫條件會評估 HTTP （S）要求和回應的內容。 如果 HTTP （S）要求或回應符合重寫條件，則會發生重寫動作。

     如果您將一個以上的條件與一個動作建立關聯，只有在符合所有條件時，才會發生此動作。 換句話說，作業是邏輯 AND 運算。

   - **重寫規則**：包含多個重寫動作/重寫條件組合。

   - **規則順序**：有助於判斷重寫規則的執行順序。 當重寫集內有多個重寫規則時，此設定會很有説明。 規則順序值較低的重寫規則會先執行。 如果您將相同的規則順序值指派給兩個重寫規則，執行的順序會不具決定性。

   - **重寫集**：包含多個將與要求路由規則相關聯的重寫規則。

2. 將重寫集附加至路由規則。 重寫設定會透過路由規則附加至來源接聽程式。 當您使用基本路由規則時，標頭重寫設定會與來源接聽程式相關聯，而且是全域標頭重寫。 當您使用以路徑為基礎的路由規則時，會在 URL 路徑對應上定義標頭重寫設定。 在這種情況下，它只適用于網站的特定路徑區域。

您可以建立多個 HTTP 標頭重寫集，並將每個重寫集套用到多個接聽程式。 但是，您只能將一個重寫集套用到特定的接聽程式。

## <a name="sign-in-to-azure"></a>登入 Azure

使用您的 Azure 帳戶登入 [Azure 入口網站](https://portal.azure.com/) 。

## <a name="configure-header-rewrite"></a>設定標頭重寫

在此範例中，我們將在後端應用程式所傳送的 HTTP 回應中重寫 location 標頭，以修改重新導向 URL。

1. 選取 [**所有資源**]，然後選取您的應用程式閘道。

2. 在左窗格中選取 [重**寫**]。

3. 選取**重寫集**：

   ![新增重寫集](media/rewrite-http-headers-portal/add-rewrite-set.png)

4. 提供重寫集的名稱，並將它與路由規則建立關聯：

   - 在 [**名稱**] 方塊中，輸入重寫集的名稱。
   - 選取 [**相關聯的路由規則**] 清單中所列的一或多個規則。 您只能選取尚未與其他重寫集相關聯的規則。 已與其他重寫集相關聯的規則會呈現暗灰色。
   - 選取 [下一步]  。
   
     ![新增名稱和關聯](media/rewrite-http-headers-portal/name-and-association.png)

5. 建立重寫規則：

   - 選取 [**新增重寫規則**]。

     ![新增重寫規則](media/rewrite-http-headers-portal/add-rewrite-rule.png)

   - 在 [**重寫規則名稱**] 方塊中，輸入重寫規則的名稱。 在 [**規則順序**] 方塊中輸入數位。

     ![新增重寫規則名稱](media/rewrite-http-headers-portal/rule-name.png)

6. 在此範例中，只有在包含 azurewebsites.net 的參考時，才會重寫 location 標頭。 若要這麼做，請新增條件，以評估回應中的位置標頭是否包含 azurewebsites.net：

   - 選取 [**新增條件**]，然後選取包含 [ **If** ] 指示展開它的方塊。

     ![新增條件](media/rewrite-http-headers-portal/add-condition.png)

   - 在 [**要檢查的變數類型**] 清單中，選取 [ **HTTP 標頭**]。

   - 在 [**標頭類型**] 清單中，選取 [**回應**]。

   - 因為在此範例中，我們要評估位置標頭，也就是通用標頭，請選取 [**標頭名稱**] 底下的 [**通用標頭**]。

   - 在 [**通用標題**] 清單中，選取 [**位置**]。

   - 在 [**區分大小寫**] 底下，選取 [**否**]。

   - 在 [**運算子**] 清單中，選取 [**等於（=）**]。

   - 輸入正則運算式模式。 在此範例中，我們將使用模式`(https?):\/\/.*azurewebsites\.net(.*)$`。

   - 選取 [確定]  。

     ![設定 If 條件](media/rewrite-http-headers-portal/condition.png)

7. 新增動作以重寫 location 標頭：

   - 在 [**動作類型**] 清單中，選取 [**設定**]。

   - 在 [**標頭類型**] 清單中，選取 [**回應**]。

   - 在 [**標頭名稱**] 底下，選取 [**通用標頭**]。

   - 在 [**通用標題**] 清單中，選取 [**位置**]。

   - 輸入標頭值。 在此範例中，我們將`{http_resp_Location_1}://contoso.com{http_resp_Location_2}`使用做為標頭值。 此值會以 location 標頭中的*contoso.com*取代*azurewebsites.net* 。

   - 選取 [確定]  。

     ![新增動作](media/rewrite-http-headers-portal/action.png)

8. 選取 [**建立**] 以建立重寫集：

   ![選取 [建立]](media/rewrite-http-headers-portal/create.png)

9. [重寫集] 視圖隨即開啟。 確認您建立的重寫集位於重寫集清單中：

   ![重寫集視圖](media/rewrite-http-headers-portal/rewrite-set-list.png)

## <a name="next-steps"></a>後續步驟

若要深入瞭解如何設定一些常見的使用案例，請參閱[常見的標頭重寫案例](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)。
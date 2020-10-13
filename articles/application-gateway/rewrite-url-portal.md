---
title: 使用 Azure 應用程式閘道重寫 URL 和查詢字串-Azure 入口網站
description: 瞭解如何使用 Azure 入口網站設定 Azure 應用程式閘道來重寫 URL 和查詢字串
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: how-to
ms.date: 7/16/2020
ms.author: surmb
ms.openlocfilehash: 160d056447bd53ea01437acd372b5efeb15b4773
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87083152"
---
# <a name="rewrite-url-with-azure-application-gateway---azure-portal-preview"></a>使用 Azure 應用程式閘道重寫 URL-Azure 入口網站 (Preview) 

本文說明如何使用 Azure 入口網站來設定 [應用程式閘道 V2 SKU](application-gateway-autoscaling-zone-redundant.md) 實例來重寫 URL。

>[!NOTE]
> URL 重寫功能目前為預覽狀態，僅適用于應用程式閘道的 Standard_v2 和 WAF_v2 SKU。 不建議在實際執行環境中使用。 若要深入瞭解預覽，請參閱 [這裡的使用](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)規定。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="before-you-begin"></a>開始之前

您需要有應用程式閘道 v2 SKU 實例，才能完成本文中的步驟。 V1 SKU 不支援重寫 URL。 如果您沒有 v2 SKU，請在開始之前先建立 [應用程式閘道 V2 sku](tutorial-autoscale-ps.md) 實例。

## <a name="sign-in-to-azure"></a>登入 Azure

使用您的 Azure 帳戶登入 [Azure 入口網站](https://portal.azure.com/) 。

## <a name="configure-url-rewrite"></a>設定 URL 重寫

在下列範例中，每當要求 URL 包含 */article*時，就會重寫 url 路徑和 url 查詢字串

`contoso.com/article/123/fabrikam` -> `contoso.com/article.aspx?id=123&title=fabrikam`

1. 選取 [ **所有資源**]，然後選取您的應用程式閘道。

2. 選取左窗格中的 [重 **寫** ]。

3. 選取 **重寫集**：

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-1.png" alt-text="新增重寫集":::

4. 提供重寫集的名稱，並將它與路由規則建立關聯：

    a. 在 [ **名稱** ] 方塊中輸入重寫集的名稱。
    
    b. 選取 [ **關聯的路由規則** ] 清單中所列的一或多個規則。 這是用來透過路由規則，將重寫設定關聯至來源接聽程式。 您只能選取尚未與其他重寫集建立關聯的路由規則。 已與其他重寫集建立關聯的規則會呈現灰色。
    
    c. 選取 [下一步]  。
    
    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-2.png" alt-text="新增重寫集":::

5. 建立重寫規則：

    a. 選取 [ **新增重寫規則**]。
    
    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-3.png" alt-text="新增重寫集":::
    
    b. 在 [ **重寫規則名稱** ] 方塊中輸入重寫規則的名稱。 在 [ **規則順序** ] 方塊中輸入數位。

6. 在此範例中，只有當 path 包含 */article*時，才會重寫 url 路徑和 url 查詢字串。 若要這樣做，請新增條件來評估 URL 路徑是否包含 */article*

    a. 選取 [ **新增條件** ]，然後選取包含要展開之 **If** 指示的方塊。
    
    b. 因為在此範例中，我們想要檢查 URL 路徑中的模式 */article* ，請在 [ **要檢查的變數類型** ] 清單中選取 [ **伺服器變數**]。
    
    c. 在 [ **伺服器變數** ] 清單中，選取 uri_path
    
    d. 在區分 **大小寫的情況**下，選取 [ **否**]。
    
    e. 在 [ **運算子** ] 清單中選取 [ **等於] (=) **。
    
    f. 輸入正則運算式模式。 在此範例中，我們將使用模式 `.*article/(.*)/(.*)`
    
       ( ) 用來捕捉子字串，以供稍後用來撰寫用來重寫 URL 路徑的運算式。 如需詳細資訊，請參閱[這裡](rewrite-http-headers-url.md#capturing)。

    g. 選取 [確定]。

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-4.png" alt-text="新增重寫集":::

 

7. 新增動作以重寫 URL 和 URL 路徑

   a. 在 [ **重寫類型** ] 清單中選取 [ **URL**]。

   b. 在 [ **動作類型** ] 清單中選取 [ **設定**]。

   c. 在 [ **元件**] 底下，選取 **URL 路徑和 url 查詢字串**

   d. 在 [ **URL 路徑] 值**中，輸入路徑的新值。 在此範例中，我們將使用 **/article.aspx** 

   e. 在 [ **url 查詢字串] 值**中，輸入 url 查詢字串的新值。 在此範例中，我們將使用 **id = {var_uri_path_1} &title = {var_uri_path_2}**
    
    `{var_uri_path_1}` 和 `{var_uri_path_1}` 可用來提取評估這個運算式中的條件時所捕捉的子字串。 `.*article/(.*)/(.*)`
    
   f. 選取 [確定]  。

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-5.png" alt-text="新增重寫集":::

8. 按一下 [ **建立** ] 以建立重寫集。

9. 確認新的重寫集出現在重寫集清單中

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-6.png" alt-text="新增重寫集":::

## <a name="verify-url-rewrite-through-access-logs"></a>透過存取記錄驗證 URL 重寫

觀察存取記錄中的下欄欄位，以確認是否根據您的預期來重寫 URL。

* **originalRequestUriWithArgs**：此欄位包含原始要求 URL
* **requestUri**：此欄位包含在應用程式閘道上重寫操作後的 URL

如需存取記錄中所有欄位的詳細資訊，請參閱 [這裡](application-gateway-diagnostics.md#for-application-gateway-and-waf-v2-sku)。

##  <a name="next-steps"></a>接下來的步驟

若要深入瞭解如何針對一些常見的使用案例設定重寫，請參閱 [常見的重寫案例](rewrite-http-headers.md)。

---
title: 教學課程：建立 Azure CDN 的 WAF 原則 - Azure 入口網站
description: 在本教學課程中，您將了解如何使用 Azure 入口網站建立 Azure CDN 的 Web 應用程式防火牆 (WAF) 原則。
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 09/16/2020
ms.author: victorh
ms.openlocfilehash: c5505b9437a4bd8dced6a090817b17d5e29374f2
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91327933"
---
# <a name="tutorial-create-a-waf-policy-on-azure-cdn-using-the-azure-portal"></a>教學課程：使用 Azure 入口網站建立 Azure CDN 的 WAF 原則

本教學課程說明如何建立基本的 Azure Web 應用程式防火牆 (WAF) 原則，並將其套用至 Azure 內容傳遞網路 (CDN) 上的端點。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立 WAF 原則
> * 將其與 CDN 端點產生關聯。 您只能將 WAF 原則與 **Microsoft SKU 的 Azure CDN 標準**上所裝載的端點建立關聯。
> * 設定 WAF 規則

## <a name="prerequisites"></a>Prerequisites

依照下列快速入門中的指示，建立 Azure CDN 設定檔和端點：[快速入門：建立 Azure CDN 設定檔和端點](../../cdn/cdn-create-new-endpoint.md)。 

## <a name="create-a-web-application-firewall-policy"></a>建立 Web 應用程式防火牆原則

首先，使用入口網站來建立具有受控預設規則集 (DRS) 的基本 WAF 原則。

1. 在畫面的左上方，選取 [建立資源]  > 搜尋 **WAF** > 選取 [Web 應用程式防火牆]  > 選取 [建立]  。
2. 在 [建立 WAF 原則]  頁面的 [基本資料]  索引標籤中，輸入或選取下列資訊、接受其餘設定的預設值，然後選取 [檢閱 + 建立]  ：

    | 設定                 | 值                                              |
    | ---                     | ---                                                |
    | 原則適用對象            |選取 Azure CDN (預覽)。|
    | 訂用帳戶            |選取您的 Front Door 訂用帳戶名稱。|
    | 資源群組          |選取您的 Front Door 資源群組名稱。|
    | 原則名稱             |輸入 WAF 原則的唯一名稱。|

   ![建立 WAF 原則](../media/waf-cdn-create-portal/basic.png)

3. 在 [建立 WAF 原則]  頁面的 [關聯]  索引標籤中，選取 [新增 CDN 端點]  ，輸入下列設定，然後選取 [新增]  ：

    | 設定                 | 值                                              |
    | ---                     | ---                                                |
    | CDN 設定檔              | 選取您的 CDN 設定檔名稱。|
    | 端點           | 選取您的端點名稱，然後選取 [新增]  。|
    
    > [!NOTE]
    > 如果端點與 WAF 原則相關聯，則會顯示為灰色。您必須先從相關聯的原則中移除端點，然後再將端點重新與新的 WAF 原則產生關聯。
1. 選取 [檢閱 + 建立]  ，然後選取 [建立]  。

## <a name="configure-web-application-firewall-policy-optional"></a>設定 Web 應用程式防火牆原則 (選擇性)

### <a name="change-mode"></a>變更模式

當您建立 WAF 原則時，WAF 原則依預設會處於 [偵測]  模式中。 在 [偵測]  模式中，WAF 不會封鎖任何要求。 此時會將符合 WAF 規則的要求記錄在 WAF 記錄中。

若要查看 WAF 的實際效果，您可以將模式設定從 [偵測]  變更為 [預防]  。 在 [預防]  模式中，會封鎖與預設規則集 (DRS) 中定義之規則相符的要求，並記錄在 WAF 記錄檔中。

 ![變更 WAF 原則模式](../media/waf-cdn-create-portal/policy.png)

### <a name="custom-rules"></a>自訂規則

若要建立自訂規則，請選取 [自訂規則]  區段底下的 [新增自訂規則]  。 此時會開啟 [自訂規則設定] 頁面。 自訂規則分成兩種類型：**比對規則**和**速率限制**規則。

下列螢幕擷取畫面顯示若查詢字串包含 **blockme** 值即封鎖要求的自訂比對規則。

![新增自訂比對規則](../media/waf-cdn-create-portal/custommatch.png)

速率限制規則需要兩個額外的欄位：[速率限制持續時間]  和 [速率限制閾值 (要求數)]  ，如下列範例所示：

![新增速率限制規則](../media/waf-cdn-create-portal/customrate.png)

### <a name="default-rule-set-drs"></a>預設規則集 (DRS)

依預設會啟用 Azure 受控預設規則集。 若要停用規則群組內的個別規則，請展開該規則群組中的規則、選取規則編號前面的核取方塊，然後選取上方索引標籤上的 [停用]  。 若要變更規則集內個別規則的動作類型，請選取規則編號前面的核取方塊，然後選取上方的 [變更動作]  索引標籤。

 ![變更 WAF 規則集](../media/waf-cdn-create-portal/managed2.png)

## <a name="clean-up-resources"></a>清除資源

當不再需要資源時，請移除資源群組及所有相關資源。


## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解 Azure Web 應用程式防火牆](../overview.md)

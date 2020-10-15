---
title: 教學課程：建立 Azure Front Door 的 WAF 原則 - Azure 入口網站
description: 在此教學課程中，您將了解如何使用 Azure 入口網站建立 Web 應用程式防火牆 (WAF) 原則。
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: victorh
ms.openlocfilehash: 7c7ea5297276ed9a1d1f2ca8f4190997dcab57c3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90602212"
---
# <a name="tutorial-create-a-web-application-firewall-policy-on-azure-front-door-using-the-azure-portal"></a>教學課程：使用 Azure 入口網站在 Azure Front Door 上建立 Web 應用程式防火牆原則

此教學課程說明如何建立基本的 Azure Web 應用程式防火牆 (WAF) 原則，並將其套用至 Azure Front Door 的前端主機。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立 WAF 原則
> * 將它與前端主機產生關聯
> * 設定 WAF 規則

## <a name="prerequisites"></a>Prerequisites

請遵循[快速入門：建立 Front Door 設定檔](../../frontdoor/quickstart-create-front-door.md)中所述的指示來建立 Front Door 設定檔。 

## <a name="create-a-web-application-firewall-policy"></a>建立 Web 應用程式防火牆原則

首先，使用入口網站來建立具有受控預設規則集 (DRS) 的基本 WAF 原則。 

1. 在畫面的左上方，選取 [建立資源]  > 搜尋 **WAF** > 選取 [Web 應用程式防火牆 (預覽)]  > 選取 [建立]  。
2. 在 [建立 WAF 原則]  頁面的 [基本資料]  索引標籤中，輸入或選取下列資訊、接受其餘設定的預設值，然後選取 [檢閱 + 建立]  ：

    | 設定                 | 值                                              |
    | ---                     | ---                                                |
    | 訂用帳戶            |選取您的 Front Door 訂用帳戶名稱。|
    | 資源群組          |選取您的 Front Door 資源群組名稱。|
    | 原則名稱             |輸入 WAF 原則的唯一名稱。|

   ![建立 WAF 原則](../media/waf-front-door-create-portal/basic.png)

3. 在 [建立 WAF 原則]  頁面的 [關聯]  索引標籤中，選取 [新增前端主機]  ，輸入下列設定，然後選取 [新增]  ：

    | 設定                 | 值                                              |
    | ---                     | ---                                                |
    | Front Door              | 選取您的 Front Door 設定檔名稱。|
    | 前端主機           | 選取您的 Front Door 主機名稱，然後選取 [新增]  。|
    
    > [!NOTE]
    > 如果前端主機與 WAF 原則相關聯，它會顯示為灰色。您必須先從相關聯的原則中移除前端主機，然後再將前端主機重新關聯至新的 WAF 原則。
1. 選取 [檢閱 + 建立]  ，然後選取 [建立]  。

## <a name="configure-web-application-firewall-rules-optional"></a>設定 Web 應用程式防火牆規則 (選擇性)

### <a name="change-mode"></a>變更模式

當您建立 WAF 原則時，預設的 WAF 原則會處於 [偵測]  模式。 在 [偵測]  模式中，WAF 不會封鎖任何要求，而是會將符合 WAF 規則的要求記錄在 WAF 記錄檔中。
若要查看 WAF 的實際效果，您可以將模式設定從 [偵測]  變更為 [預防]  。 在 [預防]  模式中，會封鎖與預設規則集 (DRS) 中定義之規則相符的要求，並記錄在 WAF 記錄檔中。

 ![變更 WAF 原則模式](../media/waf-front-door-create-portal/policy.png)

### <a name="custom-rules"></a>自訂規則

您可以選取 [自訂規則]  區段下的 [新增自訂規則]  來建立自訂規則。 這會啟動 [自訂規則設定] 頁面。 以下範例說明如何設定自訂規則，以在查詢字串包含 **blockme** 時封鎖要求。

![自訂規則](../media/waf-front-door-create-portal/customquerystring2.png)

### <a name="default-rule-set-drs"></a>預設規則集 (DRS)

預設會啟用 Azure 管理的預設規則集。 若要停用規則群組內的個別規則，請展開該規則群組中的規則、選取規則編號前面的**核取方塊**，然後選取上方索引標籤上的 [停用]  。 若要變更規則集內個別規則的動作類型，請選取規則編號前面的核取方塊，然後選取上方的 [變更動作]  索引標籤。

 ![變更 WAF 規則集](../media/waf-front-door-create-portal/managed2.png)

## <a name="clean-up-resources"></a>清除資源

當不再需要資源時，請移除資源群組及所有相關資源。

## <a name="next-steps"></a>下一步

> [!div class="nextstepaction"]
> [深入了解 Azure Front Door](../../frontdoor/front-door-overview.md)
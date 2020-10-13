---
title: 使用 Azure Sentinel 搭配 Azure Web 應用程式防火牆
description: '本文說明如何使用 Azure Sentinel 搭配 Azure Web 應用程式防火牆 (WAF) '
services: web-application-firewall
author: TreMansdoerfer
ms.service: web-application-firewall
ms.date: 10/12/2020
ms.author: victorh
ms.topic: how-to
ms.openlocfilehash: 525ec334e73ca010d319b40ab864d08dae32f493
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91997228"
---
# <a name="using-azure-sentinel-with-azure-web-application-firewall"></a>使用 Azure Sentinel 搭配 Azure Web 應用程式防火牆

Azure Web 應用程式防火牆 (WAF) 結合 Azure Sentinel 可以提供 WAF 資源的安全性資訊事件管理。 Azure Sentinel 使用 Log Analytics 提供安全性分析，可讓您輕鬆地細分和查看您的 WAF 資料。 您可以使用 Sentinel 來存取預先建立的活頁簿，並加以修改，以最符合您組織的需求。 活頁簿可以顯示 Azure 內容傳遞網路上的 WAF 分析 (CDN) 、Azure Front Door 上的 WAF，以及跨多個訂用帳戶和工作區的應用程式閘道 WAF。

## <a name="waf-log-analytics-categories"></a>WAF 記錄分析類別

WAF log analytics 分為下列類別：  

- 所有採取的 WAF 動作 
- 美國排行榜封鎖的要求 URI 位址 
- 前50事件觸發程式，  
- 經過一段時間的訊息 
- 完整訊息詳細資料 
- 依訊息的攻擊事件  
- 經過一段時間的攻擊事件 
- 追蹤識別碼篩選 
- 追蹤識別碼訊息 
- 前10大攻擊 IP 位址 
- IP 位址的攻擊訊息 

## <a name="waf-workbook-examples"></a>WAF 活頁簿範例

下列 WAF 活頁簿範例會顯示範例資料：

:::image type="content" source="media//waf-sentinel/waf-actions-filter.png" alt-text="WAF 動作篩選":::

:::image type="content" source="media//waf-sentinel/top-50-event-trigger.png" alt-text="WAF 動作篩選":::

:::image type="content" source="media//waf-sentinel/attack-events.png" alt-text="WAF 動作篩選":::

:::image type="content" source="media//waf-sentinel/top-10-attacking-ip-address.png" alt-text="WAF 動作篩選":::

## <a name="launch-a-waf-workbook"></a>啟動 WAF 活頁簿

WAF 活頁簿適用于所有 Azure Front Door、應用程式閘道和 CDN Waf。 從這些資源連線資料之前，您必須在您的資源上啟用 log analytics。 

若要為每個資源啟用 log analytics，請移至您的個別 Azure Front Door、應用程式閘道或 CDN 資源：

1. 選取 [診斷設定]****。
2. 選取 [+新增診斷設定]。 
3. 在 [診斷設定] 頁面中：
   1. 輸入名稱。 
   1. 選取 [傳送至 Log Analytics]****。 
   1. 選擇 [記錄目的地工作區]。 
   1. 選取您要分析的記錄類型：
      1. 應用程式閘道： ' ApplicationGatewayAccessLog ' 和 ' ApplicationGatewayFirewallLog '
      1. Azure Front Door： ' FrontDoorAccessLog ' 和 ' FrontDoorFirewallLog '
      1. CDN： ' AzureCdnAccessLog '
   1. 選取 [儲存]。

   :::image type="content" source="media//waf-sentinel/diagnostics-setting.png" alt-text="WAF 動作篩選":::

4. 在 Azure 首頁上，于搜尋列中輸入 **Azure Sentinel** ，然後選取 **Azure Sentinel** 資源。 
2. 選取已存在的工作區，或在 Sentinel 中建立新的工作區。 
3. 在左側面板的 [設定 **] 下選取** [ **資料連線器**]。
4. 搜尋 **microsoft web 應用程式防火牆** ，然後選取 [ **microsoft web 應用程式防火牆] (WAF) **。 選取右下方的 [ **開啟連接器** ] 頁面。

   :::image type="content" source="media//waf-sentinel/data-connectors.png" alt-text="WAF 動作篩選":::

8. 針對您想要擁有記錄分析資料的每個 WAF 資源，遵循設定中的 **指示（如果** 您先前還沒這麼做）。
6. 完成設定個別 WAF 資源之後，請選取 [ **下一個步驟** ] 索引標籤。選取其中一個建議的活頁簿。 此活頁簿會使用先前啟用的所有記錄分析資料。 您的 WAF 資源現在應該會有一個正常運作的 WAF 活頁簿。

   :::image type="content" source="media//waf-sentinel/waf-workbooks.png" alt-text="WAF 動作篩選":::


## <a name="next-steps"></a>接下來的步驟

- [深入瞭解 Azure Sentinel](../sentinel/overview.md)
- [深入瞭解 Azure 監視器活頁簿](../azure-monitor/platform/workbooks-overview.md)

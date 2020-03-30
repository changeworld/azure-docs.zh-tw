---
title: 使用門戶自訂規則 - Azure Web 應用程式防火牆
description: 本文提供有關如何使用 Azure 門戶在應用程式閘道中自訂 Web 應用程式防火牆規則的資訊。
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: c4635333614ee1c0fd0322c29a659380fb4315c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74048369"
---
# <a name="customize-web-application-firewall-rules-using-the-azure-portal"></a>使用 Azure 門戶自訂 Web 應用程式防火牆規則

Azure 應用程式閘道 Web 應用程式防火牆 （WAF） 為 Web 應用程式提供保護。 這些保護是由開放 Web 應用程式安全性專案 (OWASP) 的核心規則集 (CRS) 所提供。 某些規則可能會導致誤判，並封鎖真正的流量。 因此，應用程式閘道會提供功能以自訂規則群組與規則。 有關特定規則組和規則的詳細資訊，請參閱 Web[應用程式防火牆 CRS 規則組和規則清單](application-gateway-crs-rulegroups-rules.md)。

>[!NOTE]
> 如果您的應用程式閘道並未使用 WAF 層，右窗格中會出現將應用程式閘道升級至 WAF 層的選項。 

![啟用 WAF][fig1]

## <a name="view-rule-groups-and-rules"></a>檢視規則群組與規則

**檢視規則群組與規則**
1. 瀏覽至應用程式閘道，然後選取 [Web 應用程式防火牆]****。  
2. 選擇**您的 WAF 策略**。
2. 選擇**託管規則**。

   這個檢視會在透過所選擇規則集提供之所有規則群組的頁面上顯示一個表格， 並選取所有的規則核取方塊。

## <a name="disable-rule-groups-and-rules"></a>停用規則群組與規則

> [!IMPORTANT]
> 禁用任何規則組或規則時要小心。 這可能會使您面臨更高的安全風險。

禁用規則時，可以在一個或多個規則組下禁用整個規則組或特定規則。 

**停用規則群組或特定規則**

   1. 搜尋您要停用的規則或規則群組。
   2. 選擇要禁用的規則的核取方塊。 
   3. 選擇所選規則的頁面頂部的操作（啟用/禁用）。
   2. 選取 [儲存]****。 

![儲存變更][3]

## <a name="mandatory-rules"></a>強制性規則

以下清單包含導致 WAF 在"預防模式"期間阻止請求的條件。 在檢測模式下，它們被記錄為異常。

無法配置或禁用這些功能：

* 如果無法分析請求正文會導致請求被阻止，除非關閉正文檢查（XML、JSON、表單資料）
* 請求正文（沒有檔）資料長度大於配置的限制
* 請求正文（包括檔）大於限制
* WAF 引擎中發生內部錯誤

CRS 3.x 具體：

* 入站異常分數超過閾值

## <a name="next-steps"></a>後續步驟

設定已停用的規則之後，您可以了解如何檢視 WAF 記錄。 有關詳細資訊，請參閱[應用程式閘道診斷](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging)。

[fig1]: ../media/application-gateway-customize-waf-rules-portal/1.png
[3]: ../media/application-gateway-customize-waf-rules-portal/figure3.png

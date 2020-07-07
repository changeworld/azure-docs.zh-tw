---
title: 使用入口網站自訂規則-Azure Web 應用程式防火牆
description: 本文提供如何在應用程式閘道中使用 Azure 入口網站自訂 Web 應用程式防火牆規則的相關資訊。
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: c4635333614ee1c0fd0322c29a659380fb4315c9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "74048369"
---
# <a name="customize-web-application-firewall-rules-using-the-azure-portal"></a>使用 Azure 入口網站自訂 Web 應用程式防火牆規則

Azure 應用程式閘道 Web 應用程式防火牆（WAF）提供 Web 應用程式的保護。 這些保護是由開放 Web 應用程式安全性專案 (OWASP) 的核心規則集 (CRS) 所提供。 某些規則可能會導致誤判，並封鎖真正的流量。 因此，應用程式閘道會提供功能以自訂規則群組與規則。 如需特定規則群組和規則的詳細資訊，請參閱[Web 應用程式防火牆 CRS 規則群組與規則的清單](application-gateway-crs-rulegroups-rules.md)。

>[!NOTE]
> 如果您的應用程式閘道並未使用 WAF 層，右窗格中會出現將應用程式閘道升級至 WAF 層的選項。 

![啟用 WAF][fig1]

## <a name="view-rule-groups-and-rules"></a>檢視規則群組與規則

**檢視規則群組與規則**
1. 瀏覽至應用程式閘道，然後選取 [Web 應用程式防火牆]****。  
2. 選取您的**WAF 原則**。
2. 選取 [**受控規則**]。

   這個檢視會在透過所選擇規則集提供之所有規則群組的頁面上顯示一個表格， 並選取所有的規則核取方塊。

## <a name="disable-rule-groups-and-rules"></a>停用規則群組與規則

> [!IMPORTANT]
> 停用任何規則群組或規則時，請務必小心。 這可能會讓您暴露于增加安全性風險。

當您停用規則時，可以停用一個或多個規則群組下的整個規則群組或特定規則。 

**停用規則群組或特定規則**

   1. 搜尋您要停用的規則或規則群組。
   2. 選取您想要停用之規則的核取方塊。 
   3. 針對選取的規則選取頁面頂端的 [動作] （[啟用/停用]）。
   2. 選取 [儲存]。 

![儲存變更][3]

## <a name="mandatory-rules"></a>強制性規則

下列清單包含導致 WAF 在預防模式下封鎖要求的條件。 在偵測模式中，它們會記錄為例外狀況。

無法設定或停用這些功能：

* 無法剖析要求本文會導致要求遭到封鎖，除非已關閉主體檢查（XML、JSON、表單資料）
* 要求主體（不含檔案）資料長度大於設定的限制
* 要求主體（包含檔案）大於限制
* WAF 引擎中發生內部錯誤

CRS 3.x 特定：

* 輸入異常分數超過閾值

## <a name="next-steps"></a>後續步驟

設定已停用的規則之後，您可以了解如何檢視 WAF 記錄。 如需詳細資訊，請參閱[應用程式閘道診斷](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging)。

[fig1]: ../media/application-gateway-customize-waf-rules-portal/1.png
[3]: ../media/application-gateway-customize-waf-rules-portal/figure3.png

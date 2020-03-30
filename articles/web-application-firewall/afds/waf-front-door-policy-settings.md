---
title: 使用 Azure 前門的 Web 應用程式防火牆的策略設置
description: 瞭解 Web 應用程式防火牆 （WAF）。
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 08b21ccd7f7958f00546583f680ecb8cde4a20c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932605"
---
# <a name="policy-settings-for-web-application-firewall-on-azure-front-door"></a>Azure 前門 Web 應用程式防火牆的策略設置

Web 應用程式防火牆 （WAF） 策略允許您通過一組自訂和託管規則控制對 Web 應用程式的訪問。 WAF 策略名稱必須是唯一的。 如果嘗試使用現有名稱，您將收到驗證錯誤。 有多個策略級別設置適用于為該策略指定的所有規則，如本文所述。

## <a name="waf-state"></a>WAF 狀態

前門的 WAF 策略可以處於以下兩種狀態之一：
- **已啟用：** 啟用策略後，WAF 會主動檢查傳入請求，並根據規則定義執行相應的操作
- **禁用：** - 禁用策略時，WAF 檢查暫停。 傳入請求將繞過 WAF，並根據前門路由發送到後端。

## <a name="waf-mode"></a>WAF 模式

WAF 原則可以設定為在下列兩種模式中執行︰

- **檢測模式**在檢測模式下運行時，WAF 不會執行任何操作，除了監視和將請求及其匹配的 WAF 規則記錄到 WAF 日誌之外。 打開前門的日誌記錄診斷（使用門戶時，可以通過訪問 Azure 門戶中的 **"診斷"** 部分來實現這一點）。

- **預防模式**當配置為在預防模式下運行時，如果請求與規則匹配，WAF 將執行指定的操作。 所有相符的要求也會記錄在 WAF 記錄中。

## <a name="waf-response-for-blocked-requests"></a>阻止請求的 WAF 回應

預設情況下，當 WAF 由於匹配的規則而阻止請求時，它將返回 403 狀態碼 -**請求被阻止**的消息。 還會返回一個引用字串進行日誌記錄。

當 WAF 阻止請求時，可以定義自訂回應狀態碼和回應訊息。 支援以下自訂狀態碼：

- 200 OK
- 403 禁止
- 405 方法不允許
- 406 不可接受
- 429 請求太多

自訂回應狀態碼和回應訊息是策略級別設置。 配置後，所有被阻止的請求都獲取相同的自訂回應狀態和回應訊息。

## <a name="uri-for-redirect-action"></a>用於重定向操作的 URI

如果為 WAF 策略中包含的任何規則選擇了**REDIRECT**操作，則需要定義 URI 以將請求重定向到。 此重定向 URI 需要是有效的 HTTP（S） 網站，配置後，所有具有"REDIRECT"操作的請求匹配規則都將重定向到指定的網站。


## <a name="next-steps"></a>後續步驟
- 瞭解如何定義 WAF[自訂回應](waf-front-door-configure-custom-response-code.md)

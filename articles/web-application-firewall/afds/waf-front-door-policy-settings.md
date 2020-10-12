---
title: 具有 Azure Front Door 之 Web 應用程式防火牆的原則設定
description: 瞭解 (WAF) 的 Web 應用程式防火牆。
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 08b21ccd7f7958f00546583f680ecb8cde4a20c8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "75932605"
---
# <a name="policy-settings-for-web-application-firewall-on-azure-front-door"></a>Azure Front Door 上的 Web 應用程式防火牆的原則設定

Web 應用程式防火牆 (WAF) 原則可讓您透過一組自訂和受控規則來控制 Web 應用程式的存取。 WAF 原則名稱必須是唯一的。 如果您嘗試使用現有的名稱，您會收到驗證錯誤。 有多個原則層級設定適用于為該原則指定的所有規則，如本文所述。

## <a name="waf-state"></a>WAF 狀態

Front Door 的 WAF 原則可以是下列其中一種狀態：
- **已啟用：** 當原則啟用時，WAF 會主動檢查傳入的要求，並根據規則定義採取對應的動作
- **停用：** 當原則停用時，WAF 檢查會暫停。 傳入的要求將會略過 WAF，並根據 Front Door 路由傳送至後端。

## <a name="waf-mode"></a>WAF 模式

WAF 原則可以設定為在下列兩種模式中執行︰

- **偵測模式** 在偵測模式中執行時，WAF 不會執行監視以外的任何動作，並將要求和其相符的 WAF 規則記錄到 WAF 記錄。 當您使用入口網站時，開啟 Front Door (的記錄診斷，可以前往 Azure 入口網站) 中的 [ **診斷** ] 區段來達成。

- **預防模式** 當設定為在預防模式中執行時，如果要求符合規則，WAF 會採取指定的動作。 所有相符的要求也會記錄在 WAF 記錄中。

## <a name="waf-response-for-blocked-requests"></a>WAF 封鎖要求的回應

根據預設，當 WAF 因為符合規則而封鎖要求時，會傳回403狀態碼，其中包含 **要求被封鎖** 的訊息。 也會傳回參考字串來進行記錄。

您可以在 WAF 封鎖要求時，定義自訂的回應狀態碼和回應訊息。 以下是支援的自訂狀態碼：

- 200確定
- 403禁止
- 405不允許的方法
- 406無法接受
- 429太多要求

自訂回應狀態碼和回應訊息是原則層級設定。 一旦設定之後，所有封鎖的要求都會取得相同的自訂回應狀態和回應訊息。

## <a name="uri-for-redirect-action"></a>重新導向動作的 URI

如果針對 WAF 原則中包含的任一規則選取重新 **導向** 動作，您就必須定義 URI，以將要求重新導向至。 此重新導向 URI 必須是有效的 HTTP (S) 網站，且設定完成之後，所有符合規則並具有「重新導向」動作的要求都會重新導向至指定的網站。


## <a name="next-steps"></a>後續步驟
- 瞭解如何定義 WAF [自訂回應](waf-front-door-configure-custom-response-code.md)

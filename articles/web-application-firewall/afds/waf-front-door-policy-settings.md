---
title: 具有 Azure Front 的 Web 應用程式防火牆的原則設定
description: 學習 Web 應用程式防火牆（WAF）。
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 08b21ccd7f7958f00546583f680ecb8cde4a20c8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75932605"
---
# <a name="policy-settings-for-web-application-firewall-on-azure-front-door"></a>Azure Front 上 Web 應用程式防火牆的原則設定

Web 應用程式防火牆（WAF）原則可讓您透過一組自訂和受控規則來控制對 Web 應用程式的存取。 WAF 原則名稱必須是唯一的。 如果您嘗試使用現有的名稱，將會收到驗證錯誤。 有多個原則層級設定適用于針對該原則指定的所有規則，如本文所述。

## <a name="waf-state"></a>WAF 狀態

Front 門的 WAF 原則可以是下列兩種狀態的其中一種：
- **已啟用：** 當原則啟用時，WAF 會主動檢查傳入的要求，並根據規則定義採取對應的動作
- **已停用：** 當原則停用時，WAF 檢查會暫停。 傳入要求將會略過 WAF，並根據 Front 門板路由傳送至後端。

## <a name="waf-mode"></a>WAF 模式

WAF 原則可以設定為在下列兩種模式中執行︰

- **偵測模式**在偵測模式中執行時，WAF 不會接受 monitor 以外的任何動作，並將要求和其相符的 WAF 規則記錄到 WAF 記錄。 開啟前門的記錄診斷（使用入口網站時，可以前往 Azure 入口網站中的 [**診斷**] 區段達成此目的）。

- **預防模式**當設定為在防止模式中執行時，如果要求符合規則，WAF 會採取指定的動作。 所有相符的要求也會記錄在 WAF 記錄中。

## <a name="waf-response-for-blocked-requests"></a>已封鎖要求的 WAF 回應

根據預設，當 WAF 因為相符的規則而封鎖要求時，它會傳回403狀態碼與-**要求是封鎖的**訊息。 也會傳回記錄的參考字串。

當 WAF 封鎖要求時，您可以定義自訂回應狀態碼和回應訊息。 支援下列自訂狀態碼：

- 200正常
- 403禁止
- 不允許使用405方法
- 406無法接受
- 429太多要求

自訂回應狀態碼和回應訊息是原則層級設定。 一旦設定之後，所有封鎖的要求都會取得相同的自訂回應狀態和回應訊息。

## <a name="uri-for-redirect-action"></a>重新導向動作的 URI

如果已針對 WAF 原則中包含的任何規則選取重新**導向**動作，則必須定義 URI 以將要求重新導向至。 此重新導向 URI 必須是有效的 HTTP （S）網站，一旦設定之後，所有符合規則並具有「重新導向」動作的要求，都會重新導向至指定的網站。


## <a name="next-steps"></a>後續步驟
- 瞭解如何定義 WAF[自訂回應](waf-front-door-configure-custom-response-code.md)

---
title: 針對 Azure Front 門板設定問題進行疑難排解
description: 在本教學課程中，您會了解如何自行排解您可能會面臨的一些 Front Door 常見問題。
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/22/2018
ms.author: sharadag
ms.openlocfilehash: 40809fae312401cb62fabb10140b9bb7f60e3715
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88234766"
---
# <a name="troubleshooting-common-routing-issues"></a>針對常見路由問題進行疑難排解

本文說明如何針對您的 Azure Front 門板設定，針對您可能會面臨的一些常見路由問題進行疑難排解。

## <a name="503-response-from-front-door-after-a-few-seconds"></a>503幾秒後面板的回應

### <a name="symptom"></a>徵狀

- 傳送至後端的一般要求若未通過 Front，就會繼續進行，但透過 Front 大門會產生503錯誤回應。

- Front 門板的失敗會在幾秒後顯示 (通常會在30秒後) 

### <a name="cause"></a>原因

當您的後端超過超時設定時，就會發生此徵兆 (預設值為30秒，) 接收來自 Front 的要求，或超出此超時值，以將回應傳送至來自 Front 門的要求。 

### <a name="troubleshooting-steps"></a>疑難排解步驟

- 直接將要求傳送給您的後端 (而不需要透過 Front) ，並查看您的後端需要多久的時間才會回應。
- 透過前門傳送要求，並查看您是否看到任何503回應。 如果沒有，則這可能不是超時問題。 請聯絡支援人員。
- 如果透過 Front 門板進行，會產生503錯誤回應碼，然後設定 Front sendReceiveTimeout 欄位以將預設的超時時間延長為4分鐘， (240 秒) 。 設定位於底下 `backendPoolSettings` ，而且會呼叫 `sendRecvTimeoutSeconds` 。 

## <a name="requests-sent-to-the-custom-domain-returns-400-status-code"></a>傳送至自訂網域的要求會傳回400狀態碼

### <a name="symptom"></a>徵狀

- 您已建立 Front 門，但對網域或前端主機的要求傳回 HTTP 400 狀態碼。

- 您已建立從自訂網域到您所設定前端主機的 DNS 對應。 不過，將要求傳送至自訂網域主機名稱會傳回 HTTP 400 狀態碼，而且似乎不會路由傳送至您已設定的後端。

### <a name="cause"></a>原因

如果您尚未針對已新增為前端主機的自訂網域設定路由規則，則可能出現此徵狀。 即使已經為 Front Door 子網域 (*.azurefd.net) 下您的自訂網域具有 DNS 對應的前端主機設定路由規則，仍必須為該前端主機明確地新增路由規則。

### <a name="troubleshooting-steps"></a>疑難排解步驟

新增從自訂網域到所需後端集區的路由規則。

## <a name="front-door-is-not-redirecting-http-to-https"></a>Front 門不會將 HTTP 重新導向至 HTTPS

### <a name="symptom"></a>徵狀

您的 Front，有一條路由規則顯示 [將 HTTP 重新導向至 HTTPS]，但存取網域仍會將 HTTP 維護為通訊協定。

### <a name="cause"></a>原因

如果您沒有正確設定 Front 的路由規則，就可能會發生這種行為。 基本上，您目前的設定並不明確，而且可能會有衝突的規則。

### <a name="troubleshooting-steps"></a>疑難排解步驟

## <a name="request-to-frontend-hostname-returns-404-status-code"></a>對前端主機名稱的要求會傳回 404 狀態碼

### <a name="symptom"></a>徵狀

- 您已建立 Front Door，並設定了前端主機、至少有一個後端的後端集區，以及可將前端主機連到後端集區的路由規則。 將要求傳送至已設定的前端主機時，您的內容似乎無法使用，因為傳回了 HTTP 404 狀態碼。

### <a name="cause"></a>原因

這個徵狀的可能原因有幾個，包括︰

- 後端不是公開的後端，因此不會顯示在 Front 中。
- 後端設定錯誤，這會導致前門傳送錯誤的要求 (也就是說，您的後端只接受 HTTP，但未核取 [允許 HTTPS]，因此 Front 門會嘗試將 HTTPS 要求轉送) 。
- 後端會拒絕已透過要求轉送至後端的主機標頭。
- 尚未完整部署後端的組態。

### <a name="troubleshooting-steps"></a>疑難排解步驟

1. 部署時間
   - 確定您已等候 ~10 分鐘讓組態進行部署。

2. 檢查後端設定
    - 瀏覽到要求應路由傳送至的後端集區 (取決於您設定路由規則的方式) ，並確認「後端主機類型」__ 與後端主機名稱都正確。 如果後端是自訂主機，請確保您的拼字正確。 

    - 檢查您的 HTTP 和 HTTPS 連接埠。 在大部分情況下，80 和 443 (分別) 正確無誤，不需要任何變更。 不過，您的後端有可能並未如此設定，而且是在不同的連接埠上接聽。

        - 請檢查針對前端主機應路由傳送至的後端所設定的 [後端主機標頭]__。 在大部分情況下，此標頭應該與 [後端主機標頭]__ 相同。 不過，如果後端預期有不同的值，則不正確的值可能會導致各種 HTTP 4xx 狀態碼。 如果您輸入後端的 IP 位址，則可能必須將 [後端主機標頭]__ 設定為後端的主機名稱。


3. 檢查路由規則設定
    - 瀏覽到應會從有問題的前端主機名稱路由傳送至後端集區的路由規則。 確定接受的通訊協定設定正確，若非如此，請確定已正確設定 Front Door 在轉送要求時會使用的通訊協定。 [ _接受的通訊協定_ ] 欄位會決定 front 門板應接受的要求，而 _轉送通訊協定_ 則決定 front 門板應使用哪一個通訊協定來將要求轉送至後端。
         - 例如，如果後端只接受 HTTP 要求，則下列組態會有效：
            - [接受的通訊協定]__ 為 HTTP 和 HTTPS。 [轉送通訊協定]__ 為 HTTP。 比對要求無法運作，因為 HTTPS 是允許的通訊協定，而如果要求以 HTTPS 形式傳入，Front Door 會嘗試使用 HTTPS 轉送它。

            - [接受的通訊協定]__ 為 HTTP。 _轉送通訊協定_ 可能符合要求或 HTTP。

    - 預設會停用 [URL 重寫]__，而如果您想要縮小您要提供的後端裝載資源範圍，則應該只使用此欄位。 停用時，Front Doo 會轉送它所接收的相同要求路徑。 此欄位可能設定不正確，而 Front Door 向無法使用的後端要求資源，因而傳回 HTTP 404 狀態碼。


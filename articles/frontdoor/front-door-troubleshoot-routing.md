---
title: 針對 Azure Front Door 設定問題進行疑難排解
description: 在本教學課程中，您會了解如何自行排解您可能會面臨的一些 Front Door 常見問題。
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: 8e810a31fab4457e47329e37f54b16e6f488c9da
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "94337622"
---
# <a name="troubleshooting-common-routing-issues"></a>針對常見路由問題進行疑難排解

本文說明如何針對您的 Azure Front Door 設定所面臨的一些常見路由問題進行疑難排解。

## <a name="503-response-from-front-door-after-a-few-seconds"></a>在幾秒鐘後，來自 Front Door 的503回應

### <a name="symptom"></a>徵狀

* 傳送至您的後端但未通過 Front Door 的一般要求已成功，但透過 Front Door 會導致503錯誤回應。
* Front Door 的失敗會在幾秒後顯示 (通常會在30秒後出現) 

### <a name="cause"></a>原因

此問題的原因可能是下列兩種情況之一：
 
* 您的後端所花的時間超過設定的超時 (預設為30秒，) 接收來自 Front Door 的要求。
* 從 Front Door 傳送回應給要求所花費的時間比 timeout 值長。 

### <a name="troubleshooting-steps"></a>疑難排解步驟

*  (直接將要求傳送至您的後端，而不需要 Front Door 的) ，並查看您的後端需要多久的時間才能回應。
* 透過 Front Door 傳送要求，並查看您是否收到任何503回應。 如果沒有，則問題可能不是超時問題。 請連絡支援人員。
* 如果流覽 Front Door 會導致503錯誤回應碼，請設定 `sendReceiveTimeout` Front Door 的欄位。 您可以)  (240 秒，將預設超時時間延長為4分鐘。 設定是在底下， `backendPoolSettings` 並且會呼叫 `sendRecvTimeoutSeconds` 。 

## <a name="requests-sent-to-the-custom-domain-returns-400-status-code"></a>傳送至自訂網域的要求會傳回400狀態碼

### <a name="symptom"></a>徵狀

* 您已建立 Front Door，但對網域或前端主機的要求傳回 HTTP 400 狀態碼。
* 您已建立自訂網域到您所設定之前端主機的 DNS 對應。 不過，將要求傳送至自訂網域主機名稱會傳回 HTTP 400 狀態碼。 這似乎不會路由傳送至您所設定的後端。

### <a name="cause"></a>原因

如果您未針對新增為前端主機的自訂網域設定路由規則，就會發生此問題。 需要針對該前端主機明確新增路由規則。 即使已針對 Front Door 子域 ( *. azurefd.net) 下的前端主機設定了一個。

### <a name="troubleshooting-steps"></a>疑難排解步驟

新增自訂網域的路由規則，以將流量導向至選取的後端集區。

## <a name="front-door-doesnt-redirect-http-to-https"></a>Front Door 不會將 HTTP 重新導向至 HTTPS

### <a name="symptom"></a>徵狀

您的 Front Door 有一條路由規則，指出將 HTTP 重新導向至 HTTPS，但存取網域仍會以通訊協定的形式維護 HTTP。

### <a name="cause"></a>原因

如果您未正確設定 Front Door 的路由規則，則可能會發生這種行為。 基本上，您目前的設定不是特定的，而且可能會有衝突的規則。

### <a name="troubleshooting-steps"></a>疑難排解步驟

## <a name="request-to-frontend-hostname-returns-404-status-code"></a>對前端主機名稱的要求會傳回 404 狀態碼

### <a name="symptom"></a>徵狀

 您已藉由設定前端主機、其中至少有一個後端的後端集區，以及將前端主機連接到後端集區的路由規則，來建立 Front Door。 當您對已設定的前端主機提出要求時，您的內容無法使用，因此會傳回 HTTP 404 狀態碼。

### <a name="cause"></a>原因

這個徵狀的可能原因有幾個，包括︰

* 後端不是公開的後端，Front Door 看不到。
* 後端的設定不正確，導致 Front Door 傳送錯誤的要求。 換句話說，您的後端只接受 HTTP，而且您尚未取消核取允許 HTTPS。 因此 Front Door 正在嘗試轉寄 HTTPS 要求。
* 後端會拒絕已透過要求轉送至後端的主機標頭。
* 尚未完全部署後端的設定。

### <a name="troubleshooting-steps"></a>疑難排解步驟

1. 部署時間
   * 確定您已等候約10分鐘的時間來部署設定。

2. 檢查後端設定
    * 流覽至要求應路由傳送至 (的後端集區，取決於您如何) 設定路由規則。 確認 *後端主機類型* 和後端主機名稱正確無誤。 如果後端是自訂主機，請確定您的拼寫正確。 

    * 檢查您的 HTTP 和 HTTPS 連接埠。 在大多數情況下，80和 443 (分別) 正確，而且不需要任何變更。 不過，您的後端有機會無法以這種方式進行設定，而是在不同的埠上接聽。

        * 請檢查針對前端主機應路由傳送至的後端所設定的 [後端主機標頭]。 在大部分情況下，此標頭應該與 [後端主機標頭] 相同。 不過，如果後端預期有不同的值，則不正確的值可能會導致各種 HTTP 4xx 狀態碼。 如果您輸入後端的 IP 位址，則可能必須將 [後端主機標頭] 設定為後端的主機名稱。

3. 檢查路由規則設定：
    * 瀏覽到應會從有問題的前端主機名稱路由傳送至後端集區的路由規則。 請確定轉送要求時，已正確設定接受的通訊協定。 [ *接受的通訊協定* ] 欄位會決定 Front Door 應接受哪些要求。 *轉送通訊協定* 會決定 Front Door 應使用哪個通訊協定將要求轉送至後端。
         * 例如，如果後端只接受 HTTP 要求，則下列組態會有效：
            * [接受的通訊協定] 為 HTTP 和 HTTPS。 [轉送通訊協定] 為 HTTP。 比對要求無法運作，因為 HTTPS 是允許的通訊協定，而如果要求以 HTTPS 形式傳入，Front Door 會嘗試使用 HTTPS 轉寄。

            * [接受的通訊協定] 為 HTTP。 *轉送通訊協定* 可能符合要求或 HTTP。
    - 預設會停用 *Url 重寫* 。 只有當您想要將想要提供的後端託管資源範圍縮小時，才會使用此欄位。 停用時，Front Doo 會轉送它所接收的相同要求路徑。 您可以 jeffv 此欄位。 因此，當 Front Door 要求來自無法使用的後端資源時，它會傳回 HTTP 404 狀態碼。

## <a name="request-to-frontend-host-name-returns-411-status-code"></a>前端主機名稱的要求傳回411狀態碼

### <a name="symptom"></a>徵狀

您已建立 Front Door，並設定了前端主機、至少有一個後端的後端集區，以及可將前端主機連到後端集區的路由規則。 將要求傳送至已設定的前端主機時，您的內容似乎無法使用，因為傳回 HTTP 411 狀態碼。

這些要求的回應可能也會在回應本文中包含 HTML 錯誤頁面，其中包含解釋性語句。 例如：`HTTP Error 411. The request must be chunked or have a content length`

### <a name="cause"></a>原因

此徵兆有幾個可能的原因：但是，整體的原因是您的 HTTP 要求並非完全符合 RFC 規範。 

不符合規範的範例是在 `POST` 沒有 `Content-Length` 或標頭 (傳送的要求 `Transfer-Encoding` ，例如使用 `curl -X POST https://example-front-door.domain.com`) 。 此要求不符合 [RFC 7230](https://tools.ietf.org/html/rfc7230#section-3.3.2) 中所設定的需求，而且您的 Front Door 會封鎖 HTTP 411 回應。

這種行為與 Front Door 的 WAF 功能不同。 目前沒有任何方法可以停用此行為。 所有 HTTP 要求都必須符合需求，即使 WAF 功能未使用也一樣。

### <a name="troubleshooting-steps"></a>疑難排解步驟

- 確認您的要求符合所需 Rfc 中所設定的需求。

- 記下為了回應您的要求所傳回的任何 HTML 訊息主體，因為它們通常會 *說明您的* 要求不符合規範。

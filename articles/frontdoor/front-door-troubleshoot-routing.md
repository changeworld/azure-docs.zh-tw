---
title: 針對 Azure Front Door 設定問題進行疑難排解
description: 在本教學課程中，您將瞭解如何自行針對您可能會面臨 Azure Front Door 實例的一些常見問題進行疑難排解。
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
ms.openlocfilehash: 15cdcefe628a392704e650b560243e2f6a134ec2
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629983"
---
# <a name="troubleshooting-common-routing-problems"></a>針對常見的路由問題進行疑難排解

本文說明如何針對您可能會面臨 Azure Front Door 設定的常見路由問題進行疑難排解。

## <a name="503-response-from-azure-front-door-after-a-few-seconds"></a>在幾秒鐘後，來自 Azure Front Door 的503回應

### <a name="symptom"></a>徵狀

* 傳送至您的後端但未通過 Azure Front Door 的一般要求會成功。 透過 Azure Front Door 會導致503錯誤回應。
* Azure Front Door 的失敗通常會在大約30秒之後顯示。

### <a name="cause"></a>原因

此問題的原因可能是下列兩種情況之一：
 
* 您的後端所花的時間超過設定的超時 (預設為30秒，) 接收來自 Azure Front Door 的要求。
* 從 Azure Front Door 傳送回應給要求所花費的時間比 timeout 值長。 

### <a name="troubleshooting-steps"></a>疑難排解步驟

*  (直接將要求傳送至您的後端，而不需透過 Azure Front Door) 。 查看您的後端通常需要多久的時間才能回應。
* 透過 Azure Front Door 傳送要求，並查看您是否收到任何503回應。 如果沒有，則問題可能不是超時問題。 請連絡支援人員。
* 如果進行 Azure Front Door 會導致503錯誤回應碼，請設定 Azure Front Door 的 `sendReceiveTimeout` 欄位。 您可以)  (240 秒，將預設超時時間延長為4分鐘。 這項設定會在 `backendPoolSettings` 和下呼叫 `sendRecvTimeoutSeconds` 。 

## <a name="requests-sent-to-the-custom-domain-return-a-400-status-code"></a>傳送至自訂網域的要求會傳回400狀態碼

### <a name="symptom"></a>徵狀

* 您已建立 Azure Front Door 實例，但對網域或前端主機的要求傳回 HTTP 400 狀態碼。
* 您已建立自訂網域到您所設定之前端主機的 DNS 對應。 不過，將要求傳送至自訂網域主機名稱會傳回 HTTP 400 狀態碼。 它不會路由傳送至您所設定的後端。

### <a name="cause"></a>原因

如果您未針對新增為前端主機的自訂網域設定路由規則，就會發生此問題。 需要針對該前端主機明確新增路由規則。 即使已針對 Azure Front Door 子域 ( *. azurefd.net) 下的前端主機設定路由規則也是如此。

### <a name="troubleshooting-steps"></a>疑難排解步驟

新增自訂網域的路由規則，以將流量導向至選取的後端集區。

## <a name="azure-front-door-doesnt-redirect-http-to-https"></a>Azure Front Door 不會將 HTTP 重新導向至 HTTPS

### <a name="symptom"></a>徵狀

Azure Front Door 的路由規則會將 HTTP 重新導向至 HTTPS，但存取網域仍會維持 HTTP 作為通訊協定。

### <a name="cause"></a>原因

如果您未正確設定 Azure Front Door 的路由規則，則可能會發生這種行為。 基本上，您目前的設定不是特定的，而且可能會有衝突的規則。

### <a name="troubleshooting-steps"></a>疑難排解步驟

## <a name="request-to-a-frontend-host-name-returns-a-404-status-code"></a>前端主機名稱的要求會傳回404狀態碼

### <a name="symptom"></a>徵狀

您已藉由設定前端主機、其中至少有一個後端的後端集區，以及將前端主機連接到後端集區的路由規則，來建立 Azure Front Door 實例。 當您對設定的前端主機提出要求時，無法使用您的內容。 因此，要求會傳回 HTTP 404 狀態碼。

### <a name="cause"></a>原因

這個徵狀的可能原因有幾個，包括︰

* 後端不是公開的後端，Azure Front Door 看不到。
* 後端的設定不正確，導致 Azure Front Door 傳送錯誤的要求。 換句話說，您的後端只接受 HTTP，而且您不允許 HTTPS。 因此 Azure Front Door 正在嘗試轉寄 HTTPS 要求。
* 後端會拒絕已透過要求轉送至後端的主機標頭。
* 尚未完全部署後端的設定。

### <a name="troubleshooting-steps"></a>疑難排解步驟

* 檢查部署時間：
   * 確定您已等候至少10分鐘的時間來部署設定。

* 檢查後端設定：
    * 移至要求應路由傳送至的後端集區。  (取決於您設定路由規則的方式。 ) 驗證後端主機類型和後端主機名稱是否正確。 如果後端是自訂主機，請確定您的拼寫正確。 

    * 檢查您的 HTTP 和 HTTPS 連接埠。 在大多數情況下，80和 443 (分別) 正確，且不需要任何變更。 不過，您的後端有機會無法以這種方式進行設定，而是在不同的埠上接聽。

    * 檢查為前端主機應路由傳送至的後端所設定的後端主機標頭。 在大部分情況下，此標頭應與後端主機名稱相同。 不過，如果後端預期有不同的值，則不正確的值可能會導致各種 HTTP 4xx 狀態碼。 如果您輸入後端的 IP 位址，您可能需要將後端主機標頭設定為後端的主機名稱。

* 檢查路由規則設定：
    * 移至應從有問題的前端主機名稱路由傳送至後端集區的路由規則。 請確定在轉送要求時，已正確設定接受的通訊協定。 [ **接受的通訊協定** ] 欄位會決定 Azure Front Door 應接受哪些要求。 轉送通訊協定會決定 Azure Front Door 應使用哪個通訊協定將要求轉送至後端。
      
      例如，如果後端只接受 HTTP 要求，則下列設定會有效：
            
      * [接受的通訊協定] 為 HTTP 和 HTTPS。 轉送通訊協定為 HTTP。 符合要求將無法運作，因為 HTTPS 是允許的通訊協定。 如果要求以 HTTPS 形式傳入，Azure Front Door 會嘗試使用 HTTPS 轉寄。

      * 接受的通訊協定為 HTTP。 轉送通訊協定可能是符合要求或 HTTP。
    - 預設會停用 **Url 重寫** 。 只有當您想要將想要提供的後端託管資源範圍縮小時，才會使用此欄位。 停用此欄位時，Azure Front Door 會將它所接收的相同要求路徑轉寄。 您可以 jeffv 此欄位。 因此，當 Azure Front Door 要求來自無法使用的後端資源時，它會傳回 HTTP 404 狀態碼。

## <a name="request-to-the-frontend-host-name-returns-a-411-status-code"></a>前端主機名稱的要求會傳回411狀態碼

### <a name="symptom"></a>徵狀

您已建立 Azure Front Door 實例，並設定前端主機、其中至少有一個後端的後端集區，以及將前端主機連接到後端集區的路由規則。 當要求移至設定的前端主機時，您的內容似乎無法使用，因為傳回 HTTP 411 狀態碼。

這些要求的回應可能也會在回應本文中包含 HTML 錯誤頁面，其中包含解釋性語句。 例如：`HTTP Error 411. The request must be chunked or have a content length`。

### <a name="cause"></a>原因

此徵兆有幾個可能的原因。 整體的原因是您的 HTTP 要求並非完全符合 RFC 規範。 

不符合規範的範例是在 `POST` 沒有 `Content-Length` 或 `Transfer-Encoding` 標頭 (傳送的要求，例如使用 `curl -X POST https://example-front-door.domain.com`) 。 此要求不符合 [RFC 7230](https://tools.ietf.org/html/rfc7230#section-3.3.2)中所設定的需求。 Azure Front Door 會封鎖 HTTP 411 回應。

這種行為與 Web 應用程式防火牆不同， (WAF) Azure Front Door 的功能。 目前沒有任何方法可以停用此行為。 所有 HTTP 要求都必須符合需求，即使 WAF 功能未使用也一樣。

### <a name="troubleshooting-steps"></a>疑難排解步驟

- 確認您的要求符合所需 Rfc 中所設定的需求。

- 記下為了回應您的要求所傳回的任何 HTML 訊息主體。 訊息主體通常會 *說明您的* 要求不符合規範。

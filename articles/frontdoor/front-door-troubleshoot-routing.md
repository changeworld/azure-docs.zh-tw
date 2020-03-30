---
title: 解決 Azure 前門配置問題
description: 在本教學課程中，您會了解如何自行排解您可能會面臨的一些 Front Door 常見問題。
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2018
ms.author: sharadag
ms.openlocfilehash: 962c884eb8adc05e5d50b6b254d5c3f0b18af556
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471501"
---
# <a name="troubleshooting-common-routing-issues"></a>針對常見路由問題進行疑難排解

本文介紹如何解決 Azure 前門配置可能面臨的一些常見路由問題。

## <a name="503-response-from-front-door-after-a-few-seconds"></a>幾秒鐘後前門的503回應

### <a name="symptom"></a>徵狀

- 發送到後端而不經過前門的常規請求將成功，但通過前門會導致 503 個錯誤回應。

- 前門故障在幾秒鐘後顯示（通常在 30 秒後左右）

### <a name="cause"></a>原因

當您的後端超出超時配置（預設為 30 秒）才能接收前門的請求，或者如果超出此超時值才能從前門發送對請求的回應時，就會發生此症狀。 

### <a name="troubleshooting-steps"></a>疑難排解步驟

- 直接將要求傳送到後端（無需通過前門），並查看後端回應所需的通常時間。
- 通過前門發送請求，查看您是否看到任何 503 回應。 如果不是，則這可能不是超時問題。 請聯繫支援人員。
- 如果通過前門會導致 503 錯誤回應代碼，則為前門配置"發送接收超時"欄位，將預設超時延長至 4 分鐘（240 秒）。 設置在 下`backendPoolSettings`稱為`sendRecvTimeoutSeconds`。 

## <a name="requests-sent-to-the-custom-domain-returns-400-status-code"></a>發送到自訂域的請求返回 400 狀態碼

### <a name="symptom"></a>徵狀

- 您已創建前門，但對域或前端主機的請求正在返回 HTTP 400 狀態碼。

- 您已建立從自訂網域到您所設定前端主機的 DNS 對應。 不過，將要求傳送至自訂網域主機名稱會傳回 HTTP 400 狀態碼，而且似乎不會路由傳送至您已設定的後端。

### <a name="cause"></a>原因

如果您尚未針對已新增為前端主機的自訂網域設定路由規則，則可能出現此徵狀。 即使已經為 Front Door 子網域 (*.azurefd.net) 下您的自訂網域具有 DNS 對應的前端主機設定路由規則，仍必須為該前端主機明確地新增路由規則。

### <a name="troubleshooting-steps"></a>疑難排解步驟

新增從自訂網域到所需後端集區的路由規則。

## <a name="front-door-is-not-redirecting-http-to-https"></a>前門未將 HTTP 重定向到 HTTPS

### <a name="symptom"></a>徵狀

前門有一個路由規則，該規則表示將 HTTP 重定向到 HTTPS，但訪問域仍保留 HTTP 作為協定。

### <a name="cause"></a>原因

如果未正確配置前門的路由規則，則可能發生此行為。 基本上，您當前的配置並不具體，並且可能有衝突的規則。

### <a name="troubleshooting-steps"></a>疑難排解步驟

## <a name="request-to-frontend-hostname-returns-404-status-code"></a>對前端主機名稱的要求會傳回 404 狀態碼

### <a name="symptom"></a>徵狀

- 您已建立 Front Door，並設定了前端主機、至少有一個後端的後端集區，以及可將前端主機連到後端集區的路由規則。 將要求傳送至已設定的前端主機時，您的內容似乎無法使用，因為傳回了 HTTP 404 狀態碼。

### <a name="cause"></a>原因

這個徵狀的可能原因有幾個，包括︰

- 後端不是面向後端的公共，前門不可見。
- 後端配置錯誤，導致前門發送錯誤的請求（即，後端僅接受 HTTP，但您尚未取消選中允許 HTTPS，因此前門正在嘗試轉發 HTTPS 請求）。
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
    - 瀏覽到應會從有問題的前端主機名稱路由傳送至後端集區的路由規則。 確定接受的通訊協定設定正確，若非如此，請確定已正確設定 Front Door 在轉送要求時會使用的通訊協定。 _接受的協定_欄位確定前門應接受哪些請求，_轉發協定_確定前門應使用什麼協定將請求轉發到後端。
         - 例如，如果後端只接受 HTTP 要求，則下列組態會有效：
            - [接受的通訊協定]__ 為 HTTP 和 HTTPS。 [轉送通訊協定]__ 為 HTTP。 比對要求無法運作，因為 HTTPS 是允許的通訊協定，而如果要求以 HTTPS 形式傳入，Front Door 會嘗試使用 HTTPS 轉送它。

            - [接受的通訊協定]__ 為 HTTP。 [轉送通訊協定]__ 為比對要求或 HTTPS。

    - 預設會停用 [URL 重寫]__，而如果您想要縮小您要提供的後端裝載資源範圍，則應該只使用此欄位。 停用時，Front Doo 會轉送它所接收的相同要求路徑。 此欄位可能設定不正確，而 Front Door 向無法使用的後端要求資源，因而傳回 HTTP 404 狀態碼。


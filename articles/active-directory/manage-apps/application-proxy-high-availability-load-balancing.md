---
title: 高可用性和負載平衡 - Azure AD 應用程式代理
description: 流量分發如何與應用程式代理部署配合使用。 包括有關如何優化連接器性能和為後端伺服器使用負載平衡的提示。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3202c2fbfedfce0b0b52be94b1e0d165a6e72546
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481308"
---
# <a name="high-availability-and-load-balancing-of-your-application-proxy-connectors-and-applications"></a>應用程式代理連接器和應用程式的高可用性和負載平衡

本文介紹了流量分發如何與應用程式代理部署一起工作。 我們將討論：

- 流量如何在使用者和連接器之間分配，以及優化連接器性能的提示

- 連接器和後端應用伺服器之間的流量如何流動，以及多個後端伺服器之間負載平衡的建議

## <a name="traffic-distribution-across-connectors"></a>連接器之間的流量分佈

連接器基於高可用性原則建立其連接。 不能保證流量始終在連接器之間均勻分佈，並且沒有會話相關性。 但是，使用方式各不相同，請求會隨機發送到應用程式代理服務實例。 因此，流量通常幾乎均勻地分佈在連接器上。 下圖和步驟說明了如何在使用者和連接器之間建立連接。

![顯示使用者和連接器之間連接的圖表](media/application-proxy-high-availability-load-balancing/application-proxy-connections.png)

1. 用戶端設備上的使用者嘗試訪問通過應用程式代理髮布的本地應用程式。
2. 請求通過 Azure 負載等化器來確定哪個應用程式代理服務實例應接受該請求。 每個區域都有數十個實例可用於接受請求。 此方法有助於跨服務實例均勻分配流量。
3. 要求傳送到[服務匯流排](https://docs.microsoft.com/azure/service-bus-messaging/)。
4. 維修匯流排檢查連接以前是否使用了連接器組中的現有連接器。 如果是這樣，它將重用連接。 如果尚未將連接器與連接配對，它將隨機播放可用連接器以向其發出信號。 然後，連接器從服務匯流排接收請求。

   - 在步驟 2 中，請求轉到不同的應用程式代理服務實例，因此更有可能使用不同的連接器進行連接。 因此，連接器在組中幾乎均勻使用。

   - 僅當連接斷開或出現 10 分鐘的閒置時間時，才會重新建立連接。 例如，當電腦或連接器服務重新開機或網路中斷時，連接可能會中斷。

5. 連接器將請求傳遞到應用程式的後端伺服器。 然後，應用程式將回應發送回連接器。
6. 連接器通過打開到請求來自的服務實例的出站連接來完成回應。 然後，此連接立即關閉。 預設情況下，每個連接器限制為 200 個併發出站連接。
7. 然後，回應從服務實例傳回用戶端。
8. 來自同一連接的後續請求重複上述步驟，直到此連接斷開或空閒 10 分鐘。

應用程式通常具有許多資源，並在載入時打開多個連接。 每個連接都經過上述步驟，以分配給服務實例，如果連接以前尚未與連接器配對，請選擇新的可用連接器。


## <a name="best-practices-for-high-availability-of-connectors"></a>連接器高可用性的最佳做法

- 由於流量在連接器之間分配的方式，因此連接器組中始終至少有兩個連接器至關重要。 三個連接器優先在連接器之間提供額外的緩衝。 要確定所需的連接器的正確數量，請遵循容量規劃文檔。

- 將連接器放在不同的出站連接上，以避免單點故障。 如果連接器使用相同的出站連接，則連接的網路問題可能會影響使用該連接的所有連接器。

- 避免在連接到生產應用程式時強制連接器重新開機。 這樣做可能會對連接器之間的流量分佈產生負面影響。 重新開機連接器會導致更多連接器不可用，並強制連接到其餘可用連接器。 結果是連接器最初使用不均勻。

- 避免對連接器和 Azure 之間的出站 TLS 通信進行各種形式的內聯檢查。 這種類型的內聯檢查會導致通信流下降。

- 確保保持連接器的自動更新運行。 如果應用程式代理連接器更新程式服務正在運行，則連接器將自動更新並接收最新的升級。 如果您在伺服器上沒有看到連接器更新程式服務，則需要重新安裝您的連接器以取得任何更新。

## <a name="traffic-flow-between-connectors-and-back-end-application-servers"></a>連接器和後端應用程式伺服器之間的流量

高可用性是一個因素的另一個關鍵領域是連接器和後端伺服器之間的連接。 當應用程式通過 Azure AD 應用程式代理髮布時，從使用者到應用程式的流量通過三個躍點流：

1. 使用者連接到 Azure 上的 Azure AD 應用程式代理服務公共終結點。 在用戶端的原始用戶端 IP 位址（公共）和應用程式代理終結點的 IP 位址之間建立連接。
2. 應用程式代理連接器從應用程式代理服務中拉出用戶端的 HTTP 要求。
3. 應用程式代理連接器連接到目標應用程式。 連接器使用自己的 IP 位址建立連接。

![通過應用程式代理連接到應用程式的使用者圖](media/application-proxy-high-availability-load-balancing/application-proxy-three-hops.png)

### <a name="x-forwarded-for-header-field-considerations"></a>X-前行-用於標頭欄位注意事項
在某些情況下（如審核、負載平衡等），需要與本地環境共用外部用戶端的原始 IP 位址。 為了滿足這一要求，Azure AD 應用程式代理連接器將具有原始用戶端 IP 位址（公共）的 X-前轉-for 標頭欄位添加到 HTTP 要求中。 然後，相應的網路設備（負載等化器、防火牆）或 Web 服務器或後端應用程式可以讀取和使用資訊。

## <a name="best-practices-for-load-balancing-among-multiple-app-servers"></a>多個應用伺服器之間負載平衡的最佳做法
當分配給應用程式代理應用程式的連接器組具有兩個或多個連接器，並且您在多個伺服器（伺服器場）上運行後端 Web 應用程式時，需要良好的負載平衡策略。 一個好的策略可確保伺服器均勻地接收用戶端請求，並防止伺服器場中伺服器過度使用或利用率低。
### <a name="scenario-1-back-end-application-does-not-require-session-persistence"></a>方案 1：後端應用程式不需要會話持久性
最簡單的方案是後端 Web 應用程式不需要會話粘性（會話持久性）。 來自使用者的任何請求都可以由伺服器場中的任何後端應用程式實例處理。 您可以使用第 4 層負載等化器，並在沒有相關性下對其進行配置。 某些選項包括 Microsoft 網路負載平衡和 Azure 負載等化器或來自其他供應商的負載等化器。 或者，可以配置迴圈 DNS。
### <a name="scenario-2-back-end-application-requires-session-persistence"></a>方案 2：後端應用程式需要會話持久性
在這種情況下，後端 Web 應用程式需要在經過身份驗證的會話期間會話粘性（會話持久性）。 來自使用者的所有請求必須由在伺服器場中的同一伺服器上運行的後端應用程式實例處理。
此方案可能更為複雜，因為用戶端通常建立到應用程式代理服務的多個連接。 不同連接的請求可能會到達伺服器場中不同的連接器和伺服器。 由於每個連接器都使用自己的 IP 位址進行此通信，因此負載等化器無法根據連接器的 IP 位址確保會話粘性。 源 IP 關聯也無法使用。
以下是方案 2 的一些選項：

- 選項 1：將會話持久性基於負載等化器設置的會話 Cookie。 建議使用此選項，因為它允許負載在後端伺服器之間更均勻地分佈。 它需要具有此功能的第 7 層負載等化器，該平衡器可以處理 HTTP 流量並終止 TLS 連接。 您可以使用 Azure 應用程式閘道（會話關聯）或其他供應商的負載等化器。

- 選項 2：將會話持久性基於 X-前行-for 標頭欄位。 此選項需要具有此功能的第 7 層負載等化器，該平衡器可以處理 HTTP 流量並終止 TLS 連接。  

- 選項 3：將後端應用程式佈建為不需要會話持久性。

請參閱軟體供應商的文檔，瞭解後端應用程式的負載平衡要求。

## <a name="next-steps"></a>後續步驟

- [啟用應用程式 Proxy](application-proxy-add-on-premises-application.md)
- [啟用單一登入](application-proxy-configure-single-sign-on-with-kcd.md)
- [啟用條件訪問](application-proxy-integrate-with-sharepoint-server.md)
- [使用應用程式 Proxy 疑難排解您遇到的問題](application-proxy-troubleshoot.md)
- [瞭解 Azure AD 體系結構如何支援高可用性](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-architecture)

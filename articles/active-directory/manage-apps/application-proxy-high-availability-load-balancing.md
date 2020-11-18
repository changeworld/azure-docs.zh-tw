---
title: 高可用性和負載平衡-Azure AD 應用程式 Proxy
description: 流量分佈如何與您的應用程式 Proxy 部署搭配運作。 包含如何針對後端伺服器優化連接器效能和使用負載平衡的秘訣。
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 403fa4cab94ad6149e388b10acccd9d5e7a2b7a8
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658157"
---
# <a name="high-availability-and-load-balancing-of-your-application-proxy-connectors-and-applications"></a>應用程式 Proxy 連接器和應用程式的高可用性和負載平衡

本文說明流量分佈如何與您的應用程式 Proxy 部署搭配運作。 我們會討論：

- 如何在使用者和連接器之間分散流量，以及優化連接器效能的秘訣

- 連接器和後端應用程式伺服器之間的流量，以及在多部後端伺服器之間進行負載平衡的建議

## <a name="traffic-distribution-across-connectors"></a>跨連接器的流量分佈

連接器會根據高可用性的原則來建立其連接。 不保證流量一律會平均分散到各個連接器，而且沒有會話親和性。 不過，使用方式會有所不同，而且要求會隨機傳送至應用程式 Proxy 服務實例。 如此一來，流量通常會平均分散到連接器。 下圖和下列步驟說明如何在使用者與連接器之間建立連接。

![顯示使用者與連接器之間連接的圖表](media/application-proxy-high-availability-load-balancing/application-proxy-connections.png)

1. 用戶端裝置上的使用者嘗試存取透過應用程式 Proxy 發佈的內部部署應用程式。
2. 要求會經過 Azure Load Balancer，以判斷哪個應用程式 Proxy 服務實例應該取得要求。 每個區域都有數十個實例可接受要求。 這種方法有助於將流量平均分散到服務實例。
3. 要求會傳送至 [服務匯流排](../../service-bus-messaging/index.yml)。
4. 服務匯流排會向可用的連接器發出信號。 然後，連接器會從服務匯流排挑選要求。
   - 在步驟2中，要求會移至不同的應用程式 Proxy 服務實例，因此更可能使用不同的連接器來建立連接。 如此一來，在群組內幾乎會平均使用連接器。
5. 連接器會將要求傳遞至應用程式的後端伺服器。 然後，應用程式會將回應傳回給連接器。
6. 連接器會開啟發出要求的服務實例輸出連線，以完成回應。 然後立即關閉此連接。 根據預設，每個連接器的限制為200個並行輸出連接。
7. 然後，回應會從服務實例傳回給用戶端。
8. 來自相同連接的後續要求會重複上述步驟。

應用程式通常會有許多資源，並且會在載入時開啟多個連接。 每個連線會經過上述步驟以配置給服務實例，如果連接尚未與連接器配對，請選取新的可用連接器。


## <a name="best-practices-for-high-availability-of-connectors"></a>連接器高可用性的最佳作法

- 因為流量會在連接器之間分散，以提供高可用性，所以連接器群組中一定要至少有兩個連接器。 建議將三個連接器提供給連接器之間的額外緩衝區。 若要判斷您需要的連接器數目正確，請遵循容量規劃檔。

- 將連接器放在不同的輸出連線，以避免發生單一失敗點。 如果連接器使用相同的輸出連線，連接的網路問題可能會影響使用它的所有連接器。

- 避免在連線到生產環境應用程式時，強制重新開機連接器。 這樣做可能會對連接器之間的流量分配造成負面影響。 重新開機連接器會導致更多的連接器無法使用，並強制連接至剩餘的可用連接器。 結果是一開始不平均使用的連接器。

- 避免在連接器與 Azure 之間的輸出 TLS 通訊上進行所有形式的內嵌檢查。 這種類型的內嵌檢查會導致通訊流程降級。

- 請務必為您的連接器保留執行自動更新。 如果應用程式 Proxy 連接器更新程式服務正在執行，您的連接器會自動更新並接收最新的升級。 如果您在伺服器上沒有看到連接器更新程式服務，則需要重新安裝您的連接器以取得任何更新。

## <a name="traffic-flow-between-connectors-and-back-end-application-servers"></a>連接器和後端應用程式伺服器之間的流量流動

高可用性的另一個關鍵區域是連接器與後端伺服器之間的連線。 透過 Azure AD 應用程式 Proxy 發佈應用程式時，從使用者到應用程式的流量會流經三個躍點：

1. 使用者連接到 Azure 上的 Azure AD 應用程式 Proxy 服務公用端點。 用戶端的原始用戶端 IP 位址 (公用) 用戶端和應用程式 Proxy 端點的 IP 位址之間，會建立連接。
2. 應用程式 Proxy 連接器會從應用程式 Proxy 服務提取用戶端的 HTTP 要求。
3. 應用程式 Proxy 連接器會連接到目標應用程式。 連接器會使用自己的 IP 位址來建立連接。

![透過應用程式 Proxy 連接至應用程式的使用者圖表](media/application-proxy-high-availability-load-balancing/application-proxy-three-hops.png)

### <a name="x-forwarded-for-header-field-considerations"></a>X-轉寄-標頭欄位考慮
在某些情況下 (像是審核、負載平衡等 ) ，需要在內部部署環境中共用外部用戶端的原始 IP 位址。 為了滿足需求，Azure AD 應用程式 Proxy 連接器會在 HTTP 要求 (公用) 的原始用戶端 IP 位址新增 X 轉送的標頭欄位。 適當的網路裝置 (負載平衡器、防火牆) 或 web 伺服器或後端應用程式可以讀取及使用資訊。

## <a name="best-practices-for-load-balancing-among-multiple-app-servers"></a>在多個應用程式伺服器之間進行負載平衡的最佳作法
當指派至應用程式 Proxy 應用程式的連接器群組有兩個以上的連接器，而且您在多部伺服器上執行後端 web 應用程式 (server farm) 時，需要良好的負載平衡策略。 良好的策略可確保伺服器會平均收取用戶端要求，並防止伺服器陣列中的伺服器過度使用或使用率過高。
### <a name="scenario-1-back-end-application-does-not-require-session-persistence"></a>案例1：後端應用程式不需要會話持續性
最簡單的情況是後端 web 應用程式不需要會話 (會話持續性) 。 任何來自使用者的要求都可由伺服器陣列中的任何後端應用程式實例處理。 您可以使用第4層負載平衡器，並將它設定為無親和性。 某些選項包括 Microsoft 網路負載平衡和 Azure Load Balancer，或來自另一個廠商的負載平衡器。 或者，您也可以設定迴圈配置資源 DNS。
### <a name="scenario-2-back-end-application-requires-session-persistence"></a>案例2：後端應用程式需要會話持續性
在此案例中，後端 web 應用程式需要會話持續性 (會話持續性) 在經過驗證的會話期間。 所有來自使用者的要求都必須由在伺服器陣列中相同伺服器上執行的後端應用程式實例來處理。
此案例可能更複雜，因為用戶端通常會建立多個與應用程式 Proxy 服務的連接。 不同連接的要求可能會在伺服器陣列中的不同連接器和伺服器抵達。 因為每個連接器都使用自己的 IP 位址進行這項通訊，所以負載平衡器無法確保會話會根據連接器的 IP 位址進行。 來源 IP 親和性不能用在其中。
以下是案例2的一些選項：

- 選項1：以負載平衡器所設定的會話 cookie 作為會話持續性的基礎。 建議使用此選項，因為它可讓負載在後端伺服器之間更平均地分散。 它需要具有這項功能的第7層負載平衡器，而且可以處理 HTTP 流量並終止 TLS 連線。 您可以使用 Azure 應用程式閘道 (會話親和性) 或來自另一個廠商的負載平衡器。

- 選項2：以 X 轉送的標頭欄位作為會話持續性的基礎。 此選項需要具有這項功能的第7層負載平衡器，而且可以處理 HTTP 流量並終止 TLS 連線。  

- 選項3：設定後端應用程式不需要會話持續性。

請參閱軟體廠商的檔，以瞭解後端應用程式的負載平衡需求。

## <a name="next-steps"></a>後續步驟

- [啟用應用程式 Proxy](application-proxy-add-on-premises-application.md)
- [啟用單一登入](application-proxy-configure-single-sign-on-with-kcd.md)
- [啟用條件式存取](application-proxy-integrate-with-sharepoint-server.md)
- [使用應用程式 Proxy 疑難排解您遇到的問題](application-proxy-troubleshoot.md)
- [瞭解 Azure AD 架構如何支援高可用性](../fundamentals/active-directory-architecture.md)
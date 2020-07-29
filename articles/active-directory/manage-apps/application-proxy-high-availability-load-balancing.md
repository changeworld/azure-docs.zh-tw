---
title: 高可用性和負載平衡-Azure AD 應用程式 Proxy
description: 流量散發如何與您的應用程式 Proxy 部署搭配運作。 包含如何將連接器效能優化，並針對後端伺服器使用負載平衡的秘訣。
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
ms.openlocfilehash: 5ab3b340654fd6d824edef0a33d1ea363a913654
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84764582"
---
# <a name="high-availability-and-load-balancing-of-your-application-proxy-connectors-and-applications"></a>應用程式 Proxy 連接器和應用程式的高可用性和負載平衡

本文說明流量散發如何與您的應用程式 Proxy 部署搭配運作。 我們會討論：

- 如何在使用者和連接器之間散佈流量，以及將連接器效能優化的秘訣

- 在連接器和後端應用程式伺服器之間的流量，以及在多個後端伺服器之間進行負載平衡的建議

## <a name="traffic-distribution-across-connectors"></a>跨連接器的流量散發

連接器會根據高可用性原則來建立其連線。 不保證流量一律會平均分散到不同的連接器，而且沒有任何會話親和性。 不過，使用量會有所不同，而且要求會隨機傳送至應用程式 Proxy 服務實例。 因此，流量通常會平均分散到各個連接器。 下列圖表和步驟說明如何在使用者和連接器之間建立連接。

![顯示使用者與連接器之間連接的圖表](media/application-proxy-high-availability-load-balancing/application-proxy-connections.png)

1. 用戶端裝置上的使用者嘗試存取透過應用程式 Proxy 發佈的內部部署應用程式。
2. 要求會通過 Azure Load Balancer 來判斷哪個應用程式 Proxy 服務實例應該接受要求。 每個區域都有數十個實例可接受要求。 這個方法有助於將流量平均分散到服務實例。
3. 要求會傳送至[服務匯流排](https://docs.microsoft.com/azure/service-bus-messaging/)。
4. 服務匯流排通知至可用的連接器。 然後，連接器會從服務匯流排拾取要求。
   - 在步驟2中，要求會移至不同的應用程式 Proxy 服務實例，因此可能會使用不同的連接器來建立連接。 因此，群組內幾乎會使用連接器。
5. 連接器會將要求傳遞給應用程式的後端伺服器。 然後，應用程式會將回應傳回給連接器。
6. 連接器會開啟連入要求的服務實例輸出連線，完成回應。 然後這個連接會立即關閉。 根據預設，每個連接器的限制為200個並行輸出連線。
7. 然後，回應會從服務實例傳回給用戶端。
8. 來自相同連接的後續要求會重複上述步驟。

應用程式通常會有許多資源，並在載入時開啟多個連接。 每個連線都會執行上述步驟，以配置給服務實例，如果連線先前尚未與連接器配對，請選取新的可用連接器。


## <a name="best-practices-for-high-availability-of-connectors"></a>連接器高可用性的最佳作法

- 因為流量會分散到不同的連接器以提供高可用性，所以連接器群組中一定要有至少兩個連接器。 建議三個連接器在連接器之間提供額外的緩衝區。 若要判斷您所需的正確連接器數目，請遵循容量規劃檔。

- 將連接器放在不同的輸出連線上，以避免發生單一失敗點。 如果連接器使用相同的輸出連線，連線的網路問題可能會影響使用該連線的所有連接器。

- 避免在連接到生產環境應用程式時強制重新開機連接器。 這麼做可能會對連接器間的流量分佈造成負面影響。 重新開機連接器會導致更多的連接器無法使用，並強制連接到剩餘的可用連接器。 結果就是一開始不平均使用連接器。

- 避免在連接器與 Azure 之間的輸出 TLS 通訊上進行所有形式的內嵌檢查。 這種類型的內嵌檢查會導致通訊流程降低。

- 請務必為您的連接器保留自動更新的執行狀態。 如果應用程式 Proxy 連接器更新程式服務正在執行，您的連接器會自動更新並接收最新升級的。 如果您在伺服器上沒有看到連接器更新程式服務，則需要重新安裝您的連接器以取得任何更新。

## <a name="traffic-flow-between-connectors-and-back-end-application-servers"></a>連接器和後端應用程式伺服器之間的流量

高可用性的另一個重要領域是連接器與後端伺服器之間的連線。 透過 Azure AD 應用程式 Proxy 發佈應用程式時，從使用者到應用程式的流量會流經三個躍點：

1. 使用者連接到 Azure 上的 Azure AD 應用程式 Proxy 服務公用端點。 連接是在用戶端的原始用戶端 IP 位址（公用）與應用程式 Proxy 端點的 IP 位址之間建立。
2. 應用程式 Proxy 連接器會從應用程式 Proxy 服務提取用戶端的 HTTP 要求。
3. 應用程式 Proxy 連接器會連接到目標應用程式。 連接器會使用自己的 IP 位址來建立連接。

![透過應用程式 Proxy 連接至應用程式的使用者圖表](media/application-proxy-high-availability-load-balancing/application-proxy-three-hops.png)

### <a name="x-forwarded-for-header-field-considerations"></a>X-轉送-適用于標頭欄位考慮
在某些情況下（例如，「審核」、「負載平衡」等），需要在內部部署環境中共用外部用戶端的原始 IP 位址。 為滿足需求，Azure AD 應用程式 Proxy 連接器會將具有原始用戶端 IP 位址（公用）的 [X 轉寄] 標頭欄位新增至 HTTP 要求。 適當的網路裝置（負載平衡器、防火牆）或 web 伺服器或後端應用程式可以讀取和使用資訊。

## <a name="best-practices-for-load-balancing-among-multiple-app-servers"></a>在多個應用程式伺服器之間進行負載平衡的最佳做法
當指派給應用程式 Proxy 應用程式的連接器群組有兩個以上的連接器，而且您在多部伺服器（伺服器陣列）上執行後端 web 應用程式時，需要有良好的負載平衡策略。 良好的策略可確保伺服器會平均取得用戶端要求，並防止伺服器陣列中的伺服器過度使用或使用量過低。
### <a name="scenario-1-back-end-application-does-not-require-session-persistence"></a>案例1：後端應用程式不需要會話持續性
最簡單的案例是後端 web 應用程式不需要會話的持續性（會話持續性）。 任何來自使用者的要求都可以由伺服器陣列中的任何後端應用程式實例處理。 您可以使用第4層負載平衡器，並將它設定為沒有親和性。 有些選項包括 Microsoft 網路負載平衡和 Azure Load Balancer，或是來自另一個廠商的負載平衡器。 或者，也可以設定迴圈配置資源 DNS。
### <a name="scenario-2-back-end-application-requires-session-persistence"></a>案例2：後端應用程式需要會話持續性
在此案例中，後端 web 應用程式在經過驗證的會話期間，需要會話（會話持續性）。 來自使用者的所有要求都必須由在伺服器陣列中的相同伺服器上執行的後端應用程式實例來處理。
此案例可能會更複雜，因為用戶端通常會建立與應用程式 Proxy 服務的多個連接。 不同連接的要求可能會抵達伺服器陣列中的不同連接器和伺服器。 由於每個連接器都會使用自己的 IP 位址來進行這項通訊，因此負載平衡器無法根據連接器的 IP 位址來確保會話無法對應。 來源 IP 親和性無法使用。
以下是案例2的一些選項：

- 選項1：根據負載平衡器所設定的會話 cookie，以會話持續性為基礎。 建議使用此選項，因為它可讓負載在後端伺服器之間平均分配。 它需要具有這項功能的第7層負載平衡器，而且可以處理 HTTP 流量並終止 TLS 連線。 您可以使用 Azure 應用程式閘道（會話親和性），或來自另一個廠商的負載平衡器。

- 選項2：以 X 轉送的標頭欄位作為會話持續性的基礎。 此選項需要具有這項功能的第7層負載平衡器，而且可以處理 HTTP 流量並終止 TLS 連線。  

- 選項3：將後端應用程式設定為不需要會話持續性。

請參閱軟體廠商的檔，以瞭解後端應用程式的負載平衡需求。

## <a name="next-steps"></a>後續步驟

- [啟用應用程式 Proxy](application-proxy-add-on-premises-application.md)
- [啟用單一登入](application-proxy-configure-single-sign-on-with-kcd.md)
- [啟用條件式存取](application-proxy-integrate-with-sharepoint-server.md)
- [使用應用程式 Proxy 疑難排解您遇到的問題](application-proxy-troubleshoot.md)
- [瞭解 Azure AD 架構如何支援高可用性](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-architecture)

---
title: Azure DDoS 保護標準總覽
description: 瞭解 Azure DDoS 保護 Standard 與應用程式設計最佳作法的結合，如何提供對 DDoS 攻擊的防禦。
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/22/2020
ms.author: kumud
ms.openlocfilehash: 50b0324be8da8024dcbda140eff062f3ff712225
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91371179"
---
# <a name="azure-ddos-protection-standard-overview"></a>Azure DDoS Protection Standard 概觀

分散式阻斷服務 (DDoS) 攻擊是將應用程式移至雲端的客戶所面臨的最大可用性和安全性顧慮之一。 DDoS 攻擊會嘗試耗盡應用程式的資源，讓合法使用者無法使用該應用程式。 DDoS 攻擊可以鎖定可透過網際網路公開觸達的任何端點。

結合應用程式設計最佳做法的 Azure DDoS 保護可提供 DDoS 攻擊的防禦。 Azure 中的每個屬性都會受到 Azure 基礎結構 DDoS (基本) 保護的保護。Azure DDoS 保護標準針對基本服務層級提供額外的緩和功能，專為 Azure 虛擬網路資源進行微調。 

DDoS 保護「標準」層級很容易啟用，而且不需要變更應用程式。 保護原則會透過專用的流量監視和機器學習演算法進行調整。 原則會套用至與虛擬網路中所部署資源相關聯的公用 IP 位址，例如 Azure Load Balancer、Azure 應用程式閘道和 Azure Service Fabric 執行個體，但是此保護功能不適用於 App Service Environments。在攻擊期間，可透過 Azure 監視器檢視取得即時遙測與歷史記錄。 透過診斷設定可以獲得豐富的攻擊風險降低分析。 透過 [Azure 應用程式閘道 Web 應用程式防火牆](../application-gateway//application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)，或者從 Azure Marketplace 安裝第 3 方防火牆，可以加上應用程式層保護。 針對 IPv4 和 IPv6 Azure [公用 IP 位址](virtual-network-public-ip-address.md)提供保護。

![DDoS Protection 基本與標準](./media/ddos-protection-overview/ddoscomparison.png)

Azure DDoS 保護不會儲存客戶資料。

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>DDoS Protection Standard 可降低風險的 DDoS 攻擊類性

DDoS 保護標準層可降低下列攻擊類型的風險：

- **體積型攻擊**：這些攻擊會充斥大量看似合法流量的網路層。 其中包括 UDP 洪水、放大氾濫，以及其他詐騙封包氾濫。 DDoS Protection Standard 可透過 Azure 的全球網路規模自動吸收和清除這些潛在的多重 gb 攻擊，來降低這些攻擊。
- **通訊協定攻擊**：這些攻擊透過利用第 3 層和第 4 層通訊協定堆疊中的弱點，讓目標無法供存取。 它們包含 SYN 洪水攻擊、反映攻擊和其他通訊協定攻擊。 DDoS 保護標準層可透過與用戶端互動來區別惡意與合法流量並封鎖惡意流量，以降低這些攻擊的風險。 
- **資源 (應用程式) 層攻擊**：這些攻擊會鎖定 Web 應用程式封包，以中斷主機之間的資料傳輸。 其中包括 HTTP 通訊協定違規、SQL 插入、跨網站腳本和其他第7層攻擊。 使用 Web 應用程式防火牆，例如 Azure [應用程式閘道 Web 應用程式防火牆](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)，以及 DDoS Protection Standard，以提供這些攻擊的防禦。 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall) 還提供了協力廠商 Web 應用程式防火牆供應項目。

DDoS Protection Standard 可保護虛擬網路中的資源，包括與虛擬機器相關聯的公用 IP 位址、內部負載平衡器，以及應用程式閘道。 當與應用程式閘道 web 應用程式防火牆搭配使用，或在具有公用 IP 的虛擬網路中部署協力廠商 web 應用程式防火牆時，DDoS 保護標準可提供完整的第3層至第7層風險降低功能。

## <a name="ddos-protection-standard-features"></a>DDoS Protection Standard 功能

![DDoS 功能](./media/ddos-protection-overview/ddosfeatures.png)

DDoS Protection Standard 功能包括：

- **原生平台整合**：原生整合到 Azure。 包括透過 Azure 入口網站進行設定。 DDoS Protection Standard 了解您的資源和資源組態。
- 通行**保護：** 簡化的設定會在啟用 DDoS 保護標準時立即保護虛擬網路上的所有資源。 不需要任何介入或使用者定義。 一旦偵測到攻擊，DDoS Protection Standard 就會立即自動減輕攻擊。
- **Always on 流量監視：** 您的應用程式流量模式會在一天24小時內（一周7天）進行監視，以尋找 DDoS 攻擊的指標。 超出保護原則時，就會執行安全防護功能。
- **適應性調整：** 智慧型流量分析可在一段時間內學習您的應用程式流量，並選取及更新最適合您服務的設定檔。 設定檔會隨著時間調整流量變更。
- **多層保護：** 搭配 Web 應用程式防火牆使用時，可提供完整堆疊 DDoS 保護。
- **廣泛的安全防護範圍：** 可利用全域功能降低超過 60 種不同攻擊類型的風險，以抵禦最大的已知 DDoS 攻擊。
- **攻擊分析：** 在攻擊期間取得以五分鐘為增量的詳細報告，並在攻擊結束後取得完整摘要。 將風險降低流程記錄串流至 [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-ddos-protection) 或離線安全性資訊與事件管理 (SIEM) 系統，以在攻擊期間進行近乎即時的監視。
- **攻擊計量：** 透過 Azure 監視器可以存取每個攻擊的摘要計量。
- **攻擊警示：** 您可以使用內建攻擊計量，在開始和停止攻擊時設定警示，並在攻擊的持續時間內設定警示。 警示會整合到您的操作軟體，例如 Microsoft Azure 監視器記錄、Splunk、Azure 儲存體、電子郵件和 Azure 入口網站。
- **成本保證：** 資料傳輸和應用程式向外延展服務點數，適用于記載的 DDoS 攻擊。

## <a name="ddos-protection-standard-mitigation"></a>DDoS Protection Standard 安全防護功能

DDoS 保護標準層會監視實際流量使用率，且時常將它與 DDoS 原則中定義的閾值比較。 當超過該流量閾值時，就會自動起始 DDoS 安全防護功能。 當傳回流量低於閾值時，就會移除安全防護功能。

![下圖顯示透過 DDoS 保護標準從客戶透過 Azure 入口網站開始的流程，透過從兩個公用 I P 位址結束的原則產生來透過公用 I P 位址。](./media/ddos-protection-overview/mitigation.png)

在安全防護期間，DDoS 保護服務會重新導向傳送至受保護資源的流量，而且會執行數個檢查，例如下列檢查：

- 確定封包符合網際網路規格，且格式正確無誤。
- 與用戶端互動，判斷流量是否可能是詐騙封包 (例如： SYN Auth 或 SYN Cookie 或置放封包以供來源將它重新傳輸)。
- 速率限制封包 (如果沒有其他強制方法可以執行)。

DDoS 保護會封鎖攻擊流量並將剩餘流量轉送至其預定目的地。 在偵測到攻擊的幾分鐘內，系統會使用 Azure 監視器計量通知您。 藉由設定登入 DDoS Protection Standard 遙測，您可以將記錄寫入至可用的選項，以供日後分析。 適用於 DDoS 保護標準層的 Azure 監視器中的計量資料會保留 30 天。

Microsoft 已與 [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) 合作建立一個介面，您可以針對啟用 DDoS 保護的公用 IP 位址產生流量，以進行模擬。 BreakPoint Cloud 模擬可讓您：

- 驗證 Microsoft Azure DDoS 保護標準層如何保護您的 Azure資源免受 DDoS 攻擊
- 將遭受 DDoS 攻擊時的事件回應程序最佳化
- 記載 DDoS 合規性
- 訓練您的網路安全性小組

## <a name="next-steps"></a>後續步驟

- [設定 DDoS 保護標準層](manage-ddos-protection.md)
- [Azure DDoS 保護定價](https://azure.microsoft.com/pricing/details/ddos-protection/)

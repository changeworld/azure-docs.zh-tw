---
title: Azure DDoS 保護功能
description: 瞭解 Azure DDoS 保護功能
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: b3f903b69cebd22e870f7ccd5923e6f08455dff3
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94992381"
---
# <a name="azure-ddos-protection-standard-features"></a>Azure DDoS 保護標準功能

下列各節會概略說明標準 Azure DDoS 保護服務的主要功能。

## <a name="always-on-traffic-monitoring"></a>Always on 流量監視

DDoS 保護標準層會監視實際流量使用率，且時常將它與 DDoS 原則中定義的閾值比較。 當超過該流量閾值時，就會自動起始 DDoS 安全防護功能。 當傳回流量低於閾值時，就會移除安全防護功能。

![Azure DDoS 保護標準緩和措施](./media/ddos-protection-overview/mitigation.png)

在安全防護期間，DDoS 保護服務會重新導向傳送至受保護資源的流量，而且會執行數個檢查，例如下列檢查：

- 確定封包符合網際網路規格，且格式正確無誤。
- 與用戶端互動，判斷流量是否可能是詐騙封包 (例如： SYN Auth 或 SYN Cookie 或置放封包以供來源將它重新傳輸)。
- 速率限制封包 (如果沒有其他強制方法可以執行)。

DDoS 保護會封鎖攻擊流量並將剩餘流量轉送至其預定目的地。 在偵測到攻擊的幾分鐘內，系統會使用 Azure 監視器計量通知您。 藉由設定登入 DDoS Protection Standard 遙測，您可以將記錄寫入至可用的選項，以供日後分析。 適用於 DDoS 保護標準層的 Azure 監視器中的計量資料會保留 30 天。

## <a name="adaptive-real-time-tuning"></a>適應性即時微調

Azure 基本 DDoS 保護服務可協助保護客戶，並防止對其他客戶產生影響。 例如，如果合法連入流量的正常流量小於涵蓋整個基礎結構範圍的 DDoS 保護原則 *觸發率*，而且服務是針對該流量所佈建，則可能不會注意到對於該客戶資源的 DDoS 攻擊。 更普遍的說法是，最新攻擊 (例如多向量 DDoS) 的複雜本質，以及租用戶的應用程式專屬行為，會呼叫針對每位客戶自訂的保護原則。 服務會使用兩種見解來完成這項自訂：

- 自動學習每位客戶 (每個 IP) 的第 3 層與第 4 層流量模式。

- 考慮到 Azure 的規模會使之吸收大量的流量，進而降低誤判。

![標準 DDoS 保護運作方式圖解，圈起「原則產生」](./media/ddos-best-practices/image-5.png)

## <a name="ddos-protection-telemetry-monitoring-and-alerting"></a>DDoS 保護遙測、監視及警示

標準 DDoS 保護在遭受 DDoS 攻擊持續期間，透過 [Azure 監視器](../azure-monitor/overview.md)公開豐富的遙測。 可設定 DDoS 保護所用的任何 Azure 監視器度計量的警示。 您可以整合記錄與 Splunk (Azure 事件中樞) 、Azure 監視器記錄和 Azure 儲存體，以透過 Azure 監視器診斷介面進行先進的分析。

### <a name="ddos-mitigation-policies"></a>DDoS 防護原則

在 [Azure 入口網站中，選取 [**監視**  >  **度量**]。 在 [計量] 窗格中，選取資源群組、選取 [公用 IP 位址] 的資源類型，並選取您的 Azure 公用 IP 位址。 DDoS 計量會顯示在 [可用計量] 窗格中。

「標準 DDoS 保護」會在啟用 DDoS 的虛擬網路中，對受保護資源的每個公用 IP 套用三個自動調整的風險降低原則 (TCP SYN、TCP、UDP)。 可選取 **要觸發 DDoS 防護的輸入封包** 計量，藉此檢視原則閾值。

![可用的計量與計量圖表](./media/ddos-best-practices/image-7.png)

原則閾值會透過機器學習架構的網路流量分析進行自動設定。 只有在超出原則閾值時，才會針對遭受攻擊的 IP 位址進行 DDoS 防護。

### <a name="metric-for-an-ip-address-under-ddos-attack"></a>遭受 DDoS 攻擊的 IP 位址計量

如果公用 IP 位址遭受攻擊，則當 DDoS 保護在降低攻擊流量的風險時，[是否正遭受 DDoS 攻擊] 的計量值會變更為 1。

![「是否正遭受 DDoS 攻擊」計量與圖表](./media/ddos-best-practices/image-8.png)

建議您針對此計量設定警示。 之後當公用 IP 位址上正在執行 DDoS 風險降低作業時，您便會收到通知。

如需詳細資訊，請參閱[使用 Azure 入口網站管理 Azure DDoS 保護標準](manage-ddos-protection.md)。

## <a name="web-application-firewall-for-resource-attacks"></a>資源攻擊的 Web 應用程式防火牆

針對應用程式層上的資源攻擊，應該設定 Web 應用程式防火牆 (WAF) 來協助維護 Web 應用程式的安全。 WAF 會檢查傳入的 Web 流量，以封鎖 SQL 插入式攻擊、跨網站指令碼攻擊、DDoS 攻擊，以及其他第 7 層攻擊。 Azure 提供 [WAF 作為一項應用程式閘道功能](../web-application-firewall/ag/ag-overview.md)，用來進行 Web 應用程式的集中式保護，免於遭遇常見的攻擊和弱點。 透過 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1)，您可以取得由 Azure 夥伴所提供且可能更適合您需求的其他 WAF 供應項目。

即使是 Web 應用程式防火牆，也很容易受到體積型與狀態耗盡攻擊。 強烈建議您在 WAF 虛擬網路上啟用標準 DDoS 保護，協助防禦體積型與通訊協定攻擊。 如需詳細資訊，請參閱 [DDoS 保護參考架構](ddos-protection-reference-architectures.md)一節。

## <a name="protection-planning"></a>保護規劃

規劃與準備對於了解在發生 DDoS 攻擊時系統將如何執行，至關重要。 設計事件管理回應方案是這項工作的一部分。

如果您有標準 DDoS 保護，請務必在網際網路對向端點的虛擬網路上將之啟用。 設定 DDoS 警示可幫助您持續監看基礎結構上出現的任何潛在攻擊。 

獨立監視您的應用程式。 並了解應用程式的正常行為。 如果應用程式在遭受 DDoS 攻擊時並未如預期般運作，請準備好採取行動。 

[透過模擬測試](test-through-simulations.md)來瞭解您的服務將如何回應攻擊。

## <a name="next-steps"></a>下一步

- 瞭解如何 [建立 DDoS 保護計劃](manage-ddos-protection.md)。
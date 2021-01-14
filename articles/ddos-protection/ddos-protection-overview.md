---
title: Azure DDoS 保護標準總覽
description: 了解 Azure DDoS 保護標準與應用程式設計最佳做法結合後，如何提供對 DDoS 攻擊的防禦。
services: virtual-network
documentationcenter: na
author: yitoh
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/9/2020
ms.author: yitoh
ms.openlocfilehash: e76086db36642664ca15acee26f4e0aca85ff272
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98184454"
---
# <a name="azure-ddos-protection-standard-overview"></a>Azure DDoS Protection Standard 概觀

分散式阻斷服務 (DDoS) 攻擊是將應用程式移至雲端的客戶所面臨的最大可用性和安全性顧慮之一。 DDoS 攻擊會嘗試耗盡應用程式的資源，讓合法使用者無法使用該應用程式。 DDoS 攻擊可以鎖定可透過網際網路公開觸達的任何端點。

Azure 中的每個屬性都會受到 Azure 基礎結構 DDoS (基本) 保護的保護，不需額外費用。 Azure 全球部署網路的規模與能力可提供防禦，透過一律啟動的流量監視和即時緩和，來抵禦常見的網路層攻擊。 基本 DDoS 保護不需要進行任何使用者設定或應用程式變更。 基本 DDoS 保護可協助保護所有的 Azure 服務 (包括像是 Azure DNS 的 PaaS 服務)。

Azure DDoS 保護 Standard （結合應用程式設計最佳作法）提供增強的 DDoS 風險降低功能，以防禦 DDoS 攻擊。 它會自動調整以協助保護您在虛擬網路中的特定 Azure 資源。 只需在任何新的或現有的虛擬網路上啟用保護，而不需進行任何應用程式或資源變更。 它具有數個優於基本服務的優點，包括記錄、警示與遙測。 

![Azure DDoS 保護服務比較](./media/ddos-protection-overview/ddos-comparison.png)

Azure DDoS 保護不會儲存客戶資料。

## <a name="features"></a>功能

- **原生平台整合**：原生整合到 Azure。 包括透過 Azure 入口網站進行設定。 DDoS Protection Standard 了解您的資源和資源組態。
- 通行 **保護：** 簡化的設定會在啟用 DDoS 保護標準時立即保護虛擬網路上的所有資源。 不需要任何介入或使用者定義。 
- **Always on 流量監視：** 您的應用程式流量模式會在一天24小時內（一周7天）進行監視，以尋找 DDoS 攻擊的指標。 一旦偵測到攻擊，DDoS Protection Standard 就會立即自動減輕攻擊。
- **適應性調整：** 智慧型流量分析可在一段時間內學習您的應用程式流量，並選取及更新最適合您服務的設定檔。 設定檔會隨著時間調整流量變更。
- **多層保護：** 搭配 web 應用程式防火牆使用時，提供完整的堆疊 DDoS 保護，以在 (第3層和第4層的網路層取得保護，這是由 Azure DDoS 保護標準) 以及 WAF) 提供的應用層 (第7層提供。 WAF 供應專案包括 Azure [應用程式閘道 WAF SKU](../web-application-firewall/ag/ag-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ，以及 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall)中提供的協力廠商 web 應用程式防火牆供應專案。
- **廣泛的安全防護範圍：** 可利用全域功能降低超過 60 種不同攻擊類型的風險，以抵禦最大的已知 DDoS 攻擊。
- **攻擊分析：** 在攻擊期間取得以五分鐘為增量的詳細報告，並在攻擊結束後取得完整摘要。 將風險降低流程記錄串流至 [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md) 或離線安全性資訊與事件管理 (SIEM) 系統，以在攻擊期間進行近乎即時的監視。
- **攻擊計量：** 透過 Azure 監視器可以存取每個攻擊的摘要計量。
- **攻擊警示：** 您可以使用內建攻擊計量，在開始和停止攻擊時設定警示，並在攻擊的持續時間內設定警示。 警示會整合到您的操作軟體，例如 Microsoft Azure 監視器記錄、Splunk、Azure 儲存體、電子郵件和 Azure 入口網站。
- **Ddos 快速回應**：參與 Ddos Protection 快速回應 (DRR) 團隊，協助進行攻擊調查和分析。 若要深入瞭解，請參閱 [DDoS 快速回應](ddos-rapid-response.md)。
- **成本保證：** 針對因記載的 DDoS 攻擊而產生的資源成本，接收資料傳輸和應用程式相應放大服務點數。

## <a name="pricing"></a>定價

DDoS 保護計劃每月固定收取每月 $2944 的費用，最多可涵蓋100個公用 IP 位址。 針對額外資源的保護，每個月每個資源會額外增加 $30 的費用。

在租使用者下，單一的 DDoS 保護計劃可以跨多個訂用帳戶使用，因此不需要建立一個以上的 DDoS 保護計劃。

若要瞭解 Azure DDoS 保護標準定價，請參閱 [Azure DDoS 保護標準定價](https://azure.microsoft.com/pricing/details/ddos-protection/)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立 DDoS 保護計劃](manage-ddos-protection.md)

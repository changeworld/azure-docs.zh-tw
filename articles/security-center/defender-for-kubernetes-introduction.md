---
title: 適用於 Kubernetes 的 Azure Defender - 優點和功能
description: 了解適用於 Kubernetes 的 Azure Defender 有何優點和功能。
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: dd92e1529b889671bc29939f7e9611eceac7ee20
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92370503"
---
# <a name="introduction-to-azure-defender-for-kubernetes"></a>適用於 Kubernetes 的 Azure Defender 簡介

Azure Kubernetes Service (AKS) 是 Microsoft 的受控服務，可用來開發、部署和管理容器化應用程式。

Azure 資訊安全中心和 AKS 形成了最佳的雲端原生 Kubernetes 安全性供應項目，兩者的搭配運作可提供環境強化、工作負載保護和執行階段保護，如下所述。

若要對您的 Kubernetes 叢集進行威脅偵測，請啟用 **適用於 Kubernetes 的 Azure Defender** 。

啟用[適用於伺服器的 Azure Defender](defender-for-servers-introduction.md) 時，則可對 Linux AKS 節點進行主機層級的威脅偵測。

## <a name="availability"></a>可用性

|層面|詳細資料|
|----|:----|
|版本狀態：|正式上市 (GA)|
|定價：|**適用於 Kubernetes 的 Azure Defender** 的計費方式如 [定價頁面](security-center-pricing.md)所示|
|必要的角色和權限：|**安全性管理員** 可以關閉警示。<br>**安全性讀取者** 可以查看結果。|
|雲端：|![是](./media/icons/yes-icon.png) 商業雲端<br>![否](./media/icons/no-icon.png) 國家/地區/主權 (US Gov、中國 Gov、其他 Gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-kubernetes"></a>適用於 Kubernetes 的 Azure Defender 有哪些優點？

### <a name="run-time-protection"></a>執行階段保護

藉由持續分析下列 AKS 來源，資訊安全中心可針對您的容器化環境提供即時威脅防護，並針對在主機 *和* AKS 叢集層級上偵測到的威脅與惡意活動產生警示。 您可以使用這項資訊來快速修復安全性問題，並改善您容器的安全性。

資訊安全中心提供不同層級的威脅防護： 

- **主機層級 (Azure Defender 針對伺服器提供的)** - 使用資訊安全中心在其他 VM 上使用的同一個 Log Analytics 代理程式，Azure Defender 監視您的 Linux AKS 節點是否有可疑的動作，例如，Web 殼層偵測以及與已知可疑 IP 位址的連線。 代理程式也會監視是否有容器特定分析，例如，建立具特殊權限的容器、對 API 伺服器進行可疑的存取，以及在 Docker 容器內執行安全殼層 (SSH) 伺服器。

    如需 AKS 主機層級警示的清單，請參閱[警示的參考資料表](alerts-reference.md#alerts-containerhost)。

    >[!IMPORTANT]
    > 如果您選擇不在主機上安裝此代理程式，則只會收到部分的威脅防護權益和安全性警示。 您仍會收到與網路分析以及與惡意伺服器通訊有關的警示。

- **AKS 叢集層級 (由 Azure Defender 針對 Kubernetes 提供的)** - 在叢集層級，威脅防護是以分析 Kubernetes 的稽核記錄為基礎。 若要啟用此 **無代理程式** 監視，請啟用 Azure Defender。 為了在此層級產生警示，資訊安全中心會使用 AKS 所擷取的記錄來監視受 AKS 管理的服務。 此層級的事件範例包括公開 Kubernetes 儀表板、建立高特殊權限的角色，以及建立敏感性裝載。

    如需 AKS 叢集層級警示的清單，請參閱[警示的參考資料表](alerts-reference.md#alerts-akscluster)。

    >[!NOTE]
    > 資訊安全中心會針對在訂用帳戶設定上啟用 Kubernetes 選項後所發生的 Azure Kubernetes Service 動作和部署產生安全性警示。 

此外，我們的全球安全性研究小組會持續監視威脅的態勢。 他們會在發現容器特有的警示和弱點時加以新增。

> [!TIP]
> 您可以依照[此部落格文章](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270)中的指示來模擬容器警示。



## <a name="how-does-security-centers-kubernetes-protection-work"></a>資訊安全中心的 Kubernetes 保護如何運作？

下圖概略說明 Azure 資訊安全中心、Azure Kubernetes Service 與 Azure 原則之間的互動。

您可以看到，資訊安全中心所接收和分析的項目包括：

- 來自 API 伺服器的稽核記錄
- 來自 Log Analytics 代理程式的原始安全性事件
- 來自 AKS 叢集的叢集設定資訊
- Azure 原則中的工作負載設定 (透過 **適用於 Kubernetes的 Azure 原則附加元件** )。 [深入了解使用 Kubernetes 許可控制保護工作負載的最佳做法](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)

:::image type="content" source="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png" alt-text="Azure 資訊安全中心、Azure Kubernetes Service 與 Azure 原則互動方式的高階架構" lightbox="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png":::




## <a name="azure-defender-for-kubernetes---faq"></a>適用於 Kubernetes 的 Azure Defender - 常見問題集

### <a name="can-i-still-get-aks-protections-without-the-log-analytics-agent"></a>如果沒有 Log Analytics 代理程式，仍然可以獲得 AKS 保護嗎？

如前所述， **適用於 Kubernetes 的 Azure Defender** 方案會提供叢集層級的保護，而 **適用於伺服器的 Azure Defender** 的 Log Analytics 代理程式則會保護您的節點。 

建議您盡可能兩者都部署，以獲得最完整的保護。

如果您選擇不在主機上安裝此代理程式，則只會收到部分的威脅防護權益和安全性警示。 您仍會收到與網路分析以及與惡意伺服器通訊有關的警示。


### <a name="does-aks-allow-me-to-install-custom-vm-extensions-on-my-aks-nodes"></a>AKS 是否允許我在我的 AKS 節點上安裝自訂 VM 擴充功能？

若要讓 Azure Defender 監視您的 AKS 節點，這些節點必須執行 Log Analytics 代理程式。 

AKS 是受控服務，而 Log Analytics 代理程式是 Microsoft 管理的擴充功能，因此在 AKS 叢集上也可受到支援。



### <a name="if-my-cluster-is-already-running-an-azure-monitor-for-containers-agent-do-i-need-the-log-analytics-agent-too"></a>如果我的叢集已執行適用於容器代理程式的 Azure 監視器，是否還需要 Log Analytics 代理程式？

若要讓 Azure Defender 監視您的 AKS 節點，這些節點必須執行 Log Analytics 代理程式。

如果您的叢集已執行適用於容器代理程式的 Azure 監視器，您也可以安裝 Log Analytics 代理程式，而且兩個代理程式可以彼此搭配運作，而不會有任何問題。

[深入了解適用於容器代理程式的 Azure 監視器](../azure-monitor/insights/container-insights-manage-agent.md)。


## <a name="next-steps"></a>後續步驟

在本文中，您已了解資訊安全中心的 Kubernetes 保護，包括適用於 Kubernetes 的 Azure Defender。 

如需相關內容，請參閱下列文章： 

- [啟用 Azure Defender](security-center-pricing.md)
- [將警示匯出至 Azure Sentinel 或第三方 SIEM](continuous-export.md)
- [警示的參考資料表](alerts-reference.md)
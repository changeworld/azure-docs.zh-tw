---
title: 適用于 Kubernetes 的 Azure Defender-優點和功能
description: 瞭解 Azure Defender for Kubernetes 的優點和功能。
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: a7fb97f1036515f82fa3e61c18f95eadf0af704d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91301698"
---
# <a name="introduction-to-azure-defender-for-kubernetes"></a>Azure Defender for Kubernetes 簡介

Azure Kubernetes Service (AKS) 是 Microsoft 用來開發、部署和管理容器化應用程式的受控服務。

Azure 資訊安全中心和 AKS 形成最佳的雲端原生 Kubernetes 安全性供應專案，同時提供環境強化、工作負載保護和執行時間保護，如下所述。

針對 Kubernetes 叢集的威脅偵測，請啟用 **Azure Defender For Kubernetes**。

如果您啟用 [適用于伺服器的 Azure Defender](defender-for-servers-introduction.md)，則可以使用適用于 Linux AKS 節點的主機層級威脅偵測。

## <a name="availability"></a>可用性

|層面|詳細資料|
|----|:----|
|釋放狀態：|正式上市 (GA)|
|定價：|**適用于 Kubernetes 的 Azure Defender**會依[定價頁面上的](security-center-pricing.md)顯示計費|
|必要的角色和許可權：|**安全性系統管理員** 可以關閉警示。<br>**安全性讀取者**可以查看結果。|
|雲端：|![是](./media/icons/yes-icon.png) 商業雲端<br>![否](./media/icons/no-icon.png) 全國/主權 (US Gov、中國 Gov、其他 Gov) |
|||

## <a name="what-are-the-benefits-of-azure-defender-for-kubernetes"></a>適用于 Kubernetes 的 Azure Defender 有哪些優點？

### <a name="run-time-protection"></a>執行時間保護

透過連續分析下列 AKS 來源，安全中心會為您的容器化環境提供即時威脅防護，並產生在主機 *和* AKS 叢集層級偵測到的威脅和惡意活動警示。 您可以使用這項資訊來快速修復安全性問題，並改善您容器的安全性。

資訊安全中心提供不同層級的威脅防護： 

- **適用于伺服器的 Azure defender 所提供的主機層級 () ** -使用資訊安全中心在其他 vm 上使用的相同 Log Analytics 代理程式，Azure Defender 會監視您的 Linux AKS 節點是否有可疑的活動，例如 web shell 偵測和與已知可疑 IP 位址的連接。 代理程式也會監視容器特定的分析，例如特殊許可權的容器建立、對 API 伺服器的可疑存取，以及在 Docker 容器內執行 (SSH) 伺服器的安全殼層。

    如需 AKS 主機層級警示的清單，請參閱 [警示的參考表](alerts-reference.md#alerts-containerhost)。

    >[!IMPORTANT]
    > 如果您選擇不在主機上安裝此代理程式，則只會收到部分的威脅防護權益和安全性警示。 您仍會收到與網路分析以及與惡意伺服器通訊有關的警示。

- **Azure Defender For Kubernetes) 所提供的 AKS 叢集層級 (** -在叢集層級，威脅防護是以分析 Kubernetes 的 audit 記錄為基礎。 若要啟用此 **無代理** 程式監視，請啟用 Azure Defender。 為了在此層級產生警示，資訊安全中心會使用 AKS 所擷取的記錄來監視受 AKS 管理的服務。 此層級的事件範例包括公開 Kubernetes 儀表板、建立高特殊權限的角色，以及建立敏感性裝載。

    如需 AKS 叢集層級警示的清單，請參閱[警示的參考資料表](alerts-reference.md#alerts-akscluster)。

    >[!NOTE]
    > 資訊安全中心會針對在訂用帳戶設定上啟用 Kubernetes 選項後所發生的 Azure Kubernetes Service 動作和部署產生安全性警示。 

此外，我們的全球安全性研究小組會持續監視威脅的態勢。 他們會在發現容器特有的警示和弱點時加以新增。

> [!TIP]
> 您可以依照[此部落格文章](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270)中的指示來模擬容器警示。



## <a name="how-does-security-centers-kubernetes-protection-work"></a>資訊安全中心的 Kubernetes 保護如何運作？

以下是 Azure 資訊安全中心、Azure Kubernetes Service 和 Azure 原則之間互動的高階圖表。

您可以看到安全性中心所接收及分析的專案包含：

- 從 API 伺服器審核記錄
- Log Analytics 代理程式中的原始安全性事件
- AKS 叢集中的叢集設定資訊
- Azure 原則 (透過 **適用于 Kubernetes) 的 Azure 原則附加元件進行** 的工作負載設定。 [深入瞭解使用 Kubernetes 許可控制的工作負載保護最佳做法](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)

:::image type="content" source="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png" alt-text="Azure 資訊安全中心、Azure Kubernetes Service 和 Azure 原則之間互動的高層級架構" lightbox="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png":::




## <a name="azure-defender-for-kubernetes---faq"></a>適用于 Kubernetes 的 Azure Defender-常見問題

### <a name="can-i-still-get-aks-protections-without-the-log-analytics-agent"></a>沒有 Log Analytics 代理程式也可以取得 AKS 保護嗎？

如先前所述，選擇性的 **Azure defender For Kubernetes** 方案提供叢集層級的保護， **azure defender For Servers** 的 Log Analytics 代理程式可保護您的節點。 

建議您同時部署這兩種功能，以獲得最完整的保護。

如果您選擇不在主機上安裝代理程式，則只會收到威脅防護權益和安全性警示的子集。 您仍會收到與網路分析以及與惡意伺服器通訊有關的警示。


## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解資訊安全中心的 Kubernetes 保護，包括適用于 Kubernetes 的 Azure Defender。 

如需相關內容，請參閱下列文章： 

- [啟用 Azure Defender](security-center-pricing.md)
- [將警示匯出至 Azure Sentinel 或協力廠商 SIEM](continuous-export.md)
- [警示的參考資料表](alerts-reference.md)
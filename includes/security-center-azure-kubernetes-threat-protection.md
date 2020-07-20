---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 06/30/2020
ms.topic: include
ms.openlocfilehash: ba695337a1da1e440895f6b9f6fe2eb34f5041e3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85800594"
---
資訊安全中心可為您的容器化環境提供即時威脅防護，並產生可疑活動的警示。 您可以使用這項資訊來快速修復安全性問題，並改善您容器的安全性。

資訊安全中心提供不同層級的威脅防護： 

* **主機層級**-Log Analytics 代理程式會監視 Linux 是否有可疑的活動。 此代理程式會針對源自節點或執行於其上之容器的可疑活動觸發警示。 這類活動的範例包括 Web 殼層偵測，以及與已知可疑 IP 位址的連線。

    為了深入了解容器化環境的安全性，代理程式會監視容器特有的分析。 其會針對多種事件觸發警示，例如，建立具特殊權限的容器、對 API 伺服器進行可疑的存取，以及在 Docker 容器內執行安全殼層 (SSH) 伺服器。

    >[!IMPORTANT]
    > 如果您選擇不在主機上安裝此代理程式，則只會收到部分的威脅防護權益和安全性警示。 您仍會收到與網路分析以及與惡意伺服器通訊有關的警示。

    如需 AKS 主機層級警示的清單，請參閱[警示的參考資料表](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-containerhost)。


* 在 **AKS 叢集層級**，威脅防護立基於對 Kubernetes 稽核記錄的分析。 若要啟用此**無代理程式**監視，請從 [定價和設定] 頁面將 Kubernetes 選項新增至您的訂用帳戶 (請參閱[定價](https://docs.microsoft.com/azure/security-center/security-center-pricing))。 為了在此層級產生警示，資訊安全中心會使用 AKS 所擷取的記錄來監視受 AKS 管理的服務。 此層級的事件範例包括公開 Kubernetes 儀表板、建立高特殊權限的角色，以及建立敏感性裝載。

    >[!NOTE]
    > 資訊安全中心會針對在訂用帳戶設定上啟用 Kubernetes 選項後所發生的 Azure Kubernetes Service 動作和部署產生安全性警示。 

    如需 AKS 叢集層級警示的清單，請參閱[警示的參考資料表](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)。

此外，我們的全球安全性研究小組會持續監視威脅的態勢。 他們會在發現容器特有的警示和弱點時加以新增。

> [!TIP]
> 您可以依照[此部落格文章](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270)中的指示來模擬容器警示。
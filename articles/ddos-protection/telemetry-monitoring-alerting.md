---
title: 檢視和設定 DDoS 保護遙測
description: 瞭解如何查看和設定 DDoS 保護遙測。
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
ms.openlocfilehash: eefb658c689128c1d91858ac906c09e71d05cda6
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2020
ms.locfileid: "94888941"
---
# <a name="view-and-configure-ddos-protection-telemetry"></a>檢視和設定 DDoS 保護遙測

Azure DDoS 保護標準透過 DDoS 攻擊分析，提供詳細的攻擊見解和視覺效果。 保護其虛擬網路避免遭受 DDoS 攻擊的客戶可以詳細了解攻擊流量，並透過攻擊風險降低報告和風險降低流程記錄來緩解攻擊。 豐富的遙測會透過 Azure 監視器公開，包括在 DDoS 攻擊期間的詳細計量。 警示可以針對 DDoS 保護所公開的任何 Azure 監視器計量進行設定。 您可以透過 Azure 儲存體診斷介面，進一步整合 [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-ddos-protection)、Splunk (Azure 事件中樞) 、OMS Log Analytics 和 Azure 監視器的記錄，以進行 advanced analysis。

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 設定 DDoS 保護計量的警示
> * 使用 DDoS 保護遙測
> * 檢視 DDoS 風險降低原則
> * 查看 Azure 資訊安全中心中的 DDoS 保護警示

## <a name="prerequisites"></a>必要條件

- 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 在您可以完成本教學課程中的步驟之前，您必須先建立 [Azure DDoS 標準保護計劃](manage-ddos-protection.md)。

## <a name="configure-alerts-for-ddos-protection-metrics"></a>設定 DDoS 保護計量的警示

透過使用「Azure 監視器」警示設定，您可以選取任何可用的 DDoS 保護計量，以在攻擊期間有作用中的風險降低措施時向您發出警示。 當條件符合時，指定的地址會收到警示電子郵件：

1. 選取入口網站左上角的 [所有服務]。
2. 在 [篩選] 方塊中，輸入「監視器」。 當 **監視器** 出現在搜尋結果中時，請選取它。
3. 選取 [共用服務] 底下的 [計量]。
4. 輸入或選取您自己的值，或是輸入下列範例值，接受其餘預設值，然後選取 [確定]：

    |設定                  |值                                                                                               |
    |---------                |---------                                                                                           |
    |名稱                     | 輸入 _MyDdosAlert_。                                                                                |
    |訂用帳戶             | 選取包含您想要接收警示之公用 IP 位址的訂用帳戶。        |
    |資源群組           | 選取包含您想要接收警示之公用 IP 位址的資源群組。      |
    |資源                 | 選取包含您想要接收警示之公用 IP 位址的公用 IP 位址。 DDoS 會監視指派給虛擬網路內資源的公用 IP 位址。 如果您在虛擬網路中沒有任何具有公用 IP 位址的資源，就必須先建立一個具有公用 IP 位址的資源。 針對 [Azure 服務的虛擬網路](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services#services-that-can-be-deployed-into-a-virtual-network)中所列出透過 Resource Manager (非傳統) 部署的所有資源，您可以監視資源的公用 IP 位址，但「Azure App Service 環境」和「Azure VPN 閘道」除外。 若要繼續進行本教學課程，您可以快速建立一個 [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虛擬機器。                   |
    |計量                   | 選取 [遭受 **DDoS 攻擊**]。                                                                |
    |臨界值                | 1 - **1** 表示您正遭受攻擊。 **0** 表示您並未遭受攻擊。                         |
    |期間                   | 選取您選擇的任何值。                                                                   |
    |透過電子郵件通知         | 核取此核取方塊。                                                                                 |
    |其他系統管理員 | 如果您不是訂用帳戶的電子郵件擁有者、參與者或讀者，請輸入您的電子郵件地址。 |

    在進行攻擊偵測的幾分鐘內，您會從「Azure 監視器」收到看似下圖的電子郵件：

    ![攻擊警示](./media/manage-ddos-protection/ddos-alert.png)


若要模擬 DDoS 攻擊來驗證警示，請參閱[驗證 DDoS 偵測](test-through-simulations.md)。

您也可以深入了解如何[設定 Webhook](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json)和[邏輯應用程式](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)，以便建立警示。

## <a name="use-ddos-protection-telemetry"></a>使用 DDoS 保護遙測

攻擊的遙測可透過 Azure 監視器即時提供。 遙測只適用於公用 IP 位址在安全防護之下的期間。 在攻擊風險降低之前或之後，您都不會看到遙測。

1. 選取入口網站左上角的 [所有服務]。
2. 在 [篩選] 方塊中，輸入「監視器」。 當 **監視器** 出現在搜尋結果中時，請選取它。
3. 選取 [**共用服務**] 下的 [**計量**]。
4. 選取包含您想要其遙測資料之公用 IP 位址的 [訂用帳戶] 和 [資源群組]。
5. 針對 [資源類型] 選取 [公用 IP 位址]，然後選取您想要其遙測資料的特定公用 IP 位址。
6. 畫面的左側會出現一系列 **可用的計量**。 若選取這些計量，這些度量會在概觀畫面上的 **Azure 監視器計量圖** 中繪製成圖形。
7. 選取 [**最大值**]**匯總** 類型

計量名稱呈現不同的套件類型和位元組及封包，包含每個計量的標籤名稱基本結構，如下所示：

- **捨棄的標籤名稱** (例如 **捨棄的輸入封包 DDoS**)：DDoS 保護系統所捨棄/清除的封包數目。
- **轉送的標籤名稱** (例如 **轉送的輸入封包 DDoS**)：DDoS 系統轉送到目的地 VIP 的封包數目 – 未篩選的流量。
- **沒有標籤名稱** (例如 **輸入封包 DDoS**)：進入清除系統的封包總數 – 代表所捨棄和轉送的封包總和。

此 [Azure 監視器警示規則](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Azure%20Monitor%20Alert%20-%20DDoS%20Mitigation%20Started) 會執行簡單的查詢，以偵測何時發生主動 DDoS 風險降低。 若要模擬 DDoS 攻擊來驗證遙測，請參閱[驗證 DDoS 偵測](test-through-simulations.md)。 

## <a name="view-ddos-mitigation-policies"></a>檢視 DDoS 風險降低原則

「標準 DDoS 保護」會在啟用 DDoS 的虛擬網路中，針對受保護資源的每個公用 IP 位址套用三個自動調整的風險降低措施 (TCP SYN、TCP 及 UDP)。 您可以藉由選取  **輸入 TCP 封包來觸發 ddos 緩和** 和 **輸入 UDP 封包，以觸發** 以 **匯總** 類型為「最大值」的 ddos 風險降低計量（如下圖所示）來查看原則閾值：

![檢視風險降低原則](./media/manage-ddos-protection/view-mitigation-policies.png)

原則閾值會透過以 Azure Machine Learning 為基礎的網路流量分析進行自動設定。 只有在超出原則閾值時，才會針對遭受攻擊的 IP 位址進行 DDoS 風險降低措施。

## <a name="view-ddos-protection-alerts-in-azure-security-center"></a>查看 Azure 資訊安全中心中的 DDoS 保護警示

Azure 資訊安全中心提供 [安全性警示](/azure/security-center/security-center-managing-and-responding-alerts)的清單，以及協助調查和修復問題的資訊。 利用這項功能，您可以取得警示的統一觀點，包括 DDoS 攻擊相關的警示，以及近乎及時緩和攻擊所採取的動作。
有兩個特定警示可供您看到任何 DDoS 攻擊偵測和風險降低：

- 偵測 **到公用 IP 的 DDoS 攻擊**：當 ddos 保護服務偵測到其中一個公用 IP 位址是 DDoS 攻擊的目標時，就會產生此警示。
- **降低公用 ip 的 DDoS 攻擊**：當公用 ip 位址的攻擊已緩和時，就會產生此警示。
若要查看警示，請在 Azure 入口網站中開啟 [ **Security Center** ]。 在 [ **威脅防護**] 下，選取 [ **安全性警示**]。 下列螢幕擷取畫面顯示 DDoS 攻擊警示的範例。

![Azure 資訊安全中心中的 DDoS 警示](./media/manage-ddos-protection/ddos-alert-asc.png)

警示包含遭受攻擊的公用 IP 位址、地理和威脅情報資訊，以及補救步驟的一般資訊。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

- 設定 DDoS 保護計量的警示
- 使用 DDoS 保護遙測
- 檢視 DDoS 風險降低原則
- 查看 Azure 資訊安全中心中的 DDoS 保護警示

若要瞭解如何設定攻擊風險降低報告和流程記錄，請繼續進行下一個教學課程。

> [!div class="nextstepaction"]
> [設定 DDoS 攻擊風險降低報告和流量記錄](reports-and-flow-logs.md)
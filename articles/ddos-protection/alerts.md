---
title: 查看並設定 Azure DDoS 保護 Standard 的 DDoS 保護警示
description: 瞭解如何針對 Azure DDoS 保護 Standard 來查看和設定 DDoS 保護警示。
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/28/2020
ms.author: yitoh
ms.openlocfilehash: d9b77def3ccefe3c866ccef78684d38da0b8a268
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97915142"
---
# <a name="view-and-configure-ddos-protection-alerts"></a>檢視和設定 DDoS 保護警示

Azure DDoS 保護標準透過 DDoS 攻擊分析，提供詳細的攻擊見解和視覺效果。 保護其虛擬網路避免遭受 DDoS 攻擊的客戶可以詳細了解攻擊流量，並透過攻擊風險降低報告和風險降低流程記錄來緩解攻擊。 豐富的遙測會透過 Azure 監視器公開，包括在 DDoS 攻擊期間的詳細計量。 警示可以針對 DDoS 保護所公開的任何 Azure 監視器計量進行設定。 您可以透過 Azure 儲存體診斷介面，進一步整合 [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md)、Splunk (Azure 事件中樞) 、OMS Log Analytics 和 Azure 監視器的記錄，以進行 advanced analysis。

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 透過 Azure 監視器設定警示
> * 透過入口網站設定警示
> * 在 Azure 資訊安全中心中查看警示
> * 驗證及測試您的警示

## <a name="prerequisites"></a>必要條件

- 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 在您可以完成本教學課程中的步驟之前，您必須先建立 [Azure DDoS 標準保護方案](manage-ddos-protection.md) ，而且必須在虛擬網路上啟用 Ddos 保護標準。
- DDoS 會監視指派給虛擬網路內資源的公用 IP 位址。 如果您在虛擬網路中沒有任何具有公用 IP 位址的資源，就必須先建立一個具有公用 IP 位址的資源。 您可以透過 [azure 服務的虛擬網路](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) 中所列出的 Resource Manager (非傳統) ，監視所有部署資源的公用 IP 位址 (包括) 環境以外的後端虛擬機器位於虛擬網路 Azure App Service 的 Azure 負載平衡器。 若要繼續進行本教學課程，您可以快速建立一個 [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虛擬機器。     

## <a name="configure-alerts-through-azure-monitor"></a>透過 Azure 監視器設定警示

使用這些範本，您將能夠針對已啟用診斷記錄的所有公用 IP 位址設定警示。 因此，若要使用這些警示範本，您需要先啟用診斷設定的 Log Analytics 工作區。 請參閱 [查看和設定 DDoS 診斷記錄](diagnostic-logging.md)。

### <a name="azure-monitor-alert-rule"></a>Azure 監視器警示規則
此 [Azure 監視器警示規則](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Azure%20Monitor%20Alert%20-%20DDoS%20Mitigation%20Started) 會執行簡單的查詢，以偵測何時發生主動 DDoS 風險降低。 這表示潛在的攻擊。 動作群組可用來將動作當做警示的結果來叫用。

[![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FAzure%2520Monitor%2520Alert%2520-%2520DDoS%2520Mitigation%2520Started%2FDDoSMitigationStarted.json)

### <a name="azure-monitor-alert-rule-with-logic-app"></a>使用邏輯應用程式 Azure 監視器警示規則

此 [範本](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/DDoS%20Mitigation%20Alert%20Enrichment) 會部署擴充 DDoS 緩和警示的必要元件： Azure 監視器警示規則、動作群組和邏輯應用程式。 進程的結果是一封電子郵件警示，內含有關受攻擊之 IP 位址的詳細資料，包括與 IP 相關聯之資源的相關資訊。 資源的擁有者會新增為電子郵件的收件者及安全性小組。 此外，也會執行基本的應用程式可用性測試，且結果會包含在電子郵件警示中。

[![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FDDoS%2520Mitigation%2520Alert%2520Enrichment%2FEnrich-DDoSAlert.json)

## <a name="configure-alerts-through-portal"></a>透過入口網站設定警示

透過使用「Azure 監視器」警示設定，您可以選取任何可用的 DDoS 保護計量，以在攻擊期間有作用中的風險降低措施時向您發出警示。 

1. 登入 [Azure 入口網站](https://portal.azure.com/) ，然後流覽至您的 DDoS 保護計劃。
2. 在 [監視] 下，選取 [計量]。
3. 在灰色巡覽列中，選取 [ **新增警示規則**]。 
4. 輸入或選取您自己的值，或輸入下列範例值，接受其餘的預設值，然後選取 [ **建立警示規則**：

    |設定                  |值                                                                                               |
    |---------                |---------                                                                                           |
    | 範圍                   | 選取 [選取資源]。 </br> 選取包含您要記錄之公用 IP 位址的 **訂** 用帳戶，選取 [**資源類型**] 的 [**公用 ip 位址**]，然後選取您要記錄計量的特定公用 ip 位址。 </br> 選取 [完成]。 | 
    | 條件 | 選取 [ **選取條件**]。 </br> 在 [信號名稱] 底下，選取 [發生 **DDoS 攻擊**]。 </br> 在 [ **運算子**] 底下，選取 [ **大於或等於**]。 </br> 在 [ **匯總類型**] 下，選取 [ **最大值**]。 </br> 在 [ **臨界值**] 下，輸入 *1*。 在遭受 **DDoS 攻擊或非** 計量的情況下， **0** 表示您不受攻擊， **1** 表示您遭受攻擊。 </br> 選取 [完成]。 | 
    | 動作 | 選取 [ **新增動作群組**]。 </br> 選取 [建立動作群組]。 </br> 在 [ **通知**] 底下的 [ **通知類型**] 下，選取 [ **電子郵件/SMS 訊息/推播/語音**]。 </br> 在 [ **名稱**] 底下，輸入 _MyUnderAttackEmailAlert_。 </br> 按一下 [編輯] 按鈕，然後選取 [ **電子郵件** ] 和您所需的下列許多選項，然後選取 **[確定]**。 </br> 選取 [檢閱 + 建立]。 | 
    | 警示規則詳細資料 | 在 [ **警示規則名稱**] 底下，輸入 _MyDdosAlert_。 |

在偵測到攻擊的幾分鐘內，您應該會收到一封來自 Azure 監視器計量的電子郵件，如下圖所示：

![攻擊警示](./media/manage-ddos-protection/ddos-alert.png)

您也可以深入了解如何[設定 Webhook](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json)和[邏輯應用程式](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)，以便建立警示。

## <a name="view-alerts-in-azure-security-center"></a>在 Azure 資訊安全中心中查看警示

Azure 資訊安全中心提供 [安全性警示](../security-center/security-center-managing-and-responding-alerts.md)的清單，以及協助調查和修復問題的資訊。 利用這項功能，您可以取得警示的統一觀點，包括 DDoS 攻擊相關的警示，以及近乎及時緩和攻擊所採取的動作。
有兩個特定警示可供您看到任何 DDoS 攻擊偵測和風險降低：

- 偵測 **到公用 IP 的 DDoS 攻擊**：當 ddos 保護服務偵測到其中一個公用 IP 位址是 DDoS 攻擊的目標時，就會產生此警示。
- **降低公用 ip 的 DDoS 攻擊**：當公用 ip 位址的攻擊已緩和時，就會產生此警示。
若要查看警示，請在 Azure 入口網站中開啟 [ **Security Center** ]。 在 [ **威脅防護**] 下，選取 [ **安全性警示**]。 下列螢幕擷取畫面顯示 DDoS 攻擊警示的範例。

![Azure 資訊安全中心中的 DDoS 警示](./media/manage-ddos-protection/ddos-alert-asc.png)

警示包含遭受攻擊的公用 IP 位址、地理和威脅情報資訊，以及補救步驟的一般資訊。

## <a name="validate-and-test"></a>驗證和測試

若要模擬 DDoS 攻擊來驗證您的警示，請參閱 [驗證 ddos 偵測](test-through-simulations.md)。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

- 透過 Azure 監視器設定警示
- 透過入口網站設定警示
- 在 Azure 資訊安全中心中查看警示
- 驗證及測試您的警示

若要瞭解如何測試和模擬 DDoS 攻擊，請參閱模擬測試指南：

> [!div class="nextstepaction"]
> [透過模擬測試](test-through-simulations.md)

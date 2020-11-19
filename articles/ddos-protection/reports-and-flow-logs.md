---
title: Azure DDoS 保護標準報表和流程記錄
description: 瞭解如何設定報表和流程記錄。
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
ms.openlocfilehash: 28e977ed68a3f288f9f86a0c2be02af4cbb26ba4
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2020
ms.locfileid: "94886556"
---
# <a name="configure-ddos-attack-mitigation-reports-and-flow-logs"></a>設定 DDoS 攻擊風險降低報告和流量記錄 

Azure DDoS 保護標準透過 DDoS 攻擊分析，提供詳細的攻擊見解和視覺效果。 保護其虛擬網路避免遭受 DDoS 攻擊的客戶可以詳細了解攻擊流量，並透過攻擊風險降低報告和風險降低流程記錄來緩解攻擊。 豐富的遙測會透過 Azure 監視器公開，包括在 DDoS 攻擊期間的詳細計量。 警示可以針對 DDoS 保護所公開的任何 Azure 監視器計量進行設定。 您可以透過 Azure 儲存體診斷介面，進一步整合 [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-ddos-protection)、Splunk (Azure 事件中樞) 、OMS Log Analytics 和 Azure 監視器的記錄，以進行 advanced analysis。

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 查看及設定 DDoS 攻擊風險降低報告
> * 查看及設定 DDoS 攻擊風險降低流程記錄

## <a name="prerequisites"></a>必要條件

- 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 在您可以完成本教學課程中的步驟之前，您必須先建立 [Azure DDoS 標準保護計劃](manage-ddos-protection.md)。

## <a name="view-and-configure-ddos-attack-mitigation-reports"></a>查看及設定 DDoS 攻擊風險降低報告

攻擊風險降低報告使用 Netflow 通訊協定資料進行彙總，以提供有關資源攻擊的詳細資訊。 每當公用 IP 資源遭受攻擊時，將在啟動風險降低措施後，立即產生風險降低報告。 每隔 5 分鐘就會產生一份增量報告，以及針對整個風險降低期間，產生一份風險降低後續報告。 這是為了確保在 DDoS 攻擊持續進行較長時間的情況下，您將能夠每 5 分鐘檢視最新的風險降低報告快照集，並在攻擊風險降低結束後，檢視完整摘要。 

1. 選取入口網站左上角的 [所有服務]。
2. 在 [篩選] 方塊中，輸入「監視器」。 當 **監視器** 出現在搜尋結果中時，請選取它。
3. 在 [設定] 底下，選取 [診斷設定]。
4. 選取包含您所要記錄公用 IP 位址的 [訂用帳戶] 和 [資源群組]。
5. 針對 [資源類型] 選取 [公用 IP 位址]，然後選取您想要為其記錄計量的特定公用 IP 位址。
6. 選取 [開啟診斷以收集 DDoSMitigationReports 記錄]，然後視需要選取下列眾多選項：

    - **封存至儲存體帳戶**：可將資料寫入至「Azure 儲存體」帳戶。 若要深入瞭解這個選項，請參閱封存 [資源記錄](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)檔。
    - **串流至事件中樞**：可讓記錄接收者使用「Azure 事件中樞」來挑選記錄。 事件中樞可允許與 Splunk 或其他 SIEM 系統進行整合。 若要深入瞭解這個選項，請參閱 [將資源記錄串流至事件中樞](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。
    - **傳送至 Log Analytics**：將記錄寫入 Azure 監視器服務。 若要深入瞭解這個選項，請參閱 [收集記錄以用於 Azure 監視器記錄](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)檔。

增量及攻擊風險降低後續報告包括下列欄位
- 攻擊媒介
- 流量統計資料
- 丟棄封包的原因
- 涉及的通訊協定
- 前 10 個來源國家/地區或區域
- 前 10 個來源的 ASN

## <a name="view-and-configure-ddos-attack-mitigation-flow-logs"></a>查看及設定 DDoS 攻擊風險降低流程記錄
攻擊風險降低流程記錄可讓您在近乎即時的主動 DDoS 攻擊期間，檢查捨棄的流量、轉送的流量，以及其他感興趣的資料點。 您可以透過事件中樞將此資料的常數資料流程內嵌至 Azure Sentinel 或協力廠商 SIEM 系統，以進行近乎即時的監視、採取潛在的動作，並解決您的防禦作業需求。

1. 選取入口網站左上角的 [所有服務]。
2. 在 [篩選] 方塊中，輸入「監視器」。 當 **監視器** 出現在搜尋結果中時，請選取它。
3. 在 [設定] 底下，選取 [診斷設定]。
4. 選取包含您所要記錄公用 IP 位址的 [訂用帳戶] 和 [資源群組]。
5. 針對 [資源類型] 選取 [公用 IP 位址]，然後選取您想要為其記錄計量的特定公用 IP 位址。
6. 選取 [開啟診斷以收集 DDoSMitigationFlowLogs 記錄]，然後視需要選取下列眾多選項：

    - **封存至儲存體帳戶**：可將資料寫入至「Azure 儲存體」帳戶。 若要深入瞭解這個選項，請參閱封存 [資源記錄](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)檔。
    - **串流至事件中樞**：可讓記錄接收者使用「Azure 事件中樞」來挑選記錄。 事件中樞可允許與 Splunk 或其他 SIEM 系統進行整合。 若要深入瞭解這個選項，請參閱 [將資源記錄串流至事件中樞](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。
    - **傳送至 Log Analytics**：將記錄寫入 Azure 監視器服務。 若要深入瞭解這個選項，請參閱 [收集記錄以用於 Azure 監視器記錄](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)檔。

此 [範本](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Enable%20Diagnostic%20Logging/Azure%20Policy) 會建立 Azure 原則定義來啟用診斷記錄。

### <a name="azure-sentinel-data-connector"></a>Azure Sentinel 資料連線器

您可以連接到 Azure Sentinel、查看和分析活頁簿中的資料、建立自訂警示，並將其併入調查程式。 若要連接到 Azure Sentinel，請參閱 [連接到 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-ddos-protection)。 

![Azure Sentinel DDoS 連接器](./media/ddos-attack-telemetry/azure-sentinel-ddos.png)

### <a name="azure-ddos-protection-workbook"></a>Azure DDoS 保護活頁簿

若要在 Azure 分析儀表板中檢視流程記錄資料，您可以從 https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Azure%20DDoS%20Protection%20Workbook 匯入範例儀表板

流程記錄將會有下列欄位： 
- 來源 IP
- 目的地 IP
- 來源連接埠 
- 目的地連接埠 
- 通訊協定類型 
- 在風險降低期間所採取的動作

![DDoS 保護活頁簿](./media/ddos-attack-telemetry/ddos-attack-analytics-workbook.png)

只有在公用 IP 位址的虛擬網路上啟用 DDoS 保護標準時，攻擊分析才會運作。 

## <a name="sample-log-outputs"></a>範例記錄檔輸出

下列螢幕擷取畫面是範例記錄檔輸出：

### <a name="ddosmitigationflowlogs"></a>DDoSMitigationFlowLogs

![DDoS 保護 DDoSMitigationFlowLogs](./media/ddos-attack-telemetry/ddos-mitigation-flow-logs.png)

### <a name="ddosprotectionnotifications"></a>DDoSProtectionNotifications

![DDoS 保護 DDoSProtectionNotifications](./media/ddos-attack-telemetry/ddos-protection-notifications.png)

### <a name="ddosmitigationreports"></a>DDoSMitigationReports

![DDoS 保護 DDoSMitigationReports](./media/ddos-attack-telemetry/ddos-mitigation-reports.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

- 查看及設定 DDoS 攻擊風險降低報告
- 查看及設定 DDoS 攻擊風險降低流程記錄

若要瞭解如何測試和模擬 DDoS 攻擊，請參閱模擬測試指南：

> [!div class="nextstepaction"]
> [透過模擬測試](test-through-simulations.md)


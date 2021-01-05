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
ms.date: 12/28/2020
ms.author: yitoh
ms.openlocfilehash: dd350cc5fa0c3b30b4f0d57938348a8328af311a
ms.sourcegitcommit: 42922af070f7edf3639a79b1a60565d90bb801c0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/31/2020
ms.locfileid: "97827388"
---
# <a name="view-and-configure-ddos-diagnostic-logging"></a>查看及設定 DDoS 診斷記錄

Azure DDoS 保護標準透過 DDoS 攻擊分析，提供詳細的攻擊見解和視覺效果。 保護其虛擬網路避免遭受 DDoS 攻擊的客戶可以詳細了解攻擊流量，並透過攻擊風險降低報告和風險降低流程記錄來緩解攻擊。 豐富的遙測會透過 Azure 監視器公開，包括在 DDoS 攻擊期間的詳細計量。 警示可以針對 DDoS 保護所公開的任何 Azure 監視器計量進行設定。 您可以透過 Azure 儲存體診斷介面，進一步整合 [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md)、Splunk (Azure 事件中樞) 、OMS Log Analytics 和 Azure 監視器的記錄，以進行 advanced analysis。

以下是適用于 Azure DDoS 保護 Standard 的診斷記錄： 

- **DDoSProtectionNotifications**：通知會在公用 IP 資源遭受攻擊時，以及當攻擊風險降低時通知您。
- **DDoSMitigationFlowLogs**：攻擊風險降低流程記錄可讓您在近乎即時的主動 DDoS 攻擊期間，檢查丟棄的流量、轉送的流量，以及其他有趣的資料點。 您可以透過事件中樞將此資料的常數資料流程內嵌至 Azure Sentinel 或協力廠商 SIEM 系統，以進行近乎即時的監視、採取潛在的動作，並解決您的防禦作業需求。
- **DDoSMitigationReports**：攻擊風險降低報告使用所匯總的 Netflow 通訊協定資料，提供有關您資源之攻擊的詳細資訊。 每當公用 IP 資源遭受攻擊時，將在啟動風險降低措施後，立即產生風險降低報告。 每隔 5 分鐘就會產生一份增量報告，以及針對整個風險降低期間，產生一份風險降低後續報告。 這是為了確保在 DDoS 攻擊持續進行較長時間的情況下，您將能夠每 5 分鐘檢視最新的風險降低報告快照集，並在攻擊風險降低結束後，檢視完整摘要。 
- **AllMetrics**：提供在 DDoS 攻擊期間提供的所有可能計量。 

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 設定 DDoS 診斷記錄，包括通知、風險降低報告和風險降低流程記錄。 
> * 在定義的範圍內的所有公用 Ip 上啟用診斷記錄。
> * 查看活頁簿中的記錄資料。

## <a name="prerequisites"></a>必要條件

- 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 在您可以完成本教學課程中的步驟之前，您必須先建立 [Azure DDoS 標準保護方案](manage-ddos-protection.md) ，而且必須在虛擬網路上啟用 Ddos 保護標準。
- DDoS 會監視指派給虛擬網路內資源的公用 IP 位址。 如果您在虛擬網路中沒有任何具有公用 IP 位址的資源，就必須先建立一個具有公用 IP 位址的資源。 您可以透過 [azure 服務的虛擬網路](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) 中所列出的 Resource Manager (非傳統) ，來監視所有部署資源的公用 IP 位址 (包括) 環境和 Azure VPN 閘道以外的後端虛擬機器位於虛擬網路 Azure App Service 的 Azure 負載平衡器。 若要繼續進行本教學課程，您可以快速建立一個 [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虛擬機器。    

## <a name="configure-ddos-diagnostic-logs"></a>設定 DDoS 診斷記錄

如果您想要在環境內的所有公用 Ip 上自動啟用診斷記錄，請略過 [啟用所有公用 ip 的診斷記錄](#enable-diagnostic-logging-on-all-public-ips)。

1. 選取入口網站左上角的 [所有服務]。
2. 在 [篩選] 方塊中，輸入「監視器」。 當 **監視器** 出現在搜尋結果中時，請選取它。
3. 在 [ **設定**] 底下，選取 [ **診斷設定**]。
4. 選取包含您所要記錄公用 IP 位址的 [訂用帳戶] 和 [資源群組]。
5. 選取 [ **公用 Ip 位址** ] 作為 [ **資源類型**]，然後選取您想要啟用記錄的特定公用 ip 位址。
6. 選取 [新增診斷設定]。 在 [ **類別目錄詳細資料**] 底下，選取您所需的下列許多選項，然後選取 [ **儲存**]。

    ![DDoS 診斷設定](./media/ddos-attack-telemetry/ddos-diagnostic-settings.png)

7. 在 [ **目的地詳細資料**] 底下，選取您所需的下列許多選項：

    - **封存至儲存體帳戶**：可將資料寫入至「Azure 儲存體」帳戶。 若要深入瞭解這個選項，請參閱封存 [資源記錄](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage)檔。
    - **串流至事件中樞**：可讓記錄接收者使用「Azure 事件中樞」來挑選記錄。 事件中樞可允許與 Splunk 或其他 SIEM 系統進行整合。 若要深入瞭解這個選項，請參閱 [將資源記錄串流至事件中樞](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs)。
    - **傳送至 Log Analytics**：將記錄寫入 Azure 監視器服務。 若要深入瞭解這個選項，請參閱 [收集記錄以用於 Azure 監視器記錄](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-log-analytics-workspace)檔。

### <a name="log-schemas"></a>記錄架構

下表列出各個欄位的名稱和其描述：

# <a name="ddosprotectionnotifications"></a>[DDoSProtectionNotifications](#tab/DDoSProtectionNotifications)

| 欄位名稱 | 描述 |
| --- | --- |
| **TimeGenerated** | 建立通知的 UTC 日期和時間。 |
| **ResourceId** | 公用 IP 位址的資源識別碼。 |
| **類別** | 針對通知，這會是 `DDoSProtectionNotifications` 。|
| **ResourceGroup** | 包含您的公用 IP 位址和虛擬網路的資源群組。 |
| **SubscriptionId** | 您的 DDoS 保護方案訂用帳戶識別碼。 |
| **Resource** | 公用 IP 位址的名稱。 |
| **ResourceType** | 這一律會是 `PUBLICIPADDRESS` 。 |
| **OperationName** | 針對通知，這會是 `DDoSProtectionNotifications` 。  |
| **Message** | 攻擊的詳細資料。 |
| **型別** | 通知的類型。 可能的值包括 `MitigationStarted` 。 `MitigationStopped`. |
| **PublicIpAddress** | 您的公用 IP 位址。 |

# <a name="ddosmitigationflowlogs"></a>[DDoSMitigationFlowLogs](#tab/DDoSMitigationFlowLogs)

| 欄位名稱 | 描述 |
| --- | --- |
| **TimeGenerated** | 建立流量記錄時的 UTC 日期和時間。 |
| **ResourceId** | 公用 IP 位址的資源識別碼。 |
| **類別** | 若為流程記錄，這會是 `DDoSMitigationFlowLogs` 。|
| **ResourceGroup** | 包含您的公用 IP 位址和虛擬網路的資源群組。 |
| **SubscriptionId** | 您的 DDoS 保護方案訂用帳戶識別碼。 |
| **Resource** | 公用 IP 位址的名稱。 |
| **ResourceType** | 這一律會是 `PUBLICIPADDRESS` 。 |
| **OperationName** | 若為流程記錄，這會是 `DDoSMitigationFlowLogs` 。 |
| **Message** | 攻擊的詳細資料。 |
| **SourcePublicIpAddress** | 用戶端的公用 IP 位址，其會產生流量至您的公用 IP 位址。 |
| **SourcePort** | 範圍從0到65535的埠號碼。 |
| **DestPublicIpAddress** | 您的公用 IP 位址。 |
| **DestPort** | 範圍從0到65535的埠號碼。 |
| **通訊協定** | 通訊協定的類型。 可能的值包括 `tcp` 、 `udp` 、 `other` 。|

# <a name="ddosmitigationreports"></a>[DDoSMitigationReports](#tab/DDoSMitigationReports)

| 欄位名稱 | 描述 |
| --- | --- |
| **TimeGenerated** | 建立報表時的 UTC 日期和時間。 |
| **ResourceId** | 公用 IP 位址的資源識別碼。 |
| **類別** | 針對通知，這會是 `DDoSProtectionNotifications` 。|
| **ResourceGroup** | 包含您的公用 IP 位址和虛擬網路的資源群組。 |
| **SubscriptionId** | 您的 DDoS 保護方案訂用帳戶識別碼。 |
| **Resource** | 公用 IP 位址的名稱。 |
| **ResourceType** | 這一律會是 `PUBLICIPADDRESS` 。 |
| **OperationName** | 針對風險降低報告，這會是 `DDoSMitigationReports` 。 |
| **>reporttype** | 可能的值包括 `Incremental` 、 `PostMitigation` 。|
| **MitigationPeriodStart** | 風險降低開始的 UTC 日期和時間。  |
| **MitigationPeriodEnd** | 風險降低結束的 UTC 日期和時間。 |
| **IPAddress** | 您的公用 IP 位址。 |
| **AttackVectors** |  攻擊類型的細目。 索引鍵包括 `TCP SYN flood` 、、 `TCP flood` `UDP flood` 、 `UDP reflection` 、 `Other packet flood` 。|
| **TrafficOverview** |  攻擊流量的細目。 索引鍵包括 `Total packets` 、、 `Total packets dropped` 、、、、 `Total TCP packets` `Total TCP packets dropped` `Total UDP packets` `Total UDP packets dropped` `Total Other packets` 、 `Total Other packets dropped` 。 |
| **通訊協定** | 相關的通訊協定明細。 索引鍵包括 `TCP` 、 `UDP` 、 `Other` 。 |
| **DropReasons** | 捨棄封包的原因明細。 索引鍵包括、、、、、、、、 `Protocol violation invalid TCP syn` `Protocol violation invalid TCP` `Protocol violation invalid UDP` `UDP reflection` `TCP rate limit exceeded` `UDP rate limit exceeded` `Destination limit exceeded` `Other packet flood` `Rate limit exceeded` 、 `Packet was forwarded to service` 。 |
| **TopSourceCountries** | 連入流量的前10個來源國家/地區的明細。 |
| **TopSourceCountriesForDroppedPackets** | 受攻擊流量的前10個來源國家/地區的明細。 |
| **TopSourceASNs** | 前10個來源自發系統編號的細目 (ASN) 的連入流量。  |
| **SourceContinents** | 連入流量來源洲的明細。 |
***

## <a name="enable-diagnostic-logging-on-all-public-ips"></a>啟用所有公用 Ip 的診斷記錄

此 [範本](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Enable%20Diagnostic%20Logging/Azure%20Policy) 會建立 Azure 原則定義，以在定義的範圍內的所有公用 IP 記錄上自動啟用診斷記錄。

[![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FEnable%2520Diagnostic%2520Logging%2FAzure%2520Policy%2FDDoSLogs.json)

## <a name="view-log-data-in-workbooks"></a>查看活頁簿中的記錄資料

### <a name="azure-sentinel-data-connector"></a>Azure Sentinel 資料連線器

您可以連接記錄來 Azure Sentinel、查看和分析活頁簿中的資料、建立自訂警示，並將其併入調查程式。 若要連接到 Azure Sentinel，請參閱 [連接到 Azure Sentinel](../sentinel/connect-azure-ddos-protection.md)。 

![Azure Sentinel DDoS 連接器](./media/ddos-attack-telemetry/azure-sentinel-ddos.png)

### <a name="azure-ddos-protection-workbook"></a>Azure DDoS 保護活頁簿

您可以使用此 Azure Resource Manager (ARM) 範本來部署攻擊分析活頁簿。 此活頁簿可讓您將跨多個可篩選面板的攻擊資料視覺化，以輕鬆瞭解有哪些風險。 

[![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FAzure%2520DDoS%2520Protection%2520Workbook%2FAzureDDoSWorkbook_ARM.json)

![DDoS 保護活頁簿](./media/ddos-attack-telemetry/ddos-attack-analytics-workbook.png)

## <a name="validate-and-test"></a>驗證和測試

若要模擬 DDoS 攻擊來驗證您的記錄，請參閱 [驗證 ddos 偵測](test-through-simulations.md)。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

- 設定 DDoS 診斷記錄，包括通知、風險降低報告和風險降低流程記錄。 
- 在定義的範圍內的所有公用 Ip 上啟用診斷記錄。
- 查看活頁簿中的記錄資料。

若要瞭解如何設定攻擊警示，請繼續進行下一個教學課程。

> [!div class="nextstepaction"]
> [查看及設定 DDoS 保護警示](alerts.md)

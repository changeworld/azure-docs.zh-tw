---
title: 在 Azure 遷移伺服器評估中設置基於代理的依賴項分析
description: 本文介紹如何在 Azure 遷移伺服器評估中設置基於代理的依賴項分析。
ms.topic: how-to
ms.date: 2/24/2020
ms.openlocfilehash: e61b7b4e6c3e566aa67d2bd585d2049ae885083b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79453610"
---
# <a name="set-up-dependency-visualization"></a>設置依賴項視覺化

本文介紹如何在 Azure 遷移：伺服器評估中設置基於代理的依賴項分析。 [依賴關係分析](concepts-dependency-visualization.md)可説明您識別和瞭解要評估和遷移到 Azure 的電腦之間的依賴項。

## <a name="before-you-start"></a>開始之前

- [瞭解](concepts-dependency-visualization.md#agent-based-analysis)基於代理的依賴項分析。
- 查看為[VMware VM、](migrate-support-matrix-vmware.md#agent-based-dependency-analysis-requirements)[物理伺服器](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)和[超 VM](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements)設置基於代理的依賴項視覺化的先決條件和支援要求。
- 請確保[已創建](how-to-add-tool-first-time.md)Azure 遷移專案。
- 如果已創建專案，請確保[已添加](how-to-assess.md)Azure 遷移：伺服器評估工具。
- 請確保已設置[Azure 遷移設備](migrate-appliance.md)以發現本地電腦。 瞭解如何為[VMware、Hyper-V](how-to-set-up-appliance-vmware.md)或[Hyper-V](how-to-set-up-appliance-hyper-v.md)[物理伺服器](how-to-set-up-appliance-physical.md)設置設備。 設備發現本地電腦，並將中繼資料、效能資料發送到 Azure 遷移：伺服器評估。
- 要使用依賴項視覺化，可以將[日誌分析工作區](../azure-monitor/platform/manage-access.md)與 Azure 遷移專案相關聯：
    - 只能在設置 Azure 遷移設備並在 Azure 遷移專案中發現電腦後附加工作區。
    - 請確保訂閱中有一個包含 Azure 遷移專案的工作區。
    - 工作區必須位於美國東部、東南亞或西歐區域。 其他區域中的工作區不能與專案關聯。
    - 工作區必須位於[支援服務映射](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)的區域。
    - 您可以將新的或現有的日誌分析工作區與 Azure 遷移專案相關聯。
    - 首次為電腦設置依賴項視覺化時附加工作區。 添加 Azure 遷移專案的工作區後無法對其進行修改。
    - 在日誌分析中，與 Azure 遷移關聯的工作區將使用遷移專案鍵和專案名稱進行標記。

## <a name="associate-a-workspace"></a>關聯工作區

1. 發現用於評估的電腦後，請在 **"伺服器** > **Azure 遷移：伺服器評估**"中按一下"**概述**"。  
2. 在**Azure 遷移：伺服器評估中**，按一下 **"基本"。**
3. 在**OMS 工作區**中，按一下"**需要配置**"。

     ![配置日誌分析工作區](./media/how-to-create-group-machine-dependencies/oms-workspace-select.png)   

4. 在 **"配置 OMS 工作區**"中，指定是創建新工作區還是使用現有工作區。
    - 您可以從遷移專案訂閱中的所有工作區中選擇現有工作區。
    - 您需要讀取器對工作區的存取權限來關聯它。
5. 如果創建新工作區，請為其選擇位置。

    ![新增工作區](./media/how-to-create-group-machine-dependencies/workspace.png)


## <a name="download-and-install-the-vm-agents"></a>下載並安裝虛擬機器代理程式

在要分析的每台電腦上，安裝代理。

> [!NOTE]
> 對於系統中心操作管理器 2012 R2 或更高版本的監視電腦，您無需安裝 MMA 代理。 服務映射與運營管理器集成。 [遵循](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites)集成指南。

1. 在**Azure 遷移：伺服器評估中**，按一下 **"發現伺服器**"。
2. 對於要使用依賴項視覺化分析的每台電腦，在 **"依賴項"** 列中，按一下 **"需要代理安裝**"。
3. 在 **"依賴項"** 頁中，下載 Windows 或 Linux 的 MMA 和依賴項代理。
4. 在 **"配置 MMA 代理**"下，複製工作區 ID 和金鑰。 安裝 MMA 代理時需要這些。

    ![安裝代理](./media/how-to-create-group-machine-dependencies/dependencies-install.png)


## <a name="install-the-mma"></a>安裝 MMA

在要分析的每個 Windows 或 Linux 電腦上安裝 MMA。

### <a name="install-mma-on-a-windows-machine"></a>在 Windows 電腦上安裝 MMA

在 Windows 電腦上安裝代理程式：

1. 按兩下下載的代理程式。
2. 在 [歡迎]**** 頁面上，按 [下一步]****。 在 **"許可證條款"** 頁上，按一下"**我同意**接受許可證"。
3. 在 [目的地資料夾]**** 中，保留或修改預設的安裝資料夾 > [下一步]****。
4. 在 [代理程式安裝選項]**** 中，選取 [Azure Log Analytics]**** > [下一步]****。
5. 按一下 [新增]**** 以新增 Log Analytics 工作區。 貼上您從入口網站複製的工作區識別碼和金鑰。 按 [下一步]****。

可以從命令列或使用自動方法（如組態管理員或[Intigua）](https://go.microsoft.com/fwlink/?linkid=2104196)安裝代理。
- [了解更多](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration)有關如何使用這些方法來安裝 MMA 代理程式。
- MMA 代理程式也可以使用此[指令碼](https://go.microsoft.com/fwlink/?linkid=2104394)來安裝。
- [詳細瞭解](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-windows-operating-systems)MMA 支援的 Windows 作業系統。

### <a name="install-mma-on-a-linux-machine"></a>在 Linux 電腦上安裝 MMA

要在 Linux 電腦上安裝 MMA，

1. 使用 scp/sftp 將適當的套件組合 (x86 或 x64) 傳輸到 Linux 電腦。
2. 使用 --安裝引數安裝套件組合。

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[了解更多](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-linux-operating-systems) MMA 支援的 Linux 作業系統清單。 

## <a name="install-the-dependency-agent"></a>安裝相依性代理程式

1. 若要在 Windows 電腦上安裝相依性代理程式，請按兩下安裝檔案，並遵循精靈的指示。
2. 若要在 Linux 電腦上安裝相依性代理程式，請使用下列命令以 root 身分安裝：

    ```sh InstallDependencyAgent-Linux64.bin```

- [深入了解](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-hybrid-cloud#installation-script-examples)如何使用指令碼安裝 Dependency 代理程式。
- [詳細瞭解依賴](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#supported-operating-systems)項代理支援的作業系統。


## <a name="create-a-group-using-dependency-visualization"></a>使用依賴項視覺化創建組

現在創建一個組進行評估。 


> [!NOTE]
> 您想要將相依性視覺化的群組不應該包含超過 10 部的機器。 如果您有 10 多台電腦，則將它們拆分為較小的組。

1. 在**Azure 遷移：伺服器評估中**，按一下 **"發現伺服器**"。
2. 在 **"依賴項"** 列中，按一下要查看要查看的每台電腦**的依賴項**。
3. 在依賴項映射上，您可以看到以下內容：
    - 進出電腦的入站（用戶端）和出站（伺服器）TCP 連接。
    - 未安裝依賴項代理的從屬電腦按埠號分組。
    - 安裝了依賴項代理的從屬電腦顯示為單獨的框。
    - 在機器內部運行的進程。 展開每個機器框以查看流程。
    - 機器屬性（包括 FQDN、作業系統、MAC 位址）。 按一下每個機器框以查看詳細資訊。

4. 您可以按一下時間範圍標籤中的持續時間，以查看不同持續時間的相依性。
    - 根據預設，範圍是一小時。 
    - 您可以修改時間範圍，或指定開始和結束日期，以及持續時間。
    - 時間範圍可達一小時。 如果需要較長的範圍，請使用 Azure 監視器查詢從屬資料的時間較長。

5. 標識要組合在一起的從屬電腦後，請使用 Ctrl+Click 在地圖上選擇多台電腦，然後按一下 **"組電腦**"。
6. 指定群組名稱。
7. 確認 Azure Migrate 已探索到相依機器。

    - 如果 Azure 遷移：伺服器評估未發現從屬電腦，則無法將其添加到組中。
    - 要添加電腦，請再次運行發現，並驗證發現該電腦。

8. 如果您需要建立此群組的評估，請選取核取方塊來建立新的群組評估。
8. 按一下 [確定]**** 以儲存群組。

創建組後，我們建議您在組中的所有電腦上安裝代理，然後視覺化整個組的依賴項。

## <a name="query-dependency-data-in-azure-monitor"></a>Azure 監視器中的查詢依賴項資料

您可以在與 Azure 遷移專案關聯的日誌分析工作區中查詢服務映射捕獲的依賴項資料。 日誌分析用於編寫和運行 Azure 監視器日誌查詢。

- [瞭解如何](../azure-monitor/insights/service-map.md#log-analytics-records)在日誌分析中搜索服務映射資料。
- [在](../azure-monitor/log-query/get-started-queries.md)[日誌分析](../azure-monitor/log-query/get-started-portal.md)中獲取寫入日誌查詢的概述。

運行依賴項資料的查詢，如下所示：

1. 安裝代理程式之後，請移至入口網站，然後按一下 [概觀]****。
2. 在**Azure 遷移：伺服器評估**中，按一下 **"概述**"。 按一下向下箭頭展開 **"基本"。**
3. 在**OMS 工作區**中，按一下工作區名稱。
3. 在"日誌分析"工作區頁面上 **>"常規**"，按一下 **"日誌**"。
4. 寫你的查詢，然後按一下 **"運行**"。

### <a name="sample-queries"></a>範例查詢

下面是一些可用於提取依賴項資料的依例查詢。

- 您可以修改查詢以擷取慣用的資料點。
- [查看](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records)依賴項資料記錄的完整清單。
- [查看](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches)其他依例查詢。

#### <a name="sample-review-inbound-connections"></a>示例：查看入站連接

查看一組 VM 的入站連接。

- 表中的連接指標 （VMConnect） 的記錄不表示單個物理網路連接。
- 將多個實體網路連線分組為一個邏輯連線。
- [詳細瞭解如何在](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections)VMConnect 中聚合物理網路連接資料。

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z);
VMConnection
| where Direction == 'inbound'
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(LinksEstablished) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

#### <a name="sample-summarize-sent-and-received-data"></a>示例：匯總已發送和接收的資料

此示例匯總了一組電腦之間在入站連接上發送和接收的資料量。

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z);
VMConnection
| where Direction == 'inbound'
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(BytesSent), sum(BytesReceived) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

## <a name="next-steps"></a>後續步驟

[為組創建評估](how-to-create-assessment.md)。



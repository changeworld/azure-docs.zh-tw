---
title: 在 Azure Migrate 伺服器評量中設定代理程式相依性分析
description: 本文說明如何在 Azure Migrate 伺服器評量中設定代理程式相依性分析。
ms.topic: how-to
ms.date: 6/09/2020
ms.openlocfilehash: c5c019ec995f59b61fb96917bed50bd8ba3f61d4
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "89022372"
---
# <a name="set-up-dependency-visualization"></a>設定相依性視覺效果

本文說明如何在 Azure Migrate：伺服器評量中設定無代理程式相依性分析。 相依性[分析](concepts-dependency-visualization.md)可協助您識別並瞭解您想要評估及遷移至 Azure 的機器之間的相依性。

## <a name="before-you-start"></a>在您開始使用 Intune 之前

- 請參閱下列各項的代理程式相依性分析支援和部署需求：
    - [VMware VM](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agent-based)
    - [實體伺服器](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)
    - [Hyper-v vm](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements)。
- 請確定您已執行下列動作：
    - 有 Azure Migrate 專案。 如果沒有，請立即 [建立](how-to-add-tool-first-time.md) 一個。
    - 檢查您是否已將 Azure Migrate：伺服器評估工具 [新增](how-to-assess.md) 至專案。
    - 設定 [Azure Migrate 設備](migrate-appliance.md) 以探索內部部署機器。 設備會探索內部部署機器，並將中繼資料和效能資料傳送至 Azure Migrate：伺服器評量。 設定設備：
        - [VMware](how-to-set-up-appliance-vmware.md) Vm。
        - [Hyper-v](how-to-set-up-appliance-hyper-v.md) Vm。
        - [實體伺服器](how-to-set-up-appliance-physical.md)。
- 若要使用相依性視覺效果，請將 [Log Analytics 工作區](../azure-monitor/platform/manage-access.md) 與 Azure Migrate 專案產生關聯：
    - 您只能在設定 Azure Migrate 設備之後附加工作區，以及探索 Azure Migrate 專案中的機器。
    - 請確定訂用帳戶中包含 Azure Migrate 專案的工作區。
    - 此工作區必須位於「美國東部」、「東南亞」或「西歐」區域。 其他區域中的工作區則無法與專案相關聯。
    - 此工作區必須位於[支援服務對應](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions)的區域中。
    - 您可以將新的或現有的 Log Analytics 工作區與 Azure Migrate 專案產生關聯。
    - 您第一次設定機器的相依性視覺效果時，會附加工作區。 Azure Migrate 專案的工作區在新增之後就無法進行修改。
    - 在 Log Analytics 中，與 Azure Migrate 相關聯的工作區會標記上遷移專案金鑰和專案名稱。

## <a name="associate-a-workspace"></a>建立工作區的關聯

1. 在探索要評量的機器之後，在 [**伺服器**  >  **Azure Migrate：伺服器評估**] 中，按一下 **[總覽**]。  
2. 在 [ **Azure Migrate：伺服器評估**] 中，按一下 [ **基本**]。
3. 在 [ **OMS 工作區**] 中，按一下 [ **需要**設定]。

     ![設定 Log Analytics 工作區](./media/how-to-create-group-machine-dependencies/oms-workspace-select.png)   

4. 在 [ **設定 OMS 工作區**] 中，指定您是否要建立新的工作區，或使用現有的工作區。
    - 您可以從遷移專案訂用帳戶中的所有工作區選取現有的工作區。
    - 您需要有工作區的讀取者存取權，才能將其關聯。
5. 如果您建立新的工作區，請選取該工作區的位置。

    ![新增工作區](./media/how-to-create-group-machine-dependencies/workspace.png)


## <a name="download-and-install-the-vm-agents"></a>下載並安裝虛擬機器代理程式

在您想要分析的每部電腦上安裝代理程式。

> [!NOTE]
> 針對受 System Center Operations Manager 2012 R2 或更新版本監視的電腦，您不需要安裝 MMA 代理程式。 服務對應與 Operations Manager 整合。 [遵循](../azure-monitor/insights/service-map-scom.md#prerequisites) 整合指引。

1. 在 [ **Azure Migrate：伺服器評估**] 中，按一下 [探索到的 **伺服器**]。
2. 針對您想要使用相依性視覺效果分析的每部機器，請在 [相依性] 欄中，按一下 [**需要代理程式安裝** **]** 。
3. 在 [相依性 **]** 頁面中，下載適用于 Windows 或 LINUX 的 MMA 和 Dependency agent。
4. 在 [ **設定 MMA 代理程式**] 底下，複製工作區識別碼和金鑰。 當您安裝 MMA 代理程式時，會需要這些。

    ![安裝代理程式](./media/how-to-create-group-machine-dependencies/dependencies-install.png)


## <a name="install-the-mma"></a>安裝 MMA

在您想要分析的每部 Windows 或 Linux 機器上安裝 MMA。

### <a name="install-mma-on-a-windows-machine"></a>在 Windows 電腦上安裝 MMA

在 Windows 電腦上安裝代理程式：

1. 按兩下下載的代理程式。
2. 在 [歡迎] 頁面中按 [下一步]。 在 [授權條款] 頁面上，按一下 [我同意] 以接受授權。
3. 在 [目的地資料夾] 中，保留或修改預設的安裝資料夾 > [下一步]。
4. 在 [代理程式安裝選項] 中，選取 [Azure Log Analytics] > [下一步]。
5. 按一下 [新增] 以新增 Log Analytics 工作區。 貼上您從入口網站複製的工作區識別碼和金鑰。 按 [下一步] 。

您可以從命令列或使用自動化方法（例如 Configuration Manager 或 [>intigua](https://www.intigua.com/intigua-for-azure-migration)）安裝代理程式。
- [了解更多](../azure-monitor/platform/log-analytics-agent.md#installation-options)有關如何使用這些方法來安裝 MMA 代理程式。
- MMA 代理程式也可以使用此[指令碼](https://go.microsoft.com/fwlink/?linkid=2104394)來安裝。
- [深入瞭解](../azure-monitor/platform/agents-overview.md#supported-operating-systems) MMA 支援的 Windows 作業系統。

### <a name="install-mma-on-a-linux-machine"></a>在 Linux 機器上安裝 MMA

若要在 Linux 電腦上安裝 MMA：

1. 使用 scp/sftp 將適當的套件組合 (x86 或 x64) 傳輸到 Linux 電腦。
2. 使用 --安裝引數安裝套件組合。

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[了解更多](../azure-monitor/platform/agents-overview.md#supported-operating-systems) MMA 支援的 Linux 作業系統清單。 

## <a name="install-the-dependency-agent"></a>安裝相依性代理程式

1. 若要在 Windows 電腦上安裝相依性代理程式，請按兩下安裝檔案，並遵循精靈的指示。
2. 若要在 Linux 電腦上安裝相依性代理程式，請使用下列命令以 root 身分安裝：

    ```sh InstallDependencyAgent-Linux64.bin```

- [深入了解](../azure-monitor/insights/vminsights-enable-hybrid.md#dependency-agent)如何使用指令碼安裝 Dependency 代理程式。
- [深入瞭解](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) Dependency agent 所支援的作業系統。


## <a name="create-a-group-using-dependency-visualization"></a>使用相依性視覺效果建立群組

現在建立用於評量的群組。 


> [!NOTE]
> 您想要將相依性視覺化的群組不應該包含超過 10 部的機器。 如果您有10部以上的電腦，請將它們分割成較小的群組。

1. 在 [ **Azure Migrate：伺服器評估**] 中，按一下 [探索到的 **伺服器**]。
2. 在 [相依性 **]** 資料行中，按一下您想要檢查的每部電腦的 [查看相依性 **]** 。
3. 在相依性對應中，您可以看到下列各項：
    - 輸入 (用戶端) 和輸出 (伺服器) TCP 連線，以及電腦之間的 TCP 連線。
    - 未安裝相依性代理程式的相依機器會依埠號碼分組。
    - 已安裝相依性代理程式的相依機器會顯示為個別的方塊。
    - 在機器內執行的進程。 展開每個機器方塊以查看處理常式。
    - 電腦屬性 (包括 FQDN、作業系統、MAC 位址) 。 按一下每個機器方塊以查看詳細資料。

4. 您可以按一下時間範圍標籤中的持續時間，以查看不同持續時間的相依性。
    - 根據預設，範圍是一小時。 
    - 您可以修改時間範圍，或指定開始和結束日期，以及持續時間。
    - 時間範圍最多可達一小時。 如果您需要較長的範圍，請使用 Azure 監視器來查詢較長期間的相依資料。

5. 找出您想要群組在一起的相依機器之後，請使用 Ctrl + 按一下來選取對應上的多部電腦，然後按一下 [ **群組機器**]。
6. 指定群組名稱。
7. 確認 Azure Migrate 已探索到相依機器。

    - 如果 Azure Migrate：伺服器評估未探索到相依機器，您就無法將它新增至群組。
    - 若要新增電腦，請再次執行探索，並確認已探索到機器。

8. 如果您需要建立此群組的評估，請選取核取方塊來建立新的群組評估。
8. 按一下 [確定] 以儲存群組。

建立群組之後，建議您在群組中的所有電腦上安裝代理程式，然後將整個群組的相依性視覺化。

## <a name="query-dependency-data-in-azure-monitor"></a>查詢 Azure 監視器中的相依性資料

您可以在與 Azure Migrate 專案相關聯的 Log Analytics 工作區中，查詢服務對應所捕捉的相依性資料。 Log Analytics 可用來撰寫和執行 Azure 監視器記錄查詢。

- [瞭解如何](../azure-monitor/insights/service-map.md#log-analytics-records) 在 Log Analytics 中搜尋服務對應資料。
- [深入瞭解如何](../azure-monitor/log-query/get-started-queries.md)  在 [log Analytics](../azure-monitor/log-query/get-started-portal.md)中撰寫記錄查詢。

執行相依性資料的查詢，如下所示：

1. 安裝代理程式之後，請移至入口網站，然後按一下 [概觀]。
2. 在 [ **Azure Migrate：伺服器評估**] 中，按一下 **[總覽**]。 按一下向下箭號以展開 [ **基本**]。
3. 在 [ **OMS 工作區**] 中，按一下工作區名稱。
3. 在 [Log Analytics 工作區] 頁面上 > 一般] 中，按一下 **[****記錄**]。
4. 撰寫您的查詢，然後按一下 [ **執行**]。

### <a name="sample-queries"></a>範例查詢

以下是一些您可以用來將相依性資料解壓縮的查詢範例。

- 您可以修改查詢以擷取慣用的資料點。
- [查看](../azure-monitor/insights/service-map.md#log-analytics-records) 相依性資料記錄的完整清單。
- [檢查](../azure-monitor/insights/service-map.md#sample-log-searches) 其他查詢範例。

#### <a name="sample-review-inbound-connections"></a>範例：檢查輸入連接

檢查一組 Vm 的輸入連線。

- 連接計量 (VMConnection) 資料表中的記錄不代表個別的實體網路連接。
- 將多個實體網路連線分組為一個邏輯連線。
- [深入瞭解](../azure-monitor/insights/service-map.md#connections) 如何在 VMConnection 中匯總實體網路連接資料。

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

#### <a name="sample-summarize-sent-and-received-data"></a>範例：摘要傳送和接收的資料

此範例摘要一組電腦之間的輸入連線所傳送和接收的資料量。

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

[建立](how-to-create-assessment.md) 群組的評量。
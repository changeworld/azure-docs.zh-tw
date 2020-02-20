---
title: 透過 Azure Migrate 使用機器相依性分組機器 | Microsoft Docs
description: 說明如何透過 Azure Migrate 服務使用機器相依性來建立評量。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 10/01/2019
ms.author: hamusa
ms.openlocfilehash: bf6798d557cb1d27030565e4706864e945de6f04
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472089"
---
# <a name="set-up-dependency-visualization-for-assessment"></a>設定評估的相依性視覺效果

本文說明如何在 Azure Migrate：伺服器評估中設定相依性對應。

相依性對應可協助您將所有要評估和遷移的機器相依性視覺化。

- 在 Azure Migrate：伺服器評估中，您可以將機器彙集在一起進行評量。 通常是您想要一起遷移的機器。
- 當您想要評估較高層級信賴的群組時，通常會使用相依性對應。
- 相依性對應可協助您在執行評量和遷移之前，先交叉檢查機器相依性。
- 對應和視覺化相依性有助於有效率地規劃遷移至 Azure。 它有助於確保不會留下任何內容，從而避免在遷移期間發生意外的中斷。
- 使用對應時，您可以探索需要一起遷移的相互相依系統。 您也可以識別執行中的系統是否仍為使用者提供服務，或者是否為解除委任而非遷移的候選。

[深入瞭解](concepts-dependency-visualization.md#how-does-it-work)相依性視覺效果。

## <a name="before-you-start"></a>開始之前

- 請確定您已[建立](how-to-add-tool-first-time.md)Azure Migrate 專案。
- 如果您已經建立專案，請確定您已[新增](how-to-assess.md)Azure Migrate：伺服器評估工具。
- 請確定您已在 Azure Migrate 中探索到您的電腦。若要這麼做，您可以設定[VMware](how-to-set-up-appliance-vmware.md)或[hyper-v](how-to-set-up-appliance-hyper-v.md)的 Azure Migrate 設備。 設備會探索內部部署機器，並將中繼資料和效能資料傳送至 Azure Migrate：伺服器評量。 [詳細資訊](migrate-appliance.md)。


**功能** | **注意**
--- | ---
可用性 | Azure Government 無法使用相依性視覺效果。
服務對應 | 相依性視覺效果使用 Azure 監視器中的服務對應解決方案。 [服務對應](../azure-monitor/insights/service-map.md)會自動探索並顯示伺服器之間的連接。
代理程式 | 若要使用相依性視覺效果，請在您想要對應的電腦上安裝下列代理程式：<br/> - [Log Analytics 代理](../azure-monitor/platform/log-analytics-agent.md)程式代理程式（先前稱為 MICROSOFT MONITORING AGENT （MMA）。<br/> - [服務對應 Dependency agent](../azure-monitor/insights/vminsights-enable-overview.md#the-microsoft-dependency-agent)。<br/><br/> 若要自動安裝代理程式，您可以使用具有 Azure Migrate 代理程式部署解決方案的部署工具（例如 Configuration Manager）。
相依性代理程式 | 審查[Dependency agent](../azure-monitor/insights/vminsights-enable-overview.md#the-microsoft-dependency-agent)對 Windows 和 Linux 的支援。<br/><br/> [深入瞭解](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples)如何使用腳本來安裝 Dependency agent。
Log Analytics 代理程式（MMA） | [深入瞭解](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration)MMA 安裝方法。<br/><br/> 針對 System Center Operations Manager 2012 R2 或更新版本監視的機器，您不需要安裝 MMA 代理程式。 服務對應與 Operations Manager 整合。 您可以使用[此處](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites)的指導方針來啟用整合。 不過要注意的是，相依性代理程式必須安裝在這些電腦上。<br/><br/> 請[參閱](../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems)Log Analytics 代理程式所支援的 Linux 作業系統。
評量群組 | 您想要將相依性視覺化的群組不應該包含超過 10 部的機器。 如果您有超過10部電腦，請將它們分割成較小的群組，以將相依性視覺化。

## <a name="associate-a-log-analytics-workspace"></a>與 Log Analytics 工作區建立關聯

若要使用相依性視覺效果，您必須將[Log Analytics 工作區](../azure-monitor/platform/manage-access.md)與 Azure Migrate 專案建立關聯。

- 您只能將工作區附加在 Azure Migrate 專案訂用帳戶中。
- 您可以附加現有的工作區，或建立一個新的。
- 您第一次設定電腦的相依性視覺效果時，會附加工作區。
- 您只能在探索 Azure Migrate 專案中的機器之後，才連接工作區。 若要這麼做，您可以設定[VMware](how-to-set-up-appliance-vmware.md)或[hyper-v](how-to-set-up-appliance-hyper-v.md)的 Azure Migrate 設備。 設備會探索內部部署機器，並將中繼資料和效能資料傳送至 Azure Migrate：伺服器評量。 [詳細資訊](migrate-appliance.md)。

附加工作區，如下所示：

1. 在 [ **Azure Migrate：伺服器評估**] 中，按一下 **[總覽**]。 如果您尚未新增伺服器評估工具，請[先執行](how-to-assess.md)此動作。
2. 在 **[總覽**] 中，按一下向下箭號以展開 [**基本**]。
3. 在 [ **OMS 工作區**] 中，按一下 [**需要**設定]。
4. 在 [**設定工作區**] 中，指定您要建立新的工作區，或使用現有的工作區：

    ![新增工作區](./media/how-to-create-group-machine-dependencies/workspace.png)

    - 為新的工作區指定名稱之後，您可以選擇將在其中建立工作區的[區域](https://azure.microsoft.com/global-infrastructure/regions/)。
    - 當您連結現有的工作區時，您能以和移轉專案相同的方式，從相同訂用帳戶中所有的可用工作區中挑選。
    - 您需要讀取器存取工作區才能附加。
    - 附加專案之後，您就無法修改與其相關聯的工作區。

## <a name="download-and-install-the-vm-agents"></a>下載並安裝虛擬機器代理程式

在您想要使用相依性對應進行視覺化的每一部內部部署機器上，下載並安裝代理程式。

1. 在 [ **Azure Migrate：伺服器評定**] 中，按一下 [探索到的**伺服器**]。
2. 針對您要使用相依性視覺效果的每部電腦，按一下 [**需要代理程式安裝**]。
3. 在電腦的 [相依性 **]** 頁面中 >**下載並安裝 MMA**、下載適當的代理程式，並如下所述進行安裝。
4. 在 [**下載並安裝相依性代理程式**] 中，下載適當的代理程式並加以安裝，如下所述。
5. 在 [**設定 MMA 代理程式**] 底下，複製 [工作區識別碼] 和 [金鑰]。 當您安裝 MMA 代理程式時，您需要這些。

### <a name="install-the-mma"></a>安裝 MMA

#### <a name="install-the-agent-on-a-windows-machine"></a>在 Windows 電腦上安裝代理程式

在 Windows 電腦上安裝代理程式：

1. 按兩下下載的代理程式。
2. 在 [歡迎] 頁面中按 [下一步]。 在 [授權條款] 頁面上，按一下 [我同意] 以接受授權。
3. 在 [目的地資料夾] 中，保留或修改預設的安裝資料夾 > [下一步]。
4. 在 [代理程式安裝選項] 中，選取 [Azure Log Analytics] > [下一步]。
5. 按一下 [新增] 以新增 Log Analytics 工作區。 貼上您從入口網站複製的工作區識別碼和金鑰。 按 [下一步]。

您可以從命令列或使用自動化方法（例如 Configuration Manager 或[Intigua](https://go.microsoft.com/fwlink/?linkid=2104196)）來安裝代理程式。 [了解更多](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent)有關如何使用這些方法來安裝 MMA 代理程式。 MMA 代理程式也可以使用此[指令碼](https://go.microsoft.com/fwlink/?linkid=2104394)來安裝。

[深入瞭解](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-windows-operating-systems)MMA 所支援的 Windows 作業系統。

#### <a name="install-the-agent-on-a-linux-machine"></a>在 Linux 電腦上安裝代理程式

在 Linux 電腦上安裝代理程式：

1. 使用 scp/sftp 將適當的套件組合 (x86 或 x64) 傳輸到 Linux 電腦。
2. 使用 --安裝引數安裝套件組合。

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[了解更多](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-linux-operating-systems) MMA 支援的 Linux 作業系統清單。 

### <a name="install-the-dependency-agent"></a>安裝相依性代理程式
1. 若要在 Windows 電腦上安裝相依性代理程式，請按兩下安裝檔案，並遵循精靈的指示。
2. 若要在 Linux 電腦上安裝相依性代理程式，請使用下列命令以 root 身分安裝：

    ```sh InstallDependencyAgent-Linux64.bin```

[深入了解](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-hybrid-cloud#installation-script-examples)如何使用指令碼安裝 Dependency 代理程式。

[深入瞭解](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#supported-operating-systems)Dependency agent 所支援的作業系統。


## <a name="create-a-group-using-dependency-visualization"></a>使用相依性視覺效果建立群組

1. 在 [ **Azure Migrate：伺服器評定**] 中，按一下 [探索到的**伺服器**]。
2. 在 [相依性 **]** 資料行中，按一下您想要檢查之每部機器的 [查看相依性 **]** 。
3. 在 [相依性對應] 上，您可以看到下列各項：
    - 電腦的輸入（用戶端）和輸出（伺服器） TCP 連線。
    - 未安裝相依性代理程式的相依機器會依埠號碼分組。
    - 已安裝 dependency agent 的相依機器會顯示為不同的方塊。
    - 在機器內執行的進程。 展開每個電腦方塊以查看處理常式。
    - 電腦屬性（包括 FQDN、作業系統、MAC 位址）。 按一下每個機器方塊以查看詳細資料。

4. 您可以按一下時間範圍標籤中的持續時間，以查看不同持續時間的相依性。 根據預設，範圍是一小時。 您可以修改時間範圍，或指定開始和結束日期，以及持續時間。

    > [!NOTE]
    > 時間範圍最長可達一小時。 如果您需要較長的範圍，請使用 Azure 監視器來查詢相依資料的時間較長。

5. 識別要群組在一起的相依機器之後，請使用 Ctrl + 按一下來選取地圖上的多部機器，然後按一下 [**群組機器**]。
6. 指定群組名稱。
7. 確認 Azure Migrate 已探索到相依機器。

    - 如果 Azure Migrate：伺服器評估找不到相依電腦，您就無法將它新增至群組。
    - 若要新增電腦，請再次執行探索，並確認已探索到機器。

8. 如果您需要建立此群組的評估，請選取核取方塊來建立新的群組評估。
8. 按一下 [確定] 以儲存群組。

建立群組之後，建議您在群組中的所有機器上安裝代理程式，然後將整個群組的相依性視覺化。

## <a name="query-dependency-data-in-azure-monitor"></a>Azure 監視器中的查詢相依性資料

您可以在與您的 Azure Migrate 專案相關聯的 Log Analytics 工作區中，查詢服務對應所捕捉的相依性資料。 Log Analytics 是用來撰寫和執行 Azure 監視器記錄查詢。

- [瞭解如何](../azure-monitor/insights/service-map.md#log-analytics-records)在 Log Analytics 中搜尋服務對應的資料。
- [取得](../azure-monitor/log-query/get-started-queries.md)在[log Analytics](../azure-monitor/log-query/get-started-portal.md)中撰寫記錄查詢的總覽。

執行相依性資料的查詢，如下所示：

1. 安裝代理程式之後，請移至入口網站，然後按一下 [概觀]。
2. 在 [ **Azure Migrate：伺服器評估**] 中，按一下 **[總覽**]。 按一下向下箭號以展開 [**基本**]。
3. 在 [ **OMS 工作區**] 中，按一下工作區名稱。
3. 在 [Log Analytics 工作區] 頁面上 > **[一般**]，按一下 [**記錄**]。
4. 撰寫查詢，然後按一下 [**執行**]。

### <a name="sample-queries"></a>範例查詢

我們提供可供您用來解壓縮相依性資料的數個範例查詢。

- 您可以修改查詢以擷取慣用的資料點。
- [查看](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records)相依性資料記錄的完整清單。
- [查看](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches)其他範例查詢。

#### <a name="sample-review-inbound-connections"></a>範例：審查輸入連接

檢查一組 Vm 的輸入連線。

- 連接計量（VMConnection）資料表中的記錄不代表個別的實體網路連線。
- 將多個實體網路連線分組為一個邏輯連線。
- [深入瞭解](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections)如何在 VMConnection 中匯總實體網路連接資料。

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

#### <a name="sample-summarize-sent-and-received-data"></a>範例：摘要已傳送和接收的資料

這個範例會摘要說明一組機器之間的輸入連接所傳送和接收的資料量。

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

[建立](how-to-create-assessment.md)群組的評量。

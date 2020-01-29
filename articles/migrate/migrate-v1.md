---
title: 使用舊版的 Azure Migrate
description: 說明如何使用舊版的 Azure Migrate。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 11/19/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 5d3dc951c8cb2948a4cd0b9d9f5c2a9b213c6e7e
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514981"
---
# <a name="work-with-the-previous-version-of-azure-migrate"></a>使用舊版的 Azure Migrate

此文章提供使用舊版 Azure Migrate 的相關資訊。


Azure Migrate 服務有兩個版本：

- **目前的版本**：使用此版本來建立 Azure Migrate 專案、探索內部部署電腦，以及協調評量與移轉。 [深入了解](whats-new.md)此版本的新功能。
- **先前版本**：如果您使用的是舊版 Azure Migrate (僅支援內部部署 VMware VM 的評量)，則您現在應該使用目前的版本。 如果您仍然需要使用在舊版中建立的 Azure Migrate 專案，以下是您可以/不可執行的操作：
    - 您無法再建立移轉專案。
    - 我們建議您不要執行新的探索。
    - 您仍然可以存取現有的專案。
    - 您仍然可以執行評量。
    

## <a name="upgrade-between-versions"></a>版本之間的升級

您無法將舊版中的專案或元件升級至新版本。 您需要[建立新的 Azure Migrate 專案](how-to-add-tool-first-time.md)，並將評量與移轉工具新增到其中。

## <a name="find-projects-from-previous-version"></a>尋找舊版的專案

尋找舊版的專案，如下所示：

1. 在 Azure 入口網站 > [所有服務]  中，搜尋並選取 [Azure Migrate]  。 
2. 在 Azure Migrate 儀表板上，會有可以存取舊 Azure Migrate 專案的通知與連結。
3. 按一下連結以開啟 v1 專案。


## <a name="create-an-assessment"></a>建立評估

在入口網站中探索到 VM 之後，您可以將它們分組並建立評量。

- 在入口網站中探索到 VM 後，您就可以立即建立內部評量。
- 針對以效能為基礎的評量，建議您至少先等候一天的時間，再建立以效能為基礎的評量，以取得可靠的大小建議。

建立評量，如下所示：

1. 在專案的 [概觀]  頁面中，按一下 [+建立評估]  。
2. 按一下 [檢視全部]  來檢閱評估屬性。
3. 建立群組，並指定群組名稱。
4. 選取您想要新增至群組的機器。
5. 按一下 [建立評估]  以建立群組和評估。
6. 建立評估之後，在 [概觀]   > [儀表板]  中檢視該評估。
7. 按一下 [匯出評估]  ，將其下載為 Excel 檔案。

如果您想要以最新的效能資料更新現有的評量，則可以對評量使用 [重新計算]  命令來更新它。

## <a name="review-an-assessment"></a>檢閱評量 

評量有三個階段：

- 評量會從適用性分析開始，以確定機器是否與 Azure 相容。
- 調整大小估計。
- 每月成本預估。

機器必須先通過前一個評量，才可前往下一階段。 例如，如果機器未通過適用性檢查，就會標示為不適合 Azure，而不會再去計算大小與成本。


### <a name="review-azure-readiness"></a>檢閱 Azure 移轉整備程度

評量中的 Azure 移轉整備程度檢視會顯示每部虛擬機器的整備狀態。

**整備程度** | **State** | **詳細資料**
--- | --- | ---
可供 Azure 使用 | 無相容性問題。 機器可以依原樣遷移到 Azure，它將會在 Azure 中開機並包含完整的 Azure 支援。 | 對於已準備好的 VM，Azure Migrate 會建議這些 VM 在 Azure 中該有的大小。
可有條件地供 Azure 使用 | 該機器可能可以在 Azure 中開機，但可能沒有完整的 Azure 支援。 例如，Azure 中不支援使用較舊版本 Windows Server 的機器。 | Azure Migrate 會說明整備程度問題，並提供補救步驟。
未備妥供 Azure 使用 |  VM 將無法在 Azure 中開機。 例如，如果 VM 的磁碟超過 4 TB，便無法在 Azure 上託管。 | Azure Migrate 會說明整備程度問題，並提供補救步驟。
移轉整備程度未知 | Azure Migrate 無法識別 Azure 整備程度，通常是因為資料無法使用。


#### <a name="azure-vm-properties"></a>Azure VM 屬性
整備程度考慮了許多 VM 屬性，以識別 VM 是否可以在 Azure 中執行。


**屬性** | **詳細資料** | **整備程度**
--- | --- | ---
**開機類型** | 支援 BIOS。 不支援 UEFI。 | 如果開機類型為 UEFI，便已有條件地就緒。
**核心** | 機器核心 <= Azure VM 支援的核心數目上限 (128)。<br/><br/> 如果效能歷程記錄可用，則 Azure Migrate 會將已使用的核心數納入考量。<br/>如果評量設定已指定緩和因數，則會將使用的核心數目乘以緩和因數。<br/><br/> 如果沒有效能記錄，Azure Migrate 會使用配置的核心，而不套用緩和因數。 | 小於或等於限制便就緒。
**記憶體** | 機器的記憶體大小 <= Azure VM 的記憶體上限 (Azure M 系列 Standard_M128m 上為 3892 GB &nbsp;<sup>2</sup>)。 [深入了解](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)。<br/><br/> 如果效能歷程記錄可用，Azure Migrate 會將已使用的記憶體數納入考量。<br/><br/>如果已指定緩和因數，則會將使用的記憶體乘以緩和因數。<br/><br/> 如果沒有記錄，則會使用配置的記憶體，而不套用緩和因數。<br/><br/> | 在限制內便就緒。
**存放磁碟** | 已配置的磁碟大小必須小於或等於 4 TB (4096 GB)。<br/><br/> 附加至機器的磁碟數目必須小於或等於 65，作業系統磁碟亦包含在內。 | 在限制內便就緒。
**網路功能** | 機器必須附加 32 個以內的網路介面卡。 | 在限制內便就緒。

#### <a name="guest-operating-system"></a>客體作業系統

隨著 VM 屬性，Azure Migrate 也會查看內部部署 VM 的客體 OS，以識別 VM 是否可以在 Azure 中執行。

- Azure Migrate 會考量 vCenter Server 中指定的 OS。
- 由 Azure Migrate 探索是應用裝置為基礎，因此無法驗證 VM 內執行的作業系統是否與 vCenter Server 中所指定的相同。

會使用下列邏輯。

**作業系統** | **詳細資料** | **整備程度**
--- | --- | ---
Windows Server 2016 與所有 SP | Azure 提供完整支援。 | 可供 Azure 使用
Windows Server 2012 R2 與所有 SP | Azure 提供完整支援。 | 可供 Azure 使用
Windows Server 2012 與所有 SP | Azure 提供完整支援。 | 可供 Azure 使用
Windows Server 2008 R2 與所有 SP | Azure 提供完整支援。| 可供 Azure 使用
Windows Server 2008 (32 位元和 64 位元) | Azure 提供完整支援。 | 可供 Azure 使用
Windows Server 2003、2003 R2 | 不支援，需要[自訂支援合約 (CSA)](https://aka.ms/WSosstatement) 才能獲得 Azure 支援。 | 可有條件地供 Azure 使用，在移轉到 Azure 之前，請考慮升級作業系統。
Windows 2000、98、95、NT、3.1、MS-DOS | 不支援。 機器可能可以在 Azure 中開機，但 Azure 不會提供 OS 支援。 | 可有條件地供 Azure 使用，建議在移轉到 Azure 之前升級作業系統。
Windows Client 7、8 及 10 | Azure [僅對 Visual Studio 訂用帳戶](https://docs.microsoft.com/azure/virtual-machines/windows/client-images)提供支援。 | 可有條件地供 Azure 使用
Windows 10 專業版桌面 | Azure 對[多租用戶主機權限](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment)提供支援。 | 可有條件地供 Azure 使用
Windows Vista、XP Professional | 不支援。 機器可能可以在 Azure 中開機，但 Azure 不會提供 OS 支援。 | 可有條件地供 Azure 使用，建議在移轉到 Azure 之前升級作業系統。
Linux | Azure 認同這些 [Linux 作業系統](../virtual-machines/linux/endorsed-distros.md)。 其他 Linux 作業系統可能可以在 Azure 中開機，但我們建議您在移轉至 Azure 之前，將作業系統升級為認可的版本。 | 如果版本受到認同，則可供 Azure 使用。<br/><br/>如果版本未受到認同，則有條件地可供使用。
其他作業系統<br/><br/> 例如，Oracle Solaris、Apple Mac OS 等、FreeBSD 等。 | Azure 並未認可這些作業系統。 機器可能可以在 Azure 中開機，但 Azure 不會提供作業系統支援。 | 可有條件地供 Azure 使用，建議在移轉到 Azure 之前安裝支援的作業系統。  
在 vCenter Server 中作業系統指定為**其他** | Azure Migrate 無法在此情況下識別作業系統。 | 整備程度未知。 請確保 Azure 支援 VM 內執行的作業系統。
32 位元作業系統 | 機器可能可以在 Azure 中開機，但 Azure 可能不會提供完整支援。 | 可有條件地供 Azure 使用，在移轉到 Azure 之前，請考慮將機器的作業系統從 32 位元作業系統升級至 64 位元作業系統。


### <a name="review-sizing"></a>檢查大小

 Azure Migrate 大小建議取決於評量屬性中指定的大小調整準則。

- 如果大小調整是以效能為基礎，則大小調整建議會考量 VM (CPU 與記憶體) 與磁碟 (IOPS 與輸送量) 的效能歷程記錄。
- 如果大小調整準則是「如內部部署」，則 Azure 中的大小建議是以 VM 內部部署的大小為基礎。 磁碟大小調整是以評量屬性中指定的儲存體類型為基礎 (預設為進階磁碟)。 Azure Migrate 不會考量 VM 和磁碟的效能資料。

### <a name="review-cost-estimates"></a>檢閱成本估計

成本估計會顯示在 Azure 中執行 VM 的計算和儲存總成本，以及每部電腦的詳細資訊。

- 在計算成本估計時，會使用 VM 機器的大小建議與其磁碟和評量屬性。
- 系統會彙總群組內所有 VM 之計算和儲存的每月預估成本。
- 成本估計用於執行內部部署 VM 以作為 Azure 基礎結構即服務 (IaaS) VM。 Azure Migrate 不會考慮平台即服務 (PaaS) 或軟體即服務 (SaaS) 成本。

### <a name="review-confidence-rating-performance-based-assessment"></a>檢閱信賴評等 (以效能為基礎的評量)

每個以效能為基礎的評量都與信賴評等相關聯。

- 信賴評等的範圍介於一顆星到五顆星之間 (一顆星是最低的，五顆星是最高的)。
- 會根據計算評量所需的資料點可用性，為每個評量指派信賴評等。
- 評量的信賴評等可協助您評估 Azure Migrate 提供的大小建議之可靠性。
- 信賴評等不適用於「現狀」的內部部署評量。

針對以效能為基礎的大小調整，Azure Migrate 需要下列項目：
- CPU 的使用量資料。
- VM 記憶體資料。
- 針對連結至 VM 的每個磁碟，它都需要磁碟 IOPS 與輸送量資料。
- 針對連結至 VM 的每張網路介面卡，Azure Migrate 都需要網路輸入/輸出。
- 如果上述任一項目都無法使用，則大小建議 (以及信賴評等) 可能不可靠。


根據可用資料點的百分比，下表摘要說明了可能的信賴評等。

**資料點的可用性** | **信賴評等**
--- | ---
0%-20% | 1 顆星
21%-40% | 2 顆星
41%-60% | 3 顆星
61%-80% | 4 顆星
81%-100% | 5 顆星


#### <a name="assessment-issues-affecting-confidence-ratings"></a>影響信賴評等的評量問題

由於許多原因，評量可能沒有所有可用資料點：

- 您未在評估期間分析您的環境。 例如，如果您建立的評量將效能持續時間設定為 1 天，您必須至少等待一天後再開始探索，才能收集到所有資料點。
- 部分 VM 在評量計算期間關閉。 如果有任何 VM 電源在某段期間內關閉，Azure Migrate 就無法收集該期間的效能資料。
- 部分 VM 是在評量計算期間內建立的。 例如，如果您使用上個月的效能歷程記錄建立評量，但在一周前於環境中建立了多部 VM，則新 VM 的效能歷程記錄將不會持續整段期間。

> [!NOTE]
> 如果任何評量的信賴評等低於五顆星，請至少等待一天，讓設備分析環境，然後重新計算評量。 否則，以效能為基礎的大小調整可能不可靠。 如果您不想重新計算，建議您變更評量屬性來切換至內部部署大小調整。



## <a name="create-groups-using-dependency-visualization"></a>使用相依性視覺效果建立群組

除了手動建立群組之外，還可以使用相依性視覺效果建立群組。
- 在執行評量前，若想要透過交叉檢查機器相依性的方式，對信賴等級較高的群組進行評估，通常會使用此方法。
- 相依性視覺效果可協助您有效地規劃移轉至 Azure。 它可協助您確保不會遺留任何項目，且當您在移轉至 Azure 時，不會發生意外的中斷。
- 您可以探索所有需要一起移轉的交互相依系統，以及識別執行中的系統是否仍為使用者提供服務，還是應該解除委任而非移轉。
- Azure Migrate 使用了 Azure 監視器中的 [服務對應] 解決方案來啟用相依性視覺效果。

> [!NOTE]
> 在 Azure Government 中無法使用相依性視覺效果。

若要設定相依性視覺效果，請將 Log Analytics 工作區與 Azure Migrate 專案建立關聯、在您要將相依性視覺化的電腦上安裝代理程式，然後使用相依性資訊建立群組。 



### <a name="associate-a-log-analytics-workspace"></a>與 Log Analytics 工作區建立關聯

若要使用相依性視覺效果，請將 Log Analytics 工作區與移轉專案建立關聯。 您只能在建立移轉專案的相同訂用帳戶中，建立或連結工作區。

1. 若要將 Log Analytics 工作區連結至專案，請在 [概觀]  > [基本資訊]  中，按一下 [需要設定]  。
2. 您可以建立新的工作區，或是連結現有的工作區：
  - 若要建立新的工作區，請指定名稱。 工作區會在與移轉專案相同之 [Azure 地理區](https://azure.microsoft.com/global-infrastructure/geographies/)的區域中建立。
  - 當您連結現有的工作區時，您能以和移轉專案相同的方式，從相同訂用帳戶中所有的可用工作區中挑選。 系統只會列出那些在[支援的服務對應區域](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)中建立的工作區。 若要連結工作區，請確定您有該工作區的「讀者」存取權。

> [!NOTE]
> 您無法變更與移轉專案相關聯的工作區。

### <a name="download-and-install-vm-agents"></a>下載並安裝 VM 代理程式

在您設定工作區之後，您要在待評估的每部內部部署機器上下載並安裝代理程式。 此外，如果您的機器沒有網際網路連線，則需要下載並安裝 [Log Analytics 閘道](../azure-monitor/platform/gateway.md)。

1. 在 [概觀]  中，按一下 [管理]   > [機器]  ，並選取所需的機器。
2. 在 [相依性]  資料行中，按一下 [安裝代理程式]  。
3. 在 [相依性]  頁面上，在您要進行評量的每部 VM 上下載並安裝 Microsoft Monitoring Agent (MMA) 與相依性代理程式。
4. 複製工作區識別碼與金鑰。 在內部部署機器上安裝 MMA 時，需要用到這些識別碼與金鑰。

> [!NOTE]
> 若要自動安裝代理程式，您可以使用部署工具 (例如 Configuration Manager) 或合作夥伴工具 [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration)，它提供適用於 Azure Migrate 的代理程式部署解決方案。


#### <a name="install-the-mma-agent-on-a-windows-machine"></a>在 Windows 電腦上安裝 MMA 代理程式

在 Windows 電腦上安裝代理程式：

1. 按兩下下載的代理程式。
2. 在 [歡迎]  頁面中按 [下一步]  。 在 [授權條款]  頁面上，按一下 [我同意]  以接受授權。
3. 在 [目的地資料夾]  中，保留或修改預設的安裝資料夾 > [下一步]  。
4. 在 [代理程式安裝選項]  中，選取 [Azure Log Analytics]   > [下一步]  。
5. 按一下 [新增]  以新增 Log Analytics 工作區。 貼上您從入口網站複製的工作區識別碼和金鑰。 按 [下一步]  。

您可以從命令列或使用自動化方法 ( 例如 Configuration Manager ) 來安裝代理程式。 [了解更多](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent)有關如何使用這些方法來安裝 MMA 代理程式。

#### <a name="install-the-mma-agent-on-a-linux-machine"></a>在 Linux 電腦上安裝 MMA 代理程式

在 Linux 電腦上安裝代理程式：

1. 使用 scp/sftp 將適當的套件組合 (x86 或 x64) 傳輸到 Linux 電腦。
2. 使用 --安裝引數安裝套件組合。

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[了解更多](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) MMA 支援的 Linux 作業系統清單。

### <a name="install-the-mma-agent-on-a-machine-monitored-by-operations-manager"></a>將 MMA 代理程式安裝在由 Operations Manager 監視的電腦上

對於受 System Center Operations Manager 2012 R2 或更新版本監視的電腦，不需要安裝 MMA 代理程式。 服務對應與 Operations Manager MMA 整合，以收集必要的相依性資料。 [深入了解](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites)。 必須安裝相依性代理程式。

### <a name="install-the-dependency-agent"></a>安裝相依性代理程式

1. 若要在 Windows 電腦上安裝相依性代理程式，請按兩下安裝檔案，並遵循精靈的指示。
2. 若要在 Linux 電腦上安裝相依性代理程式，請使用下列命令以 root 身分安裝：

    ```sh InstallDependencyAgent-Linux64.bin```

- 深入了解 Windows 與 Linux 作業系統的[相依性代理程式支援](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems)。
- [深入了解](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples)如何使用指令碼安裝 Dependency 代理程式。

>[!NOTE]
> 若適用於 VM 的 Azure 監視器文章中提供了部署相依性代理程式時所需的系統必要條件和方法概觀，則這些文章也適用於「服務對應」解決方案。

### <a name="create-a-group-with-dependency-mapping"></a>建立具有相依性對應的群組

1. 安裝代理程式之後，請移至入口網站，然後按一下[管理]   > [機器]  。
2. 搜尋安裝代理程式的機器。
3. 機器的 [相依性]  資料行現在應會顯示為**檢視相依性**。 按一下資料行以檢視機器的相依性。
4. 機器的相依性對應會顯示下列詳細資料：
    - 往返機器的輸入 (用戶端) 和輸出 (伺服器) TCP 連線
        - 未安裝 MMA 與相依性代理程式的相依機器會依連接埠號碼分組。
        - 已安裝 MMA 和相依性代理程式的相依機器會以不同的方塊顯示。
    - 在機器內執行的處理序，您可以展開每個機器方塊，以檢視處理序
    - 系統會顯示機器屬性，包括 FQDN、作業系統、MAC 位址。 您可以按一下每個機器方塊來檢視詳細資料。

4. 您可以按一下時間範圍標籤中的持續時間，以檢視不同持續期間的相依性。 根據預設，範圍是一小時。 您可以修改時間範圍，或指定開始和結束日期，以及持續時間。

   > [!NOTE]
   >    支援最多一小時的時間範圍。 使用 Azure 監視器可在較長的持續期間內[查詢相依性資料](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) \(部分機器翻譯\)。

5. 找出您需要劃為同一群組的相依機器後，請使用 Ctrl + 按一下來選取對應中的多部機器，然後按一下 [群組機器]  。
6. 指定群組名稱。 確認 Azure Migrate 已探索到相依機器。

    > [!NOTE]
    > 如果 Azure Migrate 未探索到相依機器，您就無法將它新增至群組。 若要將這類機器新增至群組，您必須使用 vCenter Server 中的正確範圍，再次執行探索程序，並確定 Azure Migrate 已探索到機器。  

7. 如果您需要建立此群組的評估，請選取核取方塊來建立新的群組評估。
8. 按一下 [確定]  以儲存群組。

一旦建立群組之後，建議您在群組的所有機器上安裝代理程式，並將整個群組的相依性視覺化來調整群組。

## <a name="query-dependency-data-from-azure-monitor-logs"></a>從 Azure 監視器記錄查詢相依性資料

服務對應擷取的相依性資料可供在與 Azure Migrate 專案相關聯的 Log Analytics 工作區中查詢。 [深入了解](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records)可在 Azure 監視器記錄中查詢的服務對應資料表。 

執行 Kusto 查詢：

1. 安裝代理程式之後，請移至入口網站，然後按一下 [概觀]  。
2. 在 [概觀]  中，移至專案的 [Essentials]  區域，然後按一下 [OMS 工作區]  旁提供的工作區名稱。
3. 在 Log Analytics 工作區頁面上，按一下 [一般]   > [記錄]  。
4. 撰寫您的查詢以使用 Azure 監視器記錄收集相依性資料。 在下一節中尋找範例查詢。
5. 按一下 [執行] 以執行查詢。 

[深入了解](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)如何撰寫 Kusto 查詢。 

### <a name="sample-azure-monitor-logs-queries"></a>範例 Azure 監視器記錄查詢

以下是您可以用來擷取相依性資料的範例查詢。 您可以修改查詢以擷取慣用的資料點。 您可以在[這裡](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records)取得相依性資料記錄中的完整欄位清單。 在[這裡](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches)尋找更多範例查詢。

#### <a name="summarize-inbound-connections-on-a-set-of-machines"></a>摘述一組電腦上的輸入連接

連線計量 VMConnection 表中的記錄不代表個別實體網路連線。 將多個實體網路連線分組為一個邏輯連線。 [深入了解](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections)有關實體網路連線資料如何匯總到 VMConnection 中的單一邏輯記錄。 

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

#### <a name="summarize-volume-of-data-sent-and-received-on-inbound-connections-between-a-set-of-machines"></a>摘述一組機器之間的輸入連接上傳送及接收的資料量

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
[深入了解](migrate-services-overview.md)最新版本的 Azure Migrate。

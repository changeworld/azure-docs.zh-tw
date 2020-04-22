---
title: Azure 服務結構發佈
description: Azure 服務結構的發行說明。 包括有關服務結構的最新功能和改進的資訊。
ms.date: 06/10/2019
ms.topic: conceptual
hide_comments: true
hideEdit: true
ms.openlocfilehash: 3e0f6c78b6e5dd066cbfbac6805bb3c42068e66a
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729588"
---
# <a name="service-fabric-releases"></a>服務交換矩陣版本

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">容容排除指南</a> 
| <a href="https://github.com/Azure/service-fabric-issues" target="blank">問題 追蹤</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">支援選項</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">支援版本</a> 
| <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">代碼範例</a>

本文提供有關服務結構運行時和 SDK 的最新版本和更新的詳細資訊。

## <a name="whats-new-in-service-fabric"></a>服務交換矩陣中的新增功能

### <a name="service-fabric-71"></a>服務結構 7.1
由於當前的 COVID-19 危機,並考慮到客戶面臨的挑戰,我們提供 7.1,但不會自動升級設置為接收自動升級的群集。 我們將暫停自動升級,直到另行通知,以確保客戶可以在最適合時應用升級,以避免意外中斷。

您可以通過[Azure 門戶](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-version-azure#upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal)或透過[Azure 資源管理員部署](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-version-azure#set-the-upgrade-mode-using-a-resource-manager-template)更新到 7.1。

啟用了自動升級的服務結構群集將在我們恢復標準推出過程后自動開始接收 7.1 更新。 在[服務面料科技社區網站](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)開始推出標準之前,我們會再提供一個公告。
我們還發佈了主要版本的支援日期結束的更新,從 6.5 到 7.1[在這裡](https://docs.microsoft.com/azure/service-fabric/service-fabric-versions#supported-versions)。 

## <a name="what-is-new-in-service-fabric-71"></a>什麼是新的服務內交換矩陣 7.1?
我們很高興地宣佈下一個版本的服務交換矩陣。 此版本載入了關鍵功能和改進。 下面突顯了一些關鍵功能:
## <a name="key-announcements"></a>主要公告
- **General Availability**[**服務結構應用程式服務結構管理識別的**一般可用性](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity)
- [**支援 Ubuntu 1804**](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-create-vnet-and-linux-cluster)
 - [**預覽:VMSS 臨時作業系統磁碟支援**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-azure-deployment-preparation#use-ephemeral-os-disks-for-virtual-machine-scale-sets)*: 臨時 OS 磁碟是在本地虛擬機上創建的存儲,而不是保存到遠端 Azure 儲存。 建議所有 Service Fabric 節點類型(主節點和輔助節點)使用它們,因為與傳統持久性 OS 磁碟相比,臨時 OS 磁碟:
      -  減少作業系統磁碟的讀取/寫入延遲
      -  實現更快的重置/重新映像節點管理操作
      -  降低總體成本(磁碟是免費的,不會產生額外的儲存成本)
- [**支援 支援主題的通用名稱宣告服務結構應用程式的服務終結點憑證應用程式**](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-manifest-resources)。
- [**對容器化服務的健康探測的支援**](https://docs.microsoft.com/azure/service-fabric/probes-codepackage):支援容器化應用的活度探測機制。 Liveness 探測有助於宣佈容器化應用程式的真實性,當它們不及時回應時,將導致重新啟動。 
- 支援[容器與](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-containers-overview)[來賓可執行](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-guest-executables-introduction)應用程式的[**初始化程式 。**](https://docs.microsoft.com/azure/service-fabric/initializer-codepackages) 這允許按指定順序執行代碼包(例如容器)來執行服務包初始化。
- **Fabric 觀察員和群集觀察者**是捕獲與 SF 群集的不同方面相關的服務結構遙測的無狀態應用程式。 這兩個應用程式都已準備好部署到 Windows 生產群集,以便通過應用程式見解、事件源和日誌分析的已實施支援來捕獲豐富的遙測數據。
    - [**Fabric觀察員 (FO) 2.0**](https://github.com/microsoft/service-fabric-observer)- 在所有節點上運行,生成運行狀況事件,在達到使用者配置的資源使用閾值時發出遙測。 此版本包含多個跨監視、數據管理、運行狀況事件詳細資訊、結構化遙測的增強功能。
     - [**群集觀察者 (CO) 1.1**](https://github.com/microsoft/service-fabric-observer/tree/master/ClusterObserver) - 在一個節點上運行,捕獲群集級運行狀況遙測。 在此版本中,ClusterObserver 還會監視節點狀態,並在節點關閉/禁用/禁用的時間超過使用者指定時間段時發出遙測。

### <a name="improve-application-life-cycle-experience"></a>改善應用程式生命週期體驗

- **[預覽:請求耗盡](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-advanced#avoid-connection-drops-during-planned-downtime-of-stateless-services)**:在計劃的服務維護期間(如服務升級或節點停用)期間,您希望允許服務正常耗盡連接。 此功能在服務配置中添加實例關閉延遲持續時間。 在計劃操作期間,SF 將從發現中刪除服務的位址,然後等待此持續時間,然後再關閉服務。
- **[自動子群集檢測和平衡](https://docs.microsoft.com/azure/service-fabric/cluster-resource-manager-subclustering )**:當具有不同放置約束的服務具有通用[負載指標](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-metrics)時,就會發生子群集。 如果不同節點集上的負載差異很大,則 Service Fabric 群集資源管理器認為群集是不平衡的,即使由於放置限制,群集具有最佳平衡。 因此,它嘗試重新平衡群集,可能導致不必要的服務移動(因為"不平衡"無法顯著改善)。 從此版本開始,群集資源管理器現在將嘗試自動檢測這些類型的配置,並瞭解何時可以通過移動來修復不平衡,以及何時應該讓事情獨立,因為無法進行實質性的改進。  
- [**輔助副本的不同行動成本**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-movement-cost):我們引入了新的行動成本值「非常高」,在某些情況下提供了額外的靈活性,以定義是否應將單獨的行動成本用於輔助副本。
- 支援容器化應用[**的活度探測**](https://docs.microsoft.com/azure/service-fabric/probes-codepackage )機制。 Liveness 探測有助於宣佈容器化應用程式的真實性,當它們不及時回應時,將導致重新啟動。
- [**執行到完成/一次服務**](https://docs.microsoft.com/azure/service-fabric/run-to-completion)**

### <a name="image-store-improvements"></a>影像存放改進
 - 預設情況下,Service Fabric 7.1 使用**自訂傳輸來保護節點之間的檔案傳輸**。 對 SMB 檔共享的依賴項將從版本 7.1 中刪除。 安全的 SMB 檔共用仍然存在於包含映像儲存服務副本的節點上,供客戶選擇退出預設值,以及升級和降級到舊版本。
       
 ### <a name="reliable-collections-improvements"></a>可靠的集合改進

- [**在記憶體中,僅儲存支援狀態服務使用可靠集合**](https://docs.microsoft.com/azure/service-fabric/service-fabric-work-with-reliable-collections#volatile-reliable-collections): 易失性可靠集合允許將數據保存到磁碟,以持久化,以抵禦大規模中斷,可用於工作負載,例如,可以容忍偶爾的數據丟失。基於[易失性可靠集合的限制和限制](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-reliable-collections-guidelines#volatile-reliable-collections),我們建議對於不需要持久性的工作負載,對於處理罕見仲裁丟失情況的服務,我們建議這樣做。
- [**預覽:服務結構備份資源管理員**](https://github.com/microsoft/service-fabric-backup-explorer):為了簡化服務交換矩陣狀態應用程式的可靠集合備份的管理,服務結構備份資源管理員使用戶能夠
    - 審核和審查可靠集合的內容,
    - 將目前狀態更新為一致的檢視
    - 建立可靠集合的目前快照的備份
    - 修復資料損壞
                 
### <a name="service-fabric-71-releases"></a>服務結構 7.1 版本
| 發行日期 | 版本 | 其他資訊 |
|---|---|---|
| 2020年4月20日 | [Azure 服務結構 7.1](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-release/ba-p/1311373)  | [版本資訊](https://github.com/microsoft/service-fabric/tree/master/release_notes/Service-Fabric-71-releasenotes.md)|


### <a name="service-fabric-70"></a>服務結構 7.0

Azure 服務結構 7.0 現已可用! 您將能夠透過 Azure 門戶或透過 Azure 資源管理員部署更新到 7.0。 由於客戶對假期期間發佈版本的反饋,我們不會開始自動更新群集,這些群集設置為接收自動升級,直到 1 月。
在一月份,我們將恢復標準推出過程,啟用自動升級的群集將自動開始接收 7.0 更新。 我們將在推出開始前提供另一個公告。
我們還將更新計劃發佈日期,以表明我們考慮到了此政策。 在此處查看我們未來[發佈時程表](https://github.com/Microsoft/service-fabric/#service-fabric-release-schedule)的更新。
 
這是服務結構的最新版本,並載入了關鍵功能和改進。

### <a name="key-announcements"></a>主要公告
 - [**KeyVault 對應用程式機密的支援(預覽):**](https://docs.microsoft.com/azure/service-fabric/service-fabric-keyvault-references)已啟用[託管標識](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity)的服務結構應用程式現在可以直接引用密鑰保管庫密鑰網址 作為環境變數、應用程式參數或容器儲存庫認證。 Service Fabric 將使用應用程式的託管標識自動解析機密。 
     
- **改進了無狀態服務的升級安全性**:為了保證應用程式升級期間的可用性,我們引入了新的設定,[以定義認為可用的無狀態服務的最小實例數](https://docs.microsoft.com/dotnet/api/system.fabric.description.statelessservicedescription?view=azure-dotnet)。 以前,此值對於所有服務都是 1,並且不可更改。 通過這種新的每服務安全檢查,您可以確保服務在應用程式升級、群集升級和其他依賴於 Service Fabric 的健康和安全檢查的維護期間保留最少的向上實例數。
  
- [**使用者服務的資源限制**](https://docs.microsoft.com/azure/service-fabric/service-fabric-resource-governance#enforcing-the-resource-limits-for-user-services):使用者可以為節點上的使用者服務設置資源限制,以防止出現資源耗盡服務結構系統服務等情況。 
  
- [**複本型態的服務行動成本非常高**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-movement-cost)。 僅當群集中存在無法以任何其他方式修復的約束衝突時,才會移動具有極高移動成本的副本。 有關"非常高"行動成本的使用是否合理以及其他注意事項,請參閱文檔以獲取更多資訊。
  
-  **其他群集安全檢查**:在此版本中,我們引入了可配置的種子節點仲裁安全檢查。 這允許您自定義在群集生命週期和管理方案中必須可用的種子節點數。 將群集帶到配置的值以下的操作將被阻止。 今天,默認值始終是種子節點的仲裁,例如,如果您有 7 個種子節點,默認情況下將低於 5 個種子節點的操作被阻止。 通過此更改,您可以創建最小安全值 6,這樣一次只能關閉一個種子節點。
   
- 添加了對[**在服務結構資源管理員中管理備份和還原服務**](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-quickstart-azurecluster)的支援。 這使得以下活動可以直接從 SFX 內部進行:發現備份和還原服務、創建備份策略、啟用自動備份、執行臨時備份、觸發還原操作和瀏覽現有備份。

- 宣佈[**可靠集合缺失類型工具**](https://github.com/hiadusum/ReliableCollectionsMissingTypesTool)的可用性:此工具有助於驗證在滾動應用程式升級期間,可靠集合中使用的類型前後相容。 這有助於防止升級失敗或數據丟失以及由於缺少或不相容的類型而導致的數據損壞。

- [**在輔助副本上啟用穩定讀取**](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-configuration#configuration-names-1):穩定讀取將限制輔助副本返回已仲裁已解除的值。

此外,此版本還包含其他新功能、Bug 修復以及可支援性、可靠性和性能改進。 有關變更的完整清單,請參閱[發行說明](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)。

### <a name="service-fabric-70-releases"></a>服務交換矩陣 7.0 版本

| 發行日期 | 版本 | 其他資訊 |
|---|---|---|
| 2019 年 11 月 18 日 | [Azure 服務結構 7.0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [版本資訊](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|
| 2020年1月30日 | [Azure 服務結構 7.0 刷新發佈](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-second-refresh-release/ba-p/1137690)  | [版本資訊](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU2-releasenotes.md)|
| 2020年2月6日 | [Azure 服務結構 7.0 刷新發佈](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-third-refresh-release/ba-p/1156508)  | [版本資訊](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU3-releasenotes.md)|
| 2020年3月2日 | [Azure 服務結構 7.0 刷新發佈](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-fourth-refresh-release/ba-p/1205414)  | [版本資訊](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU4-releasenotes.md)

### <a name="service-fabric-65"></a>服務結構 6.5

此版本包括可支援性、可靠性和性能改進、新功能、錯誤修復以及簡化群集和應用程式生命週期管理的增強功能。

> [!IMPORTANT]
> Service Fabric 6.5 是 Visual Studio 2015 中支援服務交換矩陣工具的最終版本。 建議客戶今後前往[Visual Studio 2019。](https://visualstudio.microsoft.com/vs/)

以下是服務交換矩陣 6.5 中的新增功能:

- 服務結構資源管理員包含用於檢查已上傳到映像儲存的應用程式[的影像儲存檢視器](service-fabric-visualizing-your-cluster.md#image-store-viewer)。

- [修補程式編排應用程式 (POA)](service-fabric-patch-orchestration-application.md)版本[1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0)包含許多自診斷改進。 建議 POA 的客戶遷移到此版本。

- 預設情況下,服務結構6.5群集[啟用事件存儲服務](service-fabric-visualizing-your-cluster.md#event-store),除非您已選擇宣告。

- 為有狀態服務新增了[複本生命週期事件](service-fabric-diagnostics-event-generation-operational.md#replica-events)。

- [種子節點狀態的可見性更好](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status),包括種子節點不正常(*向下*、*刪除*或*未知*)的群集級警告。

- [服務結構應用程式災難復原工具](https://github.com/Microsoft/Service-Fabric-AppDRTool)允許在主群集遇到災難時快速恢復服務交換矩陣狀態服務。 主群集中的數據使用定期備份和還原在輔助備用應用程式中持續同步。

- Visual Studio 支援[將 .NET 核心應用程式發表到基於 Linux 的叢集](service-fabric-how-to-publish-linux-app-vs.md)。

- 升級或創建 Azure 上的新 Linux 群集時,將自動為服務結構 6.5(和更高版本)安裝[Azure 服務結構 CLI (SFCTL)。](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli)

- [默認情況下,SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli)安裝在 MacOS/Linux OneBox 群集上。

有關詳細資訊,請參閱[服務結構 6.5 發行說明](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)。

### <a name="service-fabric-65-releases"></a>服務交換矩陣 6.5 版本

| 發行日期 | 版本 | 其他資訊 |
|---|---|---|
| 2019 年 6 月 11 日 | [Azure 服務結構 6.5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/06/11/azure-service-fabric-6-5-release/)  | [版本資訊](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2019 年 7 月 2 日 | [Azure 服務結構 6.5 刷新發佈](https://blogs.msdn.microsoft.com/azureservicefabric/2019/07/04/azure-service-fabric-6-5-refresh-release/)  | [版本資訊](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 2019 年 7 月 29 日 | [Azure 服務結構 6.5 刷新發佈](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [版本資訊](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| Aug 23, 2019 | [Azure 服務結構 6.5 刷新發佈](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [版本資訊](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| 2019年10月14日 | [Azure 服務結構 6.5 刷新發佈](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | 【 發行標題 】(https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md  |


## <a name="previous-versions"></a>舊版

### <a name="service-fabric-64-releases"></a>服務交換矩陣 6.4 版本

| 發行日期 | 版本 | 其他資訊 |
|---|---|---|
| 2018年11月30日 | [Azure 服務結構 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [版本資訊](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 2018 年 12 月 12 日 | [Azure 服務結構 6.4 Windows 叢集的刷新發佈](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [版本資訊](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 2019年2月4日 | [Azure 服務結構 6.4 刷新發佈](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [版本資訊](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 2019年3月4日 | [Azure 服務結構 6.4 刷新發佈](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [版本資訊](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 2019年4月8日 | [Azure 服務結構 6.4 刷新發佈](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [版本資訊](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2019年5月2日 | [Azure 服務結構 6.4 刷新發佈](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [版本資訊](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 2019年5月28日 | [Azure 服務結構 6.4 刷新發佈](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [發行說明](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)

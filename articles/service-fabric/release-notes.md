---
title: Azure Service Fabric 版本
description: Azure Service Fabric 的版本資訊。 包含 Service Fabric 中最新功能和改進的資訊。
ms.date: 06/10/2019
ms.topic: conceptual
hide_comments: true
hideEdit: true
ms.openlocfilehash: 28870a197af07e964a50a06ffeef08f3b71451f4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82891717"
---
# <a name="service-fabric-releases"></a>Service Fabric 版本

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">疑難排解指南</a>  
| <a href="https://github.com/Azure/service-fabric-issues" target="blank">問題追蹤</a>  
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">支援選項</a>  
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">支援的版本</a>  
| 程式<a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">代碼範例</a>

本文提供 Service Fabric 執行時間和 Sdk 最新版本和更新的詳細資訊。

## <a name="whats-new-in-service-fabric"></a>Service Fabric 的新功能

### <a name="service-fabric-71"></a>Service Fabric 7。1
由於目前的 COVID-19 危機，並考慮到我們的客戶所面臨的挑戰，我們提供7.1，但不會自動升級設定為接收自動升級的叢集。 在進一步通知之前，我們會暫停自動升級，以確保客戶可以套用最適合他們的升級，以避免發生非預期的中斷。

您將能夠透過[Azure 入口網站](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-version-azure#upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal)或[Azure Resource Manager 部署](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-version-azure#set-the-upgrade-mode-using-a-resource-manager-template)來更新至7.1。

啟用自動升級的 Service Fabric 叢集將會在我們繼續標準首度發行程式後，開始自動收到7.1 更新。 我們會在標準首度發行開始于[Service Fabric Tech 社區網站](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)之前提供另一個公告。
我們也已針對從 6.5[到7.1 日](https://docs.microsoft.com/azure/service-fabric/service-fabric-versions#supported-versions)起的主要版本，將更新發佈至終止支援日期。 

## <a name="what-is-new-in-service-fabric-71"></a>Service Fabric 7.1 的新功能為何？
我們很高興宣佈下一版的 Service Fabric。 此版本已載入主要功能和改進。 部分主要功能會反白顯示如下：
## <a name="key-announcements"></a>金鑰公告
- **General Availability** [ **Service Fabric 應用程式的 Service Fabric 受控**識別正式運作](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity)
- [**支援 Ubuntu 18.04**](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-create-vnet-and-linux-cluster)
 - [**預覽：虛擬機器擴展集暫時 os 磁片支援**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-azure-deployment-preparation#use-ephemeral-os-disks-for-virtual-machine-scale-sets)* *：暫時 os 磁片是在本機虛擬機器上建立的儲存體，並不會儲存至遠端 Azure 儲存體。 針對所有 Service Fabric 節點類型（主要和次要），建議使用它們，因為相較于傳統的持續性 OS 磁片，暫時的 OS 磁片：
      -  減少 OS 磁片的讀取/寫入延遲
      -  啟用更快速的重設/重新映射節點管理作業
      -  降低整體成本（磁片是免費的，不會產生額外的儲存體成本）
- 支援[**依主體一般名稱宣告 Service Fabric 應用程式的服務端點憑證**](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-manifest-resources)。
- [**容器化服務的健康情況探查支援**](https://docs.microsoft.com/azure/service-fabric/probes-codepackage)：支援容器化應用程式的活動探查機制。 活動探查有助於宣告容器化應用程式的活動，而當它們無法及時回應時，就會導致重新開機。 
- 支援[容器](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-containers-overview)和[來賓可執行檔](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-guest-executables-introduction)應用程式[**的初始化運算式程式碼套件**](https://docs.microsoft.com/azure/service-fabric/initializer-codepackages)。 這可讓您以指定的循序執行程式碼套件（例如容器），以執行服務封裝初始化。
- **FabricObserver 和 ClusterObserver**是無狀態應用程式，可捕捉與 SF 叢集不同層面相關 Service Fabric 遙測。 這兩個應用程式都已準備好部署至 Windows 生產叢集，以使用 ApplicationInsights、EventSource 和 LogAnalytics 的實作為支援來捕捉豐富的遙測。
    - [**FabricObserver （FO） 2.0**](https://github.com/microsoft/service-fabric-observer)-在所有節點上執行、產生健全狀況事件、在達到使用者設定的資源使用量閾值時發出遙測。 此版本包含跨監視、資料管理、健康情況事件詳細資料、結構化遙測的數個增強功能。
     - [**ClusterObserver （CO） 1.1**](https://github.com/microsoft/service-fabric-observer/tree/master/ClusterObserver) -在一個節點上執行，並捕捉叢集層級的健康情況遙測。 在此版本中，ClusterObserver 也會監視節點狀態，並在節點關閉/停用/停用超過使用者指定的時間週期時發出遙測。

### <a name="improve-application-life-cycle-experience"></a>改善應用程式生命週期經驗

- **[預覽：要求清空](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-advanced#avoid-connection-drops-during-planned-downtime-of-stateless-services)**：在規劃的服務維護期間（例如服務升級或節點停用），您會想要允許服務正常地清空連接。 這項功能會在服務設定中加入實例關閉延遲持續時間。 在規劃的作業期間，SF 會從探索中移除服務的位址，然後在關閉服務之前等待此持續時間。
- **[自動 Subcluster 偵測和平衡](https://docs.microsoft.com/azure/service-fabric/cluster-resource-manager-subclustering )**：當具有不同放置條件約束的服務具有一般[負載](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-metrics)計量時，就會發生 Subclustering。 如果不同組節點上的負載明顯不同，則 Service Fabric 叢集 Resource Manager 認為叢集不平衡，即使因為放置條件約束而有最佳平衡，也是如此。 因此，它會嘗試重新平衡叢集，這可能會造成不必要的服務移動（因為無法大幅改善「不平衡」）。 從這個版本開始，叢集 Resource Manager 現在會嘗試自動偵測這類設定，並瞭解何時可以透過移動來修正不平衡的情況，而改為在不進行大幅改善的情況下，只留下一些問題。  
- [**次要複本的不同移動成本**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-movement-cost)：我們引進了新的移動成本價值 VeryHigh，在某些情況下提供額外的彈性，以定義次要複本是否應該使用個別的移動成本。
- 已啟用容器化應用程式的[**活動探查**](https://docs.microsoft.com/azure/service-fabric/probes-codepackage )機制。 活動探查有助於宣告容器化應用程式的活動，而當它們無法及時回應時，就會導致重新開機。
- [**針對服務執行至完成/一次**](https://docs.microsoft.com/azure/service-fabric/run-to-completion)**

### <a name="image-store-improvements"></a>映射存放區改進
 - Service Fabric 7.1 預設會使用**自訂傳輸來保護節點之間的檔案傳輸**。 SMB 檔案共用上的相依性已從版本7.1 移除。 受保護的 SMB 檔案共用仍存在於包含映射存放區服務複本的節點上，以供客戶退出宣告預設值，並將其升級並降級為舊版本。
       
 ### <a name="reliable-collections-improvements"></a>可靠的集合改進

- [**在記憶體中，使用可靠的集合支援具狀態服務**](https://docs.microsoft.com/azure/service-fabric/service-fabric-work-with-reliable-collections#volatile-reliable-collections)： Volatile 可靠的集合可讓資料保存到磁片中，以因應大規模的中斷，可用於複寫快取之類的工作負載，例如，有時可能會容忍資料遺失。 根據[Volatile 可靠集合的限制和限制](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-reliable-collections-guidelines#volatile-reliable-collections)，針對不需要持續性的工作負載，我們建議您這樣做，這適用于處理極少發生仲裁遺失情況的服務。
- [**預覽： Service Fabric Backup explorer**](https://github.com/microsoft/service-fabric-backup-explorer)：為了簡化 Service Fabric 具狀態應用程式的可靠集合備份管理，Service Fabric Backup explorer 可讓使用者
    - 審核和審核可靠集合的內容，
    - 將目前的狀態更新為一致的視圖
    - 建立可靠集合目前快照集的備份
    - 修正資料損毀
                 
### <a name="service-fabric-71-releases"></a>Service Fabric 7.1 版本
| 發行日期 | 版本 | 其他資訊 |
|---|---|---|
| 2020年4月20日 | [Azure Service Fabric 7。1](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-release/ba-p/1311373)  | [版本資訊](https://github.com/microsoft/service-fabric/tree/master/release_notes/Service-Fabric-71-releasenotes.md)|


### <a name="service-fabric-70"></a>Service Fabric 7。0

Azure Service Fabric 7.0 現已推出！ 您將能夠透過 Azure 入口網站或 Azure Resource Manager 部署來更新至7.0。 由於客戶對於假日期間發行的意見反應，我們將不會開始自動更新設定為接收自動升級的叢集，直到一月為止。
在1月，我們將繼續執行標準的推出程式，而啟用自動升級的叢集將會開始自動接收7.0 更新。 我們會在推出前提供另一個公告。
我們也會更新我們規劃的發行日期，以指出我們會將此原則納入考慮。 在此尋找未來[發行](https://github.com/Microsoft/service-fabric/#service-fabric-release-schedule)排程的更新。
 
這是 Service Fabric 的最新版本，並已載入主要功能和改進。

### <a name="key-announcements"></a>金鑰公告
 - [**KeyVaultReference 應用程式秘密的支援（預覽）**](https://docs.microsoft.com/azure/service-fabric/service-fabric-keyvault-references)：已啟用[受控](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity)識別的 Service Fabric 應用程式現在可以直接參考 Key Vault 秘密 URL，做為環境變數、應用程式參數或容器存放庫認證。 Service Fabric 會自動使用應用程式的受控識別來解析秘密。 
     
- **改善無狀態服務的升級安全性**：為了保證應用程式升級期間的可用性，我們導入了新的設定，以定義可視為可用的[無狀態服務實例數目下限](https://docs.microsoft.com/dotnet/api/system.fabric.description.statelessservicedescription?view=azure-dotnet)。 先前所有服務的這個值都是1，而且無法變更。 透過這項新的每一服務安全檢查，您可以確保您的服務在應用程式升級、叢集升級，以及依賴 Service Fabric 的健康狀態和安全檢查的其他維護期間，保留最少的實例數目。
  
- [**使用者服務的資源限制**](https://docs.microsoft.com/azure/service-fabric/service-fabric-resource-governance#enforcing-the-resource-limits-for-user-services)：使用者可以設定節點上使用者服務的資源限制，以避免 Service Fabric 系統服務的資源耗盡之類的案例。 
  
- 複本類型的[**服務移動成本非常高**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-movement-cost)。 只有當叢集中的條件約束違規無法以任何其他方式修正時，才會移動具有極高移動成本的複本。 如需有關「非常高」移動成本是否合理，以及其他考慮的詳細資訊，請參閱連結的檔。
  
-  **額外的叢集安全性檢查**：在此版本中，我們引進了可設定的種子節點仲裁安全檢查。 這可讓您自訂在叢集生命週期和管理案例中，必須提供多少種子節點。 會封鎖在設定的值下方採用叢集的作業。 今天，預設值一律是種子節點的仲裁，例如，如果您有7種子節點，則預設會封鎖您低於5個種子節點的作業。 有了這項變更，您可以將最小的安全值設為6，一次只允許一個種子節點關閉。
   
- 已新增[**在 Service Fabric Explorer 中管理備份和還原服務**](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-quickstart-azurecluster)的支援。 這可以直接從 SFX 內進行下列活動：探索備份和還原服務、建立備份原則、啟用自動備份、進行臨機操作備份、觸發還原作業，以及流覽現有的備份。

- 宣佈[**ReliableCollectionsMissingTypesTool**](https://github.com/hiadusum/ReliableCollectionsMissingTypesTool)的可用性：此工具可協助驗證在滾動應用程式升級期間，可靠的集合中使用的型別是否向前和向後相容。 這有助於防止因遺失或不相容的類型而導致升級失敗或資料遺失和資料損毀。

- [**在次要複本上啟用穩定讀取**](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-configuration#configuration-names-1)：穩定讀取會限制次要複本傳回已仲裁已確認的值。

此外，此版本還包含其他新功能、bug 修正，以及支援性、可靠性和效能改進。 如需完整的變更清單，請參閱[版本](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)資訊。

### <a name="service-fabric-70-releases"></a>Service Fabric 7.0 版本

| 發行日期 | 版本 | 其他資訊 |
|---|---|---|
| 2019 年 11 月 18 日 | [Azure Service Fabric 7。0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [版本資訊](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|
| 2020年1月30日 | [Azure Service Fabric 7.0 更新版本](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-second-refresh-release/ba-p/1137690)  | [版本資訊](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU2-releasenotes.md)|
| 2020年2月6日 | [Azure Service Fabric 7.0 更新版本](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-third-refresh-release/ba-p/1156508)  | [版本資訊](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU3-releasenotes.md)|
| 2020年3月2日 | [Azure Service Fabric 7.0 更新版本](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-fourth-refresh-release/ba-p/1205414)  | [版本資訊](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU4-releasenotes.md)

### <a name="service-fabric-65"></a>Service Fabric 6。5

此版本包含可支援性、可靠性和效能改良功能、新功能、bug 修正，以及簡化叢集和應用程式生命週期管理的加強功能。

> [!IMPORTANT]
> Service Fabric 6.5 是 Visual Studio 2015 中具有 Service Fabric 工具支援的最終版本。 我們建議客戶繼續[Visual Studio 2019](https://visualstudio.microsoft.com/vs/) 。

以下是 Service Fabric 6.5 的新這是什麼：

- Service Fabric Explorer 包含[映射存放區檢視器](service-fabric-visualizing-your-cluster.md#image-store-viewer)，可檢查您已上傳至映射存放區的應用程式。

- [修補程式協調流程應用程式（POA）](service-fabric-patch-orchestration-application.md)版本[1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0)包含許多自我診斷改良功能。 建議將 POA 的客戶移至此版本。

- 除非您退出宣告，否則 Service Fabric 6.5 叢集的[EventStore 服務預設為啟用](service-fabric-visualizing-your-cluster.md#event-store)。

- 已新增具狀態服務的[複本生命週期事件](service-fabric-diagnostics-event-generation-operational.md#replica-events)。

- [較佳的種子節點狀態可見度](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status)，包括當種子節點狀況不良（*關閉*、*移除*或*不明*）時的叢集層級警告。

- [Service Fabric 應用程式嚴重損壞修復工具](https://github.com/Microsoft/Service-Fabric-AppDRTool)可讓 Service Fabric 具狀態服務在主要叢集發生嚴重損壞時快速復原。 主要叢集的資料會使用定期備份和還原，持續在次要待命應用程式上進行同步處理。

- Visual Studio 支援[將 .Net Core 應用程式發佈到以 Linux 為基礎](service-fabric-how-to-publish-linux-app-vs.md)的叢集。

- 當您在 Azure 上升級或建立新的 Linux 叢集時，將會自動安裝適用于 Service Fabric 6.5 （和更新版本）的[azure SERVICE FABRIC CLI （SFCTL）](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) 。

- [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli)預設會安裝在 MacOS/Linux OneBox 叢集上。

如需進一步的詳細資料，請參閱[Service Fabric 6.5 版本](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)資訊。

### <a name="service-fabric-65-releases"></a>Service Fabric 6.5 版本

| 發行日期 | 版本 | 其他資訊 |
|---|---|---|
| 2019 年 6 月 11 日 | [Azure Service Fabric 6。5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/06/11/azure-service-fabric-6-5-release/)  | [版本資訊](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2019 年 7 月 2 日 | [Azure Service Fabric 6.5 更新版本](https://blogs.msdn.microsoft.com/azureservicefabric/2019/07/04/azure-service-fabric-6-5-refresh-release/)  | [版本資訊](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 2019 年 7 月 29 日 | [Azure Service Fabric 6.5 更新版本](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [版本資訊](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| Aug 23, 2019 | [Azure Service Fabric 6.5 更新版本](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [版本資訊](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| 2019年10月14日 | [Azure Service Fabric 6.5 更新版本](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | [版本資訊](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md  |


## <a name="previous-versions"></a>舊版

### <a name="service-fabric-64-releases"></a>Service Fabric 6.4 版本

| 發行日期 | 版本 | 其他資訊 |
|---|---|---|
| 2018年11月30日 | [Azure Service Fabric 6。4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [版本資訊](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 2018 年 12 月 12 日 | [適用于 Windows 叢集的 Azure Service Fabric 6.4 重新整理版本](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [版本資訊](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 2019年2月4日 | [Azure Service Fabric 6.4 更新版本](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [版本資訊](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 2019年3月4日 | [Azure Service Fabric 6.4 更新版本](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [版本資訊](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 2019年4月8日 | [Azure Service Fabric 6.4 更新版本](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [版本資訊](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2019 5 月2日 | [Azure Service Fabric 6.4 更新版本](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [版本資訊](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 2019 5 月28日 | [Azure Service Fabric 6.4 更新版本](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [版本資訊](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)

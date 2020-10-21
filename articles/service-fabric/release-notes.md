---
title: Azure Service Fabric 版本
description: Azure Service Fabric 的版本資訊。 包含 Service Fabric 的最新功能和增強功能的資訊。
ms.date: 06/10/2019
ms.topic: conceptual
hide_comments: true
hideEdit: true
ms.openlocfilehash: 9bfca7def313fc701798ff96d0ed4b18ca13ef60
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92313881"
---
# <a name="service-fabric-releases"></a>Service Fabric 版本

- <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">疑難排解指南</a> 
- <a href="https://github.com/Azure/service-fabric-issues" target="blank">問題追蹤</a> 
- <a href="/azure/service-fabric/service-fabric-support" target="blank">支援選項</a> 
- <a href="/azure/service-fabric/service-fabric-versions" target="blank">支援的版本</a> 
- <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">程式碼範例</a>

本文提供 Service Fabric 執行時間和 Sdk 的最新版本和更新的詳細資訊。

## <a name="whats-new-in-service-fabric"></a>Service Fabric 的新功能

### <a name="service-fabric-71"></a>Service Fabric 7。1
由於目前的 COVID-19-19 危機，並考慮到我們的客戶面臨的挑戰，我們將7.1 提供，但不會自動升級設定為接收自動升級的叢集。 在進一步通知之前，我們會暫停自動升級，以確保客戶可以在最適合的情況下套用升級，以避免意外中斷。

您將能夠透過 [Azure 入口網站](./service-fabric-cluster-upgrade-version-azure.md#upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal) 或透過 [Azure Resource Manager 部署](./service-fabric-cluster-upgrade-version-azure.md#set-the-upgrade-mode-using-a-resource-manager-template)，更新為7.1。

啟用自動升級的 Service Fabric 叢集將會在我們繼續標準推出程式之後，自動開始收到7.1 更新。 我們將在標準首展開始于 [Service Fabric Tech 社區網站](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)之前，提供另一個公告。
從 6.5 [到7.1 的](./service-fabric-versions.md#supported-versions)主要版本，我們也已將更新發行至終止支援日期。 

## <a name="what-is-new-in-service-fabric-71"></a>什麼是新的 Service Fabric 7.1？
我們很高興宣佈下一版的 Service Fabric。 此版本已載入主要功能和增強功能。 以下反白顯示一些主要功能：
## <a name="key-announcements"></a>重要公告
- **General Availability** [**適用于 Service Fabric 應用程式 Service Fabric 受控**識別的正式推出](./concepts-managed-identity.md)
- [**支援 Ubuntu 18.04**](./service-fabric-tutorial-create-vnet-and-linux-cluster.md)
 - [**預覽：虛擬機器擴展集暫時作業系統磁片支援**](./service-fabric-cluster-azure-deployment-preparation.md#use-ephemeral-os-disks-for-virtual-machine-scale-sets)* *：暫時性 os 磁片是在本機虛擬機器上建立的儲存體，不會儲存到遠端 Azure 儲存體。 建議您在主要和次要)  (的所有 Service Fabric 節點類型，因為相較于傳統的持續性作業系統磁片、暫時作業系統磁片：
      -  減少 OS 磁片的讀取/寫入延遲
      -  更快速地重設/重新開機映射節點管理作業
      -  降低磁片 (的整體成本，而不會產生額外的儲存體成本) 
- 支援以 [**主體一般名稱 Service Fabric 應用程式之服務端點憑證**](./service-fabric-service-manifest-resources.md)的聲明。
- [**容器化服務的健康情況探查支援**](./probes-codepackage.md)：支援容器化應用程式的活動探查機制。 活動探查有助於宣告容器化應用程式的活動，當它們沒有及時回應時，會導致重新開機。 
- 支援[容器](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-containers-overview)和[來賓可執行檔](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-guest-executables-introduction)應用程式的[**初始化運算式程式碼套件**](./initializer-codepackages.md)。 這可讓您執行程式碼封裝 (例如，以指定的順序) 容器，以執行服務封裝初始化。
- **FabricObserver 和 ClusterObserver** 是無狀態的應用程式，可捕捉與 SF 叢集不同層面相關的 Service Fabric 遙測資料。 這兩個應用程式都已準備就緒，可部署到 Windows 生產叢集，藉由實 ApplicationInsights、EventSource 和 LogAnalytics 的支援來捕捉豐富的遙測。
    - [**FabricObserver (FO) 2.0**](https://github.com/microsoft/service-fabric-observer)-在所有節點上執行、產生健康情況事件，並在達到使用者設定的資源使用量閾值時發出遙測。 此版本包含數個跨監視、資料管理、健康情況事件詳細資料、結構化遙測的增強功能。
     - [**ClusterObserver (CO) 1.1**](https://github.com/microsoft/service-fabric-observer/tree/master/ClusterObserver) -在一個節點上執行，可捕獲叢集層級的健全狀況遙測。 在此版本中，ClusterObserver 也會監視節點狀態，並在節點關閉/停用/停用超過使用者指定的時間期間時發出遙測。

### <a name="improve-application-life-cycle-experience"></a>改進應用程式生命週期體驗

- **[預覽：要求清空](./service-fabric-application-upgrade-advanced.md#avoid-connection-drops-during-stateless-service-planned-downtime)**：在規劃的服務維護期間（例如服務升級或節點停用），您想要允許服務正常地清空連接。 這項功能會在服務設定中新增實例關閉延遲持續時間。 在規劃的作業期間，SF 會從探索中移除服務的位址，然後在關閉服務之前等候這段時間。
- **[自動 Subcluster 偵測和平衡](./cluster-resource-manager-subclustering.md)**：當具有不同放置條件約束的服務具有一般 [負載度量](./service-fabric-cluster-resource-manager-metrics.md)時，就會發生子叢集。 如果不同節點集上的負載有很大的差異，則 Service Fabric 叢集資源管理員認為叢集不平衡，即使它因為放置條件約束而有最可能的平衡。 如此一來，它會嘗試重新平衡叢集，可能會造成不必要的服務移動 (因為) 無法大幅改善「不平衡」。 從這個版本開始，叢集資源管理員將會嘗試自動偵測這類設定，並瞭解何時可透過移動來修正不平衡的情況，並在不進行大幅改進的情況下，自行離開。  
- [**次要複本的不同移動成本**](./service-fabric-cluster-resource-manager-movement-cost.md)：我們引進了新的移動成本值 VeryHigh，可在某些情況下提供額外的彈性，以定義次要複本是否應使用個別的移動成本。
- 已啟用容器化應用程式的 [**活動探查**](./probes-codepackage.md) 機制。 活動探查有助於宣告容器化應用程式的活動，當它們沒有及時回應時，會導致重新開機。
- [**針對服務執行到完成/一次**](./run-to-completion.md)**

### <a name="image-store-improvements"></a>映射存放區改進
 - Service Fabric 7.1 預設會使用 **自訂傳輸來保護節點之間的檔案傳輸**。 SMB 檔案共用的相依性會從7.1 版中移除。 受保護的 SMB 檔案共用仍存在於包含映射存放區服務複本的節點上，以供客戶退出宣告預設以及升級和降級為舊版本。
       
 ### <a name="reliable-collections-improvements"></a>可靠的集合改進

- [**在記憶體中，使用可靠的集合來支援具狀態服務的支援**](./service-fabric-work-with-reliable-collections.md#volatile-reliable-collections)：暫時性可靠的集合可讓資料保存到磁片中，以因應大規模中斷、可用於複寫快取等工作負載，例如，可能會偶爾發生資料遺失的情況。 根據 [暫時性可靠集合的限制和限制](./service-fabric-reliable-services-reliable-collections-guidelines.md#volatile-reliable-collections)，針對不需要持續性的工作負載，建議您這樣做，因為這種服務會處理極罕見的仲裁遺失情況。
- [**預覽： Service Fabric Backup explorer**](https://github.com/microsoft/service-fabric-backup-explorer)：為了輕鬆管理 Service Fabric 具狀態應用程式的可靠集合備份，Service Fabric Backup explorer 可讓使用者
    - 審核和審核可靠集合的內容，
    - 將目前狀態更新為一致的視圖
    - 建立可靠集合目前快照集的備份
    - 修正資料損毀
                 
### <a name="service-fabric-71-releases"></a>Service Fabric 7.1 版本
| 發行日期 | 版本 | 其他資訊 |
|---|---|---|
| 2020年4月20日 | [Azure Service Fabric 7。1](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-release/ba-p/1311373)  | [版本資訊](https://github.com/microsoft/service-fabric/tree/master/release_notes/Service-Fabric-71-releasenotes.md)|
| 2020 年 6 月 16 日 | [Microsoft Azure Service Fabric 7.1 第一次重新整理](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-first-refresh-release/ba-p/1466517) | [版本資訊](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU1-releasenotes.md)
| 2020年7月20日 | [Microsoft Azure Service Fabric 7.1 第二次更新](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-second-refresh-release/ba-p/1534246) | [版本資訊](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU2-releasenotes.md)
| 2020 年8 月 12 日 | [Microsoft Azure Service Fabric 7.1 第三次重新整理](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-third-refresh-release/ba-p/1587586) | [版本資訊](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU3-releasenotes.md)
| 2020年9月10日 | [Microsoft Azure Service Fabric 7.1 第四次重新整理](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-fourth-refresh-release/ba-p/1653859)  | [版本資訊](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU5-releasenotes.md)|

### <a name="service-fabric-70"></a>Service Fabric 7。0

Azure Service Fabric 7.0 現已推出！ 您可以透過 Azure 入口網站或透過 Azure Resource Manager 部署更新為7.0。 由於客戶對於假日期間的版本有任何意見反應，我們將不會開始自動更新叢集設定為在1月之前接收自動升級。
在1月，我們將繼續進行標準的推出程式，且已啟用自動升級的叢集將會開始自動收到7.0 更新。 開始推出之前，我們將提供另一次公告。
我們也會更新我們計畫的發行日期，以表示我們會將此原則納入考慮。 請在此尋找未來 [發行](https://github.com/Microsoft/service-fabric/#service-fabric-release-schedule)排程的更新。
 
這是 Service Fabric 的最新版本，並已載入主要功能和增強功能。

### <a name="key-announcements"></a>重要公告
 - [**KeyVaultReference 支援應用程式秘密 (預覽版) **](./service-fabric-keyvault-references.md)：已啟用 [受控](./concepts-managed-identity.md) 識別的 Service Fabric 應用程式現在可以直接將 Key Vault 秘密 URL 參考為環境變數、應用程式參數或容器存放庫認證。 Service Fabric 將會使用應用程式的受控識別自動解析秘密。 
     
- **改進無狀態服務的升級安全性**：為了保證應用程式升級期間的可用性，我們引進了新的設定，以定義 [無狀態服務的最少實例數目](/dotnet/api/system.fabric.description.statelessservicedescription?view=azure-dotnet) 可被視為可用。 之前，所有服務的這個值都是1，且無法變更。 有了這項新的個別服務安全性檢查，您就可以確保您的服務會在應用程式升級、叢集升級，以及依賴 Service Fabric 健康情況和安全性檢查的其他維護期間，保留最少的實例數目。
  
- [**使用者服務的資源限制**](./service-fabric-resource-governance.md#enforcing-the-resource-limits-for-user-services)：使用者可以在節點上設定使用者服務的資源限制，以防止 Service Fabric 系統服務的資源耗盡等案例。 
  
- 複本類型的[**服務移動成本很高**](./service-fabric-cluster-resource-manager-movement-cost.md)。 只有當叢集中有條件約束違規，且無法以任何其他方式修正時，才會移動具有極高移動成本的複本。 請參閱連結的檔，以取得「非常高」移動成本的使用方式是否合理，以及其他考慮的詳細資訊。
  
-  **其他叢集安全性檢查**：在此版本中，我們引進了可設定的種子節點仲裁安全檢查。 這可讓您自訂在叢集生命週期和管理案例中，必須可使用的種子節點數目。 會封鎖在設定的值下方採用叢集的作業。 目前，預設值一律為種子節點的仲裁，例如，如果您有7個種子節點，則預設會封鎖低於5種子節點的作業。 透過這項變更，您可以建立最小的安全值6，一次只允許一個種子節點關閉。
   
- 已新增 [**在 Service Fabric Explorer 中管理備份和還原服務**](./service-fabric-backuprestoreservice-quickstart-azurecluster.md)的支援。 這可讓您直接從 SFX 內取得下列活動：探索備份和還原服務、建立備份原則、啟用自動備份、進行臨機操作備份、觸發還原作業，以及流覽現有的備份。

- 宣佈 [**ReliableCollectionsMissingTypesTool**](https://github.com/hiadusum/ReliableCollectionsMissingTypesTool)的可用性：此工具可協助驗證在輪流應用程式升級期間，可靠集合中使用的類型是向前和向後相容。 這有助於防止升級失敗或資料遺失和資料損毀，因為類型遺失或不相容。

- [**在次要複本上啟用穩定讀取**](./service-fabric-reliable-services-configuration.md#configuration-names-1)：穩定讀取會限制次要複本，以傳回已仲裁已確認的值。

此外，此版本還包含其他新功能、bug 修正，以及支援性、可靠性和效能改進。 如需完整的變更清單，請參閱 [版本](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)資訊。

### <a name="service-fabric-70-releases"></a>Service Fabric 7.0 版本

| 發行日期 | 版本 | 其他資訊 |
|---|---|---|
| 2019 年 11 月 18 日 | [Azure Service Fabric 7。0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [版本資訊](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|
| 2020年1月30日 | [Azure Service Fabric 7.0 更新版本](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-second-refresh-release/ba-p/1137690)  | [版本資訊](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU2-releasenotes.md)|
| 2020年2月6日 | [Azure Service Fabric 7.0 更新版本](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-third-refresh-release/ba-p/1156508)  | [版本資訊](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU3-releasenotes.md)|
| 2020年3月2日 | [Azure Service Fabric 7.0 更新版本](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-fourth-refresh-release/ba-p/1205414)  | [版本資訊](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU4-releasenotes.md)

### <a name="service-fabric-65"></a>Service Fabric 6。5

此版本包含支援性、可靠性和效能改進、新功能、錯誤修正和增強功能，可簡化叢集和應用程式生命週期管理。

> [!IMPORTANT]
> Service Fabric 6.5 是 Visual Studio 2015 中 Service Fabric 工具支援的最終版本。 我們建議客戶移至 [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) 。

以下是 Service Fabric 6.5 的新功能：

- Service Fabric Explorer 包含 [映射存放區檢視器](service-fabric-visualizing-your-cluster.md#image-store-viewer) ，可檢查您已上傳至映射存放區的應用程式。

- [修補程式協調流程應用程式 (POA) ](service-fabric-patch-orchestration-application.md) 版本 [1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) 包含許多自我診斷的增強功能。 建議將 POA 客戶移至此版本。

- 除非您退出宣告，否則預設會針對 Service Fabric 6.5 叢集[啟用 EventStore 服務](service-fabric-visualizing-your-cluster.md#event-store)。

- 已新增具狀態服務的 [複本生命週期事件](service-fabric-diagnostics-event-generation-operational.md#replica-events) 。

- [更好的種子節點狀態可見度](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status)，包括叢集層級警告（如果種子節點狀況不良 (*關閉*、 *移除* 或 *未知* 的) ）。

- [Service Fabric 應用程式嚴重損壞修復工具](https://github.com/Microsoft/Service-Fabric-AppDRTool) 可讓 Service Fabric 具狀態服務在主要叢集遇到嚴重損壞時快速復原。 主要叢集的資料會使用定期備份和還原，持續在次要待命應用程式上進行同步處理。

- Visual Studio 支援 [將 .Net Core 應用程式發佈至以 Linux 為基礎的](service-fabric-how-to-publish-linux-app-vs.md)叢集。

- 當您在 Azure 上升級或建立新的 Linux 叢集時，Service Fabric 6.5 (和更新版本) 會自動安裝[azure SERVICE FABRIC CLI (SFCTL) ](./service-fabric-cli.md) 。

- 預設會在 MacOS/Linux OneBox 叢集上安裝[SFCTL](./service-fabric-cli.md) 。

如需詳細資訊，請參閱 [Service Fabric 6.5 版本](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)資訊。

### <a name="service-fabric-65-releases"></a>Service Fabric 6.5 版本

| 發行日期 | 版本 | 其他資訊 |
|---|---|---|
| 2019 年 6 月 11 日 | [Azure Service Fabric 6。5](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [版本資訊](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2019 年 7 月 2 日 | [Azure Service Fabric 6.5 更新版本](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [版本資訊](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 2019 年 7 月 29 日 | [Azure Service Fabric 6.5 更新版本](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [版本資訊](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| Aug 23, 2019 | [Azure Service Fabric 6.5 更新版本](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [版本資訊](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| 2019年10月14日 | [Azure Service Fabric 6.5 更新版本](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | [版本資訊] (https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md  |


## <a name="previous-versions"></a>舊版

### <a name="service-fabric-64-releases"></a>Service Fabric 6.4 版本

| 發行日期 | 版本 | 其他資訊 |
|---|---|---|
| 2018年11月30日 | [Azure Service Fabric 6。4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [版本資訊](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 2018 年 12 月 12 日 | [適用于 Windows 叢集的 Azure Service Fabric 6.4 更新版本](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [版本資訊](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 2019年2月4日 | [Azure Service Fabric 6.4 更新版本](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [版本資訊](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 2019年3月4日 | [Azure Service Fabric 6.4 更新版本](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [版本資訊](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 2019年4月8日 | [Azure Service Fabric 6.4 更新版本](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [版本資訊](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 5月2日，2019 | [Azure Service Fabric 6.4 更新版本](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [版本資訊](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 2019 5 月28日 | [Azure Service Fabric 6.4 更新版本](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [版本資訊](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
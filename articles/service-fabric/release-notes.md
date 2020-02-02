---
title: Azure Service Fabric 版本
description: Azure Service Fabric 的版本資訊。 包含 Service Fabric 中最新功能和改進的資訊。
ms.date: 06/10/2019
ms.topic: conceptual
hide_comments: true
hideEdit: true
ms.openlocfilehash: 73624df3cb7b04c3c442a6654f1388017482b2ba
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2020
ms.locfileid: "76935448"
---
# <a name="service-fabric-releases"></a>Service Fabric 版本

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">疑難排解指南</a> 
| <a href="https://github.com/Azure/service-fabric-issues" target="blank">問題追蹤</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">支持的選項</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">支持的版本</a> 
| <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">代碼示例</a>

本文提供 Service Fabric 執行時間和 Sdk 最新版本和更新的詳細資訊。

## <a name="whats-new-in-service-fabric"></a>Service Fabric 的新功能

### <a name="service-fabric-70"></a>Service Fabric 7。0

Azure Service Fabric 7.0 現已推出！ 您將能夠透過 Azure 入口網站或 Azure Resource Manager 部署來更新至7.0。 由於客戶對於假日期間發行的意見反應，我們將不會開始自動更新設定為接收自動升級的叢集，直到一月為止。
在1月，我們將繼續執行標準的推出程式，而啟用自動升級的叢集將會開始自動接收7.0 更新。 我們會在推出前提供另一個公告。
我們也會更新我們規劃的發行日期，以指出我們會將此原則納入考慮。 在此尋找未來[發行](https://github.com/Microsoft/service-fabric/#service-fabric-release-schedule)排程的更新。
 
這是 Service Fabric 的最新版本，並已載入主要功能和改進。

### <a name="key-announcements"></a>金鑰公告
 - [**KeyVaultReference 應用程式秘密的支援（預覽）** ](https://docs.microsoft.com/azure/service-fabric/service-fabric-keyvault-references)：已啟用[受控](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity)識別的 Service Fabric 應用程式現在可以直接參考 Key Vault 秘密 URL，做為環境變數、應用程式參數或容器存放庫認證。 Service Fabric 會自動使用應用程式的受控識別來解析秘密。 
     
- **改善無狀態服務的升級安全性**：為了保證應用程式升級期間的可用性，我們導入了新的設定，以定義可視為可用的[無狀態服務實例數目下限](https://docs.microsoft.com/dotnet/api/system.fabric.description.statelessservicedescription?view=azure-dotnet)。 先前所有服務的這個值都是1，而且無法變更。 透過這項新的每一服務安全檢查，您可以確保您的服務在應用程式升級、叢集升級，以及依賴 Service Fabric 的健康狀態和安全檢查的其他維護期間，保留最少的實例數目。
  
- [**使用者服務的資源限制**](https://docs.microsoft.com/azure/service-fabric/service-fabric-resource-governance#enforcing-the-resource-limits-for-user-services)：使用者可以設定節點上使用者服務的資源限制，以避免 Service Fabric 系統服務的資源耗盡之類的案例。 
  
- 複本類型的[**服務移動成本非常高**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-movement-cost)。 只有當叢集中的條件約束違規無法以任何其他方式修正時，才會移動具有極高移動成本的複本。 如需有關何時使用「非常高」的移動成本是合理的，還有其他考慮，請參閱檔以取得詳細資訊。
  
-  **其他叢集安全性檢查**：在此版本中，我們引進了可設定的種子節點仲裁安全檢查。 這可讓您自訂在叢集生命週期和管理案例中，必須提供多少種子節點。 會封鎖在設定的值下方採用叢集的作業。 今天，預設值一律是種子節點的仲裁，例如，如果您有7種子節點，則預設會封鎖您低於5個種子節點的作業。 有了這項變更，您可以將最小的安全值設為6，一次只允許一個種子節點關閉。
   
- 已新增[**在 Service Fabric Explorer 中管理備份和還原服務**](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-quickstart-azurecluster)的支援。 這可以直接從 SFX 內進行下列活動：探索備份和還原服務、建立備份原則、啟用自動備份、進行臨機操作備份、觸發還原作業，以及流覽現有的備份。

- 宣佈[**ReliableCollectionsMissingTypesTool**](https://github.com/hiadusum/ReliableCollectionsMissingTypesTool)的可用性：此工具可協助驗證在滾動應用程式升級期間，可靠的集合中使用的型別是否向前和向後相容。 這有助於防止因遺失或不相容的類型而導致升級失敗或資料遺失和資料損毀。

- [**在次要複本上啟用穩定讀取**](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-configuration#configuration-names-1)：穩定讀取會限制次要複本傳回已仲裁已確認的值。

此外，此版本還包含其他新功能、bug 修正，以及支援性、可靠性和效能改進。 如需完整的變更清單，請參閱[版本](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)資訊。

### <a name="service-fabric-70-releases"></a>Service Fabric 7.0 版本

| 發行日期 | 發行 | 詳細資訊 |
|---|---|---|
| 2019年11月18日 | [Azure Service Fabric 7。0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [版本資訊](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|
| 2020年1月30日 | [Azure Service Fabric 7.0 更新版本](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-second-refresh-release/ba-p/1137690)  | [版本資訊](https://github.com/Azure/service-fabric/blob/master/release_notes/Service-Fabric-70CU2-releasenotes.md)|


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

| 發行日期 | 發行 | 詳細資訊 |
|---|---|---|
| 2019 年 6 月 11 日 | [Azure Service Fabric 6。5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/06/11/azure-service-fabric-6-5-release/)  | [版本資訊](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2019 年 7 月 2 日 | [Azure Service Fabric 6.5 更新版本](https://blogs.msdn.microsoft.com/azureservicefabric/2019/07/04/azure-service-fabric-6-5-refresh-release/)  | [版本資訊](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 2019年7月29日 | [Azure Service Fabric 6.5 更新版本](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [版本資訊](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| Aug 23, 2019 | [Azure Service Fabric 6.5 更新版本](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [版本資訊](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| 2019年10月14日 | [Azure Service Fabric 6.5 更新版本](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | [版本資訊](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md)  |


## <a name="previous-versions"></a>舊版

### <a name="service-fabric-64-releases"></a>Service Fabric 6.4 版本

| 發行日期 | 發行 | 詳細資訊 |
|---|---|---|
| 2018年11月30日 | [Azure Service Fabric 6。4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [版本資訊](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 2018 年 12 月 12 日 | [適用于 Windows 叢集的 Azure Service Fabric 6.4 重新整理版本](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [版本資訊](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 2019年2月4日 | [Azure Service Fabric 6.4 更新版本](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [版本資訊](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 2019年3月4日 | [Azure Service Fabric 6.4 更新版本](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [版本資訊](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 2019年4月8日 | [Azure Service Fabric 6.4 更新版本](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [版本資訊](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2019 5 月2日 | [Azure Service Fabric 6.4 更新版本](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [版本資訊](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 2019 年 5 月 28 日 | [Azure Service Fabric 6.4 更新版本](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [版本資訊](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)

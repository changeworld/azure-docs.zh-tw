---
title: Azure 服務結構發佈
description: Azure 服務結構的版本資訊。 包括有關服務結構的最新功能和改進的資訊。
ms.date: 06/10/2019
ms.topic: conceptual
hide_comments: true
hideEdit: true
ms.openlocfilehash: cb4fdd56e9cf67c71ac690d423499929167f8977
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064236"
---
# <a name="service-fabric-releases"></a>服務交換矩陣版本

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">故障排除指南</a> 
| <a href="https://github.com/Azure/service-fabric-issues" target="blank">問題 跟蹤</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">支援選項</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">支援版本</a> 
| <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">代碼示例</a>

本文提供有關服務結構運行時和 SDK 的最新版本和更新的詳細資訊。

## <a name="whats-new-in-service-fabric"></a>服務交換矩陣中的新增功能

### <a name="service-fabric-70"></a>服務結構 7.0

Azure 服務結構 7.0 現已可用！ 您將能夠通過 Azure 門戶或通過 Azure 資源管理器部署更新到 7.0。 由於客戶對假期期間發佈版本的回饋，我們不會開始自動更新群集，這些群集設置為接收自動升級，直到 1 月。
在一月份，我們將恢復標準推出過程，啟用自動升級的群集將自動開始接收 7.0 更新。 我們將在推出開始前提供另一個公告。
我們還將更新計畫發佈日期，以表明我們考慮到了此政策。 在此處查看我們未來[發佈時程表](https://github.com/Microsoft/service-fabric/#service-fabric-release-schedule)的更新。
 
這是服務結構的最新版本，並載入了關鍵功能和改進。

### <a name="key-announcements"></a>主要公告
 - [**KeyVault 對應用程式機密的支援（預覽）：**](https://docs.microsoft.com/azure/service-fabric/service-fabric-keyvault-references)已啟用[託管標識](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity)的服務結構應用程式現在可以直接引用金鑰保存庫金鑰 URL 作為環境變數、應用程式參數或容器存儲庫憑據。 Service Fabric 將使用應用程式的託管標識自動解析機密。 
     
- **改進了無狀態服務的升級安全性**：為了保證應用程式升級期間的可用性，我們引入了新的配置，[以定義認為可用的無狀態服務的最小實例數](https://docs.microsoft.com/dotnet/api/system.fabric.description.statelessservicedescription?view=azure-dotnet)。 以前，此值對於所有服務都是 1，並且不可更改。 通過這種新的每服務安全檢查，您可以確保服務在應用程式升級、群集升級和其他依賴于 Service Fabric 的健康和安全檢查的維護期間保留最少的向上實例數。
  
- [**使用者服務的資源限制**](https://docs.microsoft.com/azure/service-fabric/service-fabric-resource-governance#enforcing-the-resource-limits-for-user-services)：使用者可以為節點上的使用者服務設置資源限制，以防止出現資源耗盡服務結構系統服務等情況。 
  
- [**副本類型的服務移動成本非常高**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-movement-cost)。 僅當群集中存在無法以任何其他方式修復的約束衝突時，才會移動具有極高移動成本的副本。 有關"非常高"移動成本的使用是否合理以及其他注意事項，請參閱文檔以獲取更多資訊。
  
-  **其他群集安全檢查**：在此版本中，我們引入了可配置的種子節點仲裁安全檢查。 這允許您自訂在群集生命週期和管理方案中必須可用的種子節點數。 將群集帶到配置的值以下的操作將被阻止。 今天，預設值始終是種子節點的仲裁，例如，如果您有 7 個種子節點，預設情況下將低於 5 個種子節點的操作被阻止。 通過此更改，您可以創建最小安全值 6，這樣一次只能關閉一個種子節點。
   
- 添加了對[**在服務結構資源管理器中管理備份和還原服務**](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-quickstart-azurecluster)的支援。 這使得以下活動可以直接從 SFX 內部進行：發現備份和還原服務、創建備份策略、啟用自動備份、執行臨時備份、觸發還原操作和流覽現有備份。

- 宣佈[**可靠集合缺失類型工具**](https://github.com/hiadusum/ReliableCollectionsMissingTypesTool)的可用性：此工具有助於驗證在滾動應用程式升級期間，可靠集合中使用的類型前後相容。 這有助於防止升級失敗或資料丟失以及由於缺少或不相容的類型而導致的資料損壞。

- [**在輔助副本上啟用穩定讀取**](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-configuration#configuration-names-1)：穩定讀取將限制輔助副本返回已仲裁已解除的值。

此外，此版本還包含其他新功能、Bug 修復以及可支援性、可靠性和性能改進。 有關更改的完整清單，請參閱[版本資訊](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)。

### <a name="service-fabric-70-releases"></a>服務交換矩陣 7.0 版本

| 發行日期 | 版本 | 其他資訊 |
|---|---|---|
| 2019 年 11 月 18 日 | [Azure 服務結構 7.0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [版本資訊](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|
| 2020年1月30日 | [Azure 服務結構 7.0 刷新發佈](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-second-refresh-release/ba-p/1137690)  | [版本資訊](https://github.com/Azure/service-fabric/blob/master/release_notes/Service-Fabric-70CU2-releasenotes.md)|
| 2020年2月6日 | [Azure 服務結構 7.0 刷新發佈](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-third-refresh-release/ba-p/1156508)  | [版本資訊](https://github.com/Azure/service-fabric/blob/master/release_notes/Service-Fabric-70CU3-releasenotes.md)|

### <a name="service-fabric-65"></a>服務結構 6.5

此版本包括可支援性、可靠性和性能改進、新功能、錯誤修復以及簡化群集和應用程式生命週期管理的增強功能。

> [!IMPORTANT]
> Service Fabric 6.5 是 Visual Studio 2015 中支援服務交換矩陣工具的最終版本。 建議客戶今後前往[Visual Studio 2019。](https://visualstudio.microsoft.com/vs/)

以下是服務交換矩陣 6.5 中的新增功能：

- 服務結構資源管理器包括用於檢查已上載到映射存儲的應用程式[的圖像存儲檢視器](service-fabric-visualizing-your-cluster.md#image-store-viewer)。

- [修補程式編排應用程式 （POA）](service-fabric-patch-orchestration-application.md)版本[1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0)包含許多自診斷改進。 建議 POA 的客戶遷移到此版本。

- 預設情況下，服務結構 6.5 群集[啟用事件存儲服務](service-fabric-visualizing-your-cluster.md#event-store)，除非您已退出宣告。

- 為有狀態服務添加了[副本生命週期事件](service-fabric-diagnostics-event-generation-operational.md#replica-events)。

- [種子節點狀態的可見度更好](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status)，包括種子節點不正常（*向下*、*刪除*或*未知*）的群集級警告。

- [服務結構應用程式災害復原工具](https://github.com/Microsoft/Service-Fabric-AppDRTool)允許在主群集遇到災難時快速恢復服務交換矩陣狀態服務。 主群集中的資料使用定期備份和還原在輔助備用應用程式中持續同步。

- Visual Studio 支援[將 .NET 核心應用程式發佈到基於 Linux 的群集](service-fabric-how-to-publish-linux-app-vs.md)。

- 升級或創建 Azure 上的新 Linux 群集時，將自動為服務結構 6.5（和更高版本）安裝[Azure 服務結構 CLI （SFCTL）。](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli)

- [預設情況下，SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli)安裝在 MacOS/Linux OneBox 群集上。

有關詳細資訊，請參閱[服務結構 6.5 版本資訊](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)。

### <a name="service-fabric-65-releases"></a>服務交換矩陣 6.5 版本

| 發行日期 | 版本 | 其他資訊 |
|---|---|---|
| 2019 年 6 月 11 日 | [Azure 服務結構 6.5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/06/11/azure-service-fabric-6-5-release/)  | [版本資訊](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2019 年 7 月 2 日 | [Azure 服務結構 6.5 刷新發佈](https://blogs.msdn.microsoft.com/azureservicefabric/2019/07/04/azure-service-fabric-6-5-refresh-release/)  | [版本資訊](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 2019 年 7 月 29 日 | [Azure 服務結構 6.5 刷新發佈](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [版本資訊](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| Aug 23, 2019 | [Azure 服務結構 6.5 刷新發佈](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [版本資訊](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| 2019年10月14日 | [Azure 服務結構 6.5 刷新發佈](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | [版本資訊](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md)  |


## <a name="previous-versions"></a>舊版

### <a name="service-fabric-64-releases"></a>服務交換矩陣 6.4 版本

| 發行日期 | 版本 | 其他資訊 |
|---|---|---|
| 2018年11月30日 | [Azure 服務結構 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [版本資訊](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 2018 年 12 月 12 日 | [Azure 服務結構 6.4 Windows 群集的刷新發佈](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [版本資訊](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 2019年2月4日 | [Azure 服務結構 6.4 刷新發佈](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [版本資訊](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 2019年3月4日 | [Azure 服務結構 6.4 刷新發佈](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [版本資訊](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 2019年4月8日 | [Azure 服務結構 6.4 刷新發佈](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [版本資訊](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2019年5月2日 | [Azure 服務結構 6.4 刷新發佈](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [版本資訊](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 2019年5月28日 | [Azure 服務結構 6.4 刷新發佈](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [版本資訊](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)

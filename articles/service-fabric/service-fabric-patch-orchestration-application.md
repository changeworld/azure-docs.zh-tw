---
title: 修補 Service Fabric 叢集中的 Windows 作業系統
description: 本文討論如何通過使用修補程式編排應用程式在 Service Fabric 群集上自動進行作業系統修補。
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/01/2019
ms.author: atsenthi
ms.openlocfilehash: 857a4da0b24d600ecc572933af578e2e8faf501a
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366324"
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>修補 Service Fabric 叢集中的 Windows 作業系統

> 
> [!IMPORTANT]
> 自 2019 年 4 月 30 日起，修補程式業務流程應用程式版本 1.2.* 不再受支援。 請務必升級到最新版本。

> [!NOTE]
> [在虛擬機器規模集中獲取自動作業系統映射升級](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade)是使作業系統在 Azure 中進行修補的最佳做法。 基於虛擬機器縮放集的自動作業系統映射升級將需要銀或更大的耐用性。
>

 修補程式編排應用程式 （POA） 是圍繞 Azure 服務結構修復管理器服務的包裝器，該服務為非 Azure 託管群集啟用基於配置的 OS 修補程式計畫。 非 Azure 託管群集不需要 POA，但需要按更新域安排修補程式安裝來修補 Service Fabric 群集主機，而不會造成停機。

POA 是一個服務交換矩陣應用程式，可在 Service Fabric 群集上自動修補作業系統，而不會造成停機。

POA 提供以下功能：

- **自動化的作業系統更新安裝**。 會自動下載並安裝作業系統更新。 叢集節點根據需要重新開機，而不會造成群集停機。

- **叢集感知的修補和健康情況整合**。 當 POA 應用更新時，它會監視叢集節點的運行狀況。 叢集節點一次更新一個節點，或一次更新一個更新域。 如果群集的運行狀況由於修補過程而下降，將停止修補以防止使問題惡化。

## <a name="internal-details-of-poa"></a>POA 的內部詳細資訊

POA 由以下子元件組成：

- **協調器服務**︰是具狀態服務，負責：
    - 協調整個叢集上的 Windows Update 作業。
    - 儲存已完成之 Windows Update 作業的結果。

- **節點代理程式服務**︰是無狀態服務，在所有 Service Fabric 叢集節點上執行。 此服務負責：
    - 啟動節點代理程式 NTService。
    - 監視節點代理程式 NTService。

- **節點代理程式 NTService**：在較高層級權限 (系統) 執行的 Windows NT 服務。 相比之下，節點代理程式服務和協調器服務則是在較低層級權限 (網路服務) 執行。 此服務會負責執行下列所有叢集節點上的 Windows Update 作業：
    - 禁用節點上的自動 Windows 更新。
    - 根據使用者提供的策略下載和安裝 Windows 更新。
    - 重新開機電腦後 Windows 更新安裝。
    - 將 Windows Update 的結果上傳至協調器服務。
    - 如果操作耗盡所有重試後出現故障，則報告運行狀況報告。

> [!NOTE]
> POA 使用服務交換矩陣修復管理器服務來禁用或啟用節點並執行運行狀況檢查。 POA 創建的修復任務跟蹤每個節點的 Windows 更新進度。

## <a name="prerequisites"></a>Prerequisites

> [!NOTE]
> 所需的最小 .NET 框架版本為 4.6。

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>啟用維修管理器服務（如果服務尚未運行）

POA 要求在群集上啟用修復管理器服務。

#### <a name="azure-clusters"></a>Azure 叢集

預設情況下，銀色持久性層中的 Azure 群集啟用了修復管理器服務。 黃金持久性層中的 Azure 群集可能啟用，也可能未啟用修復管理器服務，具體取決於創建這些群集的時數。 預設情況下，青銅持久性層中的 Azure 群集未啟用修復管理器服務。 如果服務已啟用，則可以在服務結構資源管理器中的系統服務部分中看到它運行。

##### <a name="the-azure-portal"></a>Azure 入口網站
設置群集時，可以從 Azure 門戶啟用修復管理器。 配置群集時，請在 **"附加功能**"下選擇 **"包括修復管理器"** 選項。

![從 Azure 門戶啟用修復管理器的圖像](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="the-azure-resource-manager-deployment-model"></a>Azure 資源管理器部署模型
或者，可以使用 Azure[資源管理器部署模型](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)在新和現有服務結構群集上啟用修復管理器服務。 取得您想要部署之叢集的範本。 您可以使用範例範本，或建立自訂的 Azure Resource Manager 部署模型範本。 

要使用[Azure 資源管理器部署模型範本](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)啟用修復管理器服務，請使用以下操作：

1. 檢查以確保`apiVersion`設置為*2017-07-01 預覽*的*Microsoft.ServiceFabric/群集*資源。 如果情況不同，您需要更新`apiVersion`至*2017-07-01 預覽*版或更高版本：

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

1. 通過在`addonFeatures``fabricSettings`本節後添加以下部分來啟用維修管理器服務：

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. 使用這些更改更新群集範本後，應用這些更改，讓更新完成。 現在，您可以看到群集中正在運行的修復管理器服務。 在服務結構資源管理器中的系統服務部分中稱為*結構：/系統/修復管理器服務*。 

### <a name="standalone-on-premises-clusters"></a>獨立的內部部署叢集

要在新或現有服務交換矩陣群集上啟用修復管理器服務，可以使用獨立 Windows[群集的配置設置](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest)。

要啟用維修管理器服務，

1. 檢查以確保`apiVersion`[在常規群集配置](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest#general-cluster-configurations)中設置為*04-2017*或更高版本，如下所示：

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

1. 通過在`addonFeatures``fabricSettings`節後添加以下部分來啟用維修管理器服務，如下所示：

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

1. 使用更新的群集清單[創建新群集](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-for-windows-server)或[升級群集配置](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-windows-server)，使用這些更改更新群集清單。 

   使用更新的群集清單運行群集後，可以看到群集中正在運行的修復管理器服務。 它被稱為*結構：/系統/修復管理器服務*，它位於服務結構資源管理器中的系統服務部分。

### <a name="configure-windows-updates-for-all-nodes"></a>為所有節點配置 Windows 更新

自動 Windows 更新可能會導致可用性損失，因為多個叢集節點可以同時重新開機。 預設情況下，POA 會嘗試禁用每個叢集節點上的自動 Windows 更新。 但是，如果設置由管理員或群組原則管理，我們建議將 Windows 更新策略設置為"下載前通知"。

## <a name="download-the-application-package"></a>下載應用程式包

要下載應用程式包，請轉到 GitHub 上的[修補程式業務流程應用程式發佈頁面](https://github.com/microsoft/Service-Fabric-POA/releases/latest/)。

## <a name="configure-poa-behavior"></a>配置 POA 行為

您可以配置 POA 行為以滿足您的需要。 在創建或更新應用程式時傳入應用程式參數，覆蓋預設值。 可以通過指定`ApplicationParameter``Start-ServiceFabricApplicationUpgrade`或`New-ServiceFabricApplication`Cmdlet 來提供應用程式參數。

| 參數        | 類型                          | 詳細資料 |
|:-|-|-|
|MaxResultsToCache    |long                              | 應緩存的最大 Windows 更新結果數。 <br><br>預設值為 3000，假設： <br> &nbsp;&nbsp;- 節點數為 20。 <br> &nbsp;&nbsp;- 每月對節點的更新數為 5。 <br> &nbsp;&nbsp;- 每個操作的結果數可以是 10。 <br> &nbsp;&nbsp;- 應存儲過去三個月的結果。 |
|TaskApprovalPolicy   |例舉 <br> { NodeWise, UpgradeDomainWise }                          |TaskApprovalPolicy 會指出協調器服務在 Service Fabric 叢集節點中用來安裝 Windows 更新的原則。<br><br>允許的值包括： <br>*NodeWise*： 一次安裝一個節點的 Windows 更新。 <br> *升級域：* 一次安裝一個更新域的 Windows 更新。 （最多時，屬於更新域的所有節點都可以進行 Windows 更新。<br><br> 為了説明確定最適合群集的策略，請參閱[常見問題解答](#frequently-asked-questions)部分。
|LogsDiskQuotaInMB   |long  <br> （預設值： *1024*）               | 修補程式業務流程應用日誌的最大大小（MB），可在本地節點上保留。
| WUQuery               | 字串<br>（預設值：*已安裝=0*）                | 用以取得 Windows 更新的查詢。 如需詳細資訊，請參閱 [WuQuery](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)。
| InstallWindowsOSOnlyUpdates | *布林* <br> (預設值：False)                 | 使用此旗標可控制所應下載並安裝的更新。 允許下列值 <br>true - 只安裝 Windows 作業系統的更新。<br>false - 在電腦上安裝所有可用的更新。          |
| WUOperationTimeOutInMinutes | Int <br>（預設值： *90*）                   | 指定任何 Windows Update 作業的逾時 (搜尋或下載或安裝)。 如果作業未在指定的逾時內完成，它就會中止。       |
| WURescheduleCount     | Int <br> （預設值： *5*）                  | 如果操作持續失敗，服務重新安排 Windows 更新的最大次數。          |
| WURescheduleTimeInMinutes | Int <br>（預設值： *30*） | 如果失敗仍然存在，服務重新安排 Windows 更新的時間間隔。 |
| WUFrequency           | 逗號分隔字串（預設：*每週，星期三，7：00：00）*     | 安裝 Windows 更新的頻率。 格式與可能的值如下： <br>&nbsp;&nbsp;- 每月：DD，HH：MM：SS（例如，*每月，5，12：22：32）*<br>欄位 DD（天）的允許值是 1 到 28 和"最後"的數位。 <br> &nbsp;&nbsp;- 每週，天，HH：MM：SS（例如，*每週，星期二，12：22：32）*  <br> &nbsp;&nbsp;- 每日，HH：MM：SS（例如，*日報，12：22：32*）  <br> &nbsp;&nbsp;-  *無*表示不應執行 Windows 更新。  <br><br> 時間以 UTC 表示。|
| AcceptWindowsUpdateEula | Boolean <br>（預設值 *：true）* | 藉由設定這個旗標，應用程式會代表電腦的擁有者接受 Windows Update 的使用者授權合約 (EULA)。              |

> [!TIP]
> 如果希望立即進行 Windows 更新，請`WUFrequency`根據應用程式部署時間進行設置。 例如，假設您的測試叢集有五個節點，計劃於大約下午 5:00 UTC 部署應用程式。 如果假定應用程式升級或部署最多需要 30 分鐘，請將 WU頻率設置為 *"每日"，17：30：00*。

## <a name="deploy-poa"></a>部署 POA

1. 完成準備叢集的所有必要條件步驟。
1. 像任何其他服務結構應用一樣部署 POA。 要使用 PowerShell 部署它，請參閱[使用 PowerShell 部署和刪除應用程式](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications)。
1. 若要在部署時設定應用程式，可將 `ApplicationParameter` 傳遞給 `New-ServiceFabricApplication` Cmdlet。 為了您的方便，我們提供了 Deploy.ps1 指令碼以及我們的應用程式。 若要使用指令碼：

    - 使用 `Connect-ServiceFabricCluster` 連線至 Service Fabric 叢集。
    - 利用適當的 `ApplicationParameter` 值執行 Powershell 指令碼 Deploy.ps1。

> [!NOTE]
> 將腳本和應用程式資料夾 *"修補程式應用程式"* 保留在同一目錄中。

## <a name="upgrade-poa"></a>升級 POA

要使用 PowerShell 升級您的 POA 版本，請使用[PowerShell 按照服務結構應用程式升級](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell)中的說明進行操作。

## <a name="remove-poa"></a>刪除 POA

要刪除應用程式，請按照[使用 PowerShell 部署和刪除應用程式](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications)的說明進行操作。

為了您的方便，我們提供了 Undeploy.ps1 腳本以及應用程式。 若要使用指令碼：

  - 使用 ```Connect-ServiceFabricCluster``` 連線至 Service Fabric 叢集。
  - 執行 Powershell 指令碼 Undeploy.ps1。

> [!NOTE]
> 將腳本和應用程式資料夾 *"修補程式應用程式"* 保留在同一目錄中。

## <a name="view-the-windows-update-results"></a>檢視 Windows Update 結果

POA 公開 REST API 以向使用者顯示歷史結果。 下面是結果 JSON 的示例：

```json
[
  {
    "NodeName": "_stg1vm_1",
    "WindowsUpdateOperationResults": [
      {
        "OperationResult": 0,
        "NodeName": "_stg1vm_1",
        "OperationTime": "2019-05-13T08:44:56.4836889Z",
        "OperationStartTime": "2019-05-13T08:44:33.5285601Z",
        "UpdateDetails": [
          {
            "UpdateId": "7392acaf-6a85-427c-8a8d-058c25beb0d6",
            "Title": "Cumulative Security Update for Internet Explorer 11 for Windows Server 2012 R2 (KB3185319)",
            "Description": "A security issue has been identified in a Microsoft software product that could affect your system. You can help protect your system by installing this update from Microsoft. For a complete listing of the issues that are included in this update, see the associated Microsoft Knowledge Base article. After you install this update, you may have to restart your system.",
            "ResultCode": 0,
            "HResult": 0
          }
        ],
        "OperationType": 1,
        "WindowsUpdateQuery": "IsInstalled=0",
        "WindowsUpdateFrequency": "Daily,10:00:00",
        "RebootRequired": false
      }
    ]
  },
  ...
]
```

下表描述了 JSON 欄位：

欄位 | 值 | 詳細資料
-- | -- | --
OperationResult | 0 - 成功<br> 1 - 成功但發生錯誤<br> 2 - 失敗<br> 3 - 已中止<br> 4 - 中止但逾時 | 指示整個操作的結果，通常涉及安裝一個或多個更新。
ResultCode | 與 OperationResult 相同 | 此欄位指示單個更新的安裝操作的結果。
OperationType | 1 - 安裝<br> 0 - 搜索和下載| 預設情況下，安裝是結果中顯示的唯一的操作類型。
WindowsUpdateQuery | 預設值為 "IsInstalled=0" | 用於搜索更新的 Windows 更新查詢。 有關詳細資訊，請參閱[WuQuery](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)。
RebootRequired | true - 需要重新開機<br> false - 不需要重新開機 | 指示是否需要重新開機才能完成更新的安裝。
操作開始時間 | Datetime | 指示操作（下載/安裝）開始的時間。
操作時間 | Datetime | 指示操作（下載/安裝）完成的時間。
HResult | 0 - 成功<br> 其他 - 失敗| 使用 updateID"7392acaf-6a85-427c-8a8d-058c25beb0d6"指示 Windows 更新失敗的原因。

如果尚未排程更新，JSON 結果會是空的。

登錄到群集以查詢 Windows 更新結果。 找出協調器服務主位址的副本 IP 位址，並從瀏覽器打開&lt;以下 URL：HTTP:// REPLICA-IP&gt;：&lt;應用程式埠&gt;/修補應用程式/v1/GetWindows更新結果。

協調器服務的 REST 端點具有動態連接埠。 要檢查確切的 URL，請參閱服務結構資源管理器。 例如，結果在 上*http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults*可用。

![REST 終結點的圖像](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)

如果在群集上啟用了反向代理，也可以從群集外部訪問 URL。

您需要命中的終結點是*HTTP://&lt;SERVERURL：&gt;&lt;反向代理&gt;/修補應用程式/協調服務/v1/獲取Windows更新結果*。

要在群集上啟用反向代理，請按照[Azure 服務結構 中的反向代理中的](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy)說明進行操作。 

> 
> [!WARNING]
> 配置反向代理後，群集中公開 HTTP 終結點的所有微服務都可以從群集外部定址。

## <a name="diagnostics-and-health-events"></a>診斷和運行狀況事件

本節討論如何通過 Service Fabric 群集上的 POA 調試或診斷修補程式更新的問題。

> [!NOTE]
> 要獲得以下許多調用的自診斷改進，應安裝 POA 版本 1.4.0 或更高版本。

節點代理 NT 服務創建用於在節點上安裝更新的[修復任務](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtask?view=azure-dotnet)。 然後，協調員服務根據任務審批策略準備每個任務。 最後，準備好的任務由修復管理器批准，如果群集處於不正常狀態，修復管理器不會批准任何任務。 

為了説明您瞭解節點上的更新是如何進行的，讓我們一步一步地操作：

1. NodeAgentNT服務在每個節點上運行，在計畫的時間查找可用的 Windows 更新。 如果更新可用，它將在節點上下載它們。

1. 下載更新後，節點代理 NTService 會為節點創建相應的修復任務，該節點的名稱*POS___unique_id>。\< * 您可以使用["獲取服務結構修復任務](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps)Cmdlet"或在節點詳細資訊部分中使用 SFX 來查看這些修復任務。 創建修復任務後，它會快速移動到[*"已聲明"* 狀態](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtaskstate?view=azure-dotnet)。

1. 協調員服務定期查找*處於"已聲明"* 狀態的修復任務，然後根據任務審批策略將其更新為 *"準備*"狀態。 如果任務審批策略配置為 NodeWise，則僅當當前沒有其他修復任務處於 *"準備*、*已批准*、*執行*"或 *"還原*"狀態時，才準備與節點對應的修復任務。 

   同樣，在升級 Wise 任務審批策略中，只有屬於同一更新域的節點存在上述狀態中的任務。 將修復任務移動到 *"準備"* 狀態後，將[禁用](https://docs.microsoft.com/powershell/module/servicefabric/disable-servicefabricnode?view=azureservicefabricps)相應的服務結構節點，將意圖設置為 *"重新開機*"。

   POA 版本 1.4.0 和更高版本在"協調服務"上使用群集修補狀態屬性發佈事件，以顯示正在修補的節點。 更新安裝在_poanode_0上，如下圖所示：

    [![群集修補狀態的圖像](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png)](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png#lightbox)

1. 禁用節點後，修復任務將移動到*執行*狀態。 
   
   > [!NOTE]
   > 處於 *"已禁用"* 狀態的節點可能會阻止新的修復任務，從而停止群集上的修補操作。

1. 當修復任務處於執行狀態*時*，該節點上的修補程式安裝將開始。 安裝修補程式後，節點可能會重新開機，也可能不重新開機，具體取決於修補程式。 接下來，修復任務將移動到 *"還原*"狀態，從而重新啟用節點。 然後，修復任務被標記為已完成。

   在 POA 版本 1.4.0 及更高版本中，您可以通過使用 WU 操作狀態-\<節點名稱>屬性查看 NodeAgent 服務上的運行狀況事件來查找更新的狀態。 以下圖像中的突出顯示部分顯示節點上的 Windows 更新狀態*poanode_0，* 並*poanode_2*：

   [![Windows 更新操作狀態的圖像](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png#lightbox)

   [![Windows 更新操作狀態的圖像](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png#lightbox)

   您還可以使用 PowerShell 獲取詳細資訊。 為此，可以使用[Get-ServiceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps)連接到群集並獲取修復任務的狀態。 
   
   在下面的示例中，"POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15"任務處於*下載完成*狀態。 這意味著已在*poanode_2*節點上下載更新，並且當任務移動到*執行*狀態時，將嘗試安裝。

   ``` powershell
    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k = Get-ServiceFabricRepairTask -TaskId "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15"

    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k.ExecutorData
    {"ExecutorSubState":2,"ExecutorTimeoutInMinutes":90,"RestartRequestedTime":"0001-01-01T00:00:00"}
    ```

   如果仍發現更多問題，請登錄到虛擬機器 （VM） 或 VM，並使用 Windows 事件日誌瞭解這些問題。 前面提到的修復任務只能存在於以下執行器子狀態中：

      執行子國家 | 描述
    -- | -- 
      無 =1 |  表示節點上沒有正在進行的操作。 狀態可能處於過渡狀態。
      下載完成*2 | 表示下載操作已完成，但成功、部分失敗或失敗。
      安裝批准=3 | 表示下載操作已提前完成，並且維修管理器已批准安裝。
      安裝進度=4 | 對應于修復任務的執行狀態。
      安裝完成*5 | 表示安裝已完成成功、部分成功或失敗。
      重新開機請求=6 | 表示修補程式安裝已完成，並且節點上有掛起的重新開機操作。
      重新開機不需要=7 |  表示在修補程式安裝完成後不需要重新開機。
      重新開機完成=8 | 表示重新開機已成功完成。
      操作完成=9 | Windows 更新操作已成功完成。
      操作中止 = 10 | 表示 Windows 更新操作已中止。

1. 在 POA 版本 1.4.0 及更高版本中，當節點更新嘗試完成時，節點代理服務上將發佈具有"WU操作狀態-[NodeName]"屬性的事件，以便在下次嘗試下載和安裝 Windows 更新時通知您。 這顯示在下圖中：

     [![Windows 更新操作狀態的圖像](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png#lightbox)

### <a name="diagnostics-logs"></a>診斷記錄

修補程式編排應用程式日誌作為服務結構運行時日誌的一部分收集。

您可以使用您選擇的診斷工具或管道捕獲日誌。 POA 使用以下固定提供程式 ID 通過[事件源記錄事件](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netframework-4.5.1)：

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>健康狀態報告

POA 還在以下情況下針對節點代理服務或協調員服務發佈運行狀況報告：

* 節點代理程式 NTService 關閉

   如果節點上的節點代理程式 NTService 關閉，就會產生節點代理程式服務的警告等級健康情況報告。

* 未啟用維修管理器服務

   如果在群集上找不到修復管理器服務，則為協調員服務生成警告級運行狀況報告。

## <a name="frequently-asked-questions"></a>常見問題集

**問：為什麼在 POA 運行時，為什麼我會看到我的群集處於錯誤狀態？**

答：在安裝過程中，POA 禁用或重新開機節點，這可能會導致群集不正常。

根據應用程式的策略，一個節點可以在修補操作期間關閉 *，或者*整個更新域可以同時關閉。

在 Windows 更新安裝結束時，重新開機後將重新啟用節點。

在下列範例中，因為兩個節點關閉，且違反 MaxPercentageUnhealthyNodes 原則，叢集會暫時進入錯誤狀態。 在開始修補操作之前，該錯誤是暫時的。

![健康情況不良之叢集的圖片](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

如果問題持續發生，請參閱〈疑難排解〉一節。

**問：如果 POA 處於警告狀態，我該怎麼辦？**

答：檢查針對應用程式發佈的運行狀況報告是否指示根本原因。 警告通常包含問題的詳細資料。 如果問題是暫時性的，應用程式應自動復原。

**問：如果我的群集不正常，並且需要執行緊急作業系統更新，該怎麼辦？**

答：當群集不正常時，POA 不會安裝更新。 嘗試將群集帶到正常狀態以解除阻止 POA 工作流。

**問：我應該將任務審批策略設置為群集的"NodeWise"還是"升級域Wise"？**

答："升級域Wise"設置通過並行修補屬於更新域的所有節點來加快群集整體修復。 在此過程中，屬於整個更新域的節點不可用（處於[*禁用*狀態](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabled)）。

相反，"NodeWise"設置一次只設置一個節點，這意味著整個群集修補可能需要更長的時間。 但是，在修補過程中，最多隻有一個節點不可用（處於*禁用*狀態）。

如果您的群集可以容忍在修補週期期間在 N-1 數個更新域上運行（其中 N 是群集上更新域的總數），則可以將策略設置為"UpgradeDomainWise"。 否則，將其設置為"NodeWise"。

**問：修補節點需要多長時間？**

答：修補節點可能需要幾分鐘（例如[，Windows Defender 定義更新](https://www.microsoft.com/en-us/wdsi/definitions)）到小時（例如[，Windows 累積更新](https://www.catalog.update.microsoft.com/Search.aspx?q=windows%20server%20cumulative%20update)）。 修補節點所需的時間主要取決於： 
 - 更新的大小。
 - 必須在修補視窗中應用的更新數。
 - 安裝更新、重新開機節點（如果需要）和完成重新開機後安裝步驟所需的時間。
 - VM 或電腦的性能以及網路條件。

**問：修補整個群集需要多長時間？**

答：修補整個群集所需的時間取決於：

- 修補節點所需的時間。

- 協調器服務的原則。 預設策略"NodeWise"導致一次只修補一個節點，這種方法比使用"升級域Wise"慢。 

   例如：如果節點需要 +1 小時進行修補，則修補包含 5 個更新域（每個包含 4 個節點）的 20 節點（相同類型節點）群集需要：
    - 對於"節點"：+20小時。
    - 對於"升級域"：+5小時。

- 群集負載。 每個修補操作都需要將客戶工作負載重新置放到群集中的其他可用節點。 在此期間，正在修補的節點將處於[*禁用*狀態](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabling)。 如果群集在峰值負載附近運行，則禁用過程將需要更長的時間。 因此，在這種緊張條件下，整個修補過程可能看起來很慢。

- 修補期間的群集運行狀況失敗。 [群集運行狀況](https://docs.microsoft.com/azure/service-fabric/service-fabric-health-introduction)的任何[下降](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet#System_Fabric_Health_HealthState_Error)都會中斷修補過程。 此問題將增加修補整個群集所需的總時間。

**問：為什麼我在 Windows 更新結果中看到一些更新是通過 REST API 獲得的，但不是在電腦上的 Windows 更新歷史記錄下獲得的？**

答：某些產品更新僅出現在其自己的更新或修補程式歷史記錄中。 例如，Windows Defender 更新可能顯示在 Windows 伺服器 2016 上的 Windows 更新歷史記錄中，也可能不顯示。

**問：POA 能否用於修補我的開發群集（單節點群集）？**

答：否，POA 不能用於修補單節點群集。 此限制是設計原因，因為[Service Fabric 系統服務](https://docs.microsoft.com/azure/service-fabric/service-fabric-technical-overview#system-services)或其他客戶應用將造成停機時間。 因此，修補修復作業永遠不會得到維修經理的批准。

**問：如何在 Linux 上修補叢集節點？**

答：要瞭解如何在 Linux 上協調更新，請參閱[Azure 虛擬機器縮放集自動作業系統映射升級](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade)。

**問：為什麼更新週期需要這麼長時間？**

答：查詢結果 JSON，輸入所有節點的更新週期，然後可以使用"操作開始時間和操作完成時間"嘗試找出在每個節點上更新安裝所佔用的時間。 

如果有一個大型時間視窗，其中沒有進行更新，群集可能處於錯誤狀態，因此，維修管理器無法批准任何 POA 修復任務。 如果更新安裝在任何節點上需要很長時間，則該節點可能在一段時間內尚未更新。 許多更新可能正在等待安裝，這可能會導致延遲。 

節點修補也可能被阻止，因為它處於*禁用*狀態。 這通常是因為禁用節點可能會導致仲裁或資料丟失的情況。

**問：為什麼在 POA 修補節點時必須禁用節點？**

答：POA 禁用具有 *"重新開機*"意圖的節點，該意圖將停止或重新分配在節點上運行的所有服務交換矩陣服務。 POA 這樣做是為了確保應用程式最終不會使用新舊 DLL 的組合，因此我們建議不要在不禁用節點的情況下修補節點。

**問：使用 POA 可以更新的最大節點數是多少？**

答：POA 使用服務交換矩陣修復管理器為更新節點創建修復任務。 但是，可以同時存在不超過 250 個維修任務。 目前，POA 同時為每個節點創建修復任務，因此 POA 可以更新群集中的不超過 250 個節點。 

## <a name="disclaimers"></a>免責聲明

- POA 代表使用者接受 Windows 更新的使用者授權合約。 可在應用程式的設定中選擇性地將此設定關閉。

- POA 收集遙測資料以跟蹤使用方式和性能。 應用程式的遙測會遵循 Service Fabric 執行階段遙測設定 (預設為開啟) 的設定。

## <a name="troubleshooting"></a>疑難排解

本節為修補節點的問題提供可能的故障排除解決方案。

### <a name="a-node-is-not-coming-back-to-up-state"></a>節點不會回到開啟狀態

* 節點可能處於*禁用*狀態，因為：

  - 安全性檢查擱置。 若要補救這種情況，請確定有足夠多健康情況良好的節點。

* 節點可能處於 *"已禁用"* 狀態，因為：

  - 它被手動禁用。
  - 由於正在進行的 Azure 基礎結構作業，它已禁用。
  - POA 暫時禁用它以修補節點。

* 節點可能會卡在關閉狀態，因為：

  - 它手動置於向下狀態。
  - 它正在進行重新開機（可能由 POA 觸發）。
  - 它有故障的 VM 或電腦，或者存在網路連接問題。

### <a name="updates-were-skipped-on-some-nodes"></a>已略過某些節點上的更新

POA 嘗試根據重新計畫策略安裝 Windows 更新。 服務會根據應用程式原則，嘗試復原節點並略過更新。

在這種情況下，系統會針對節點代理程式服務產生警告等級的健康情況報告。 Windows 更新結果還包含故障的可能原因。

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-is-being-installed"></a>安裝更新時，群集的運行狀況出錯

錯誤的 Windows 更新可能會降低特定節點或更新域上的應用程式或群集的運行狀況。 POA 停止任何後續的 Windows 更新操作，直到群集再次正常運行。

管理員必須進行干預，並確定應用程式或群集由於 Windows 更新而變得不正常的原因。

## <a name="poa-release-notes"></a>POA 版本資訊

>[!NOTE]
> 對於 POA 版本 1.4.0 及更高版本，您可以在 GitHub 上的[修補程式業務流程應用程式發佈頁面上](https://github.com/microsoft/Service-Fabric-POA/releases/)找到版本資訊和版本。

### <a name="version-110"></a>1.1.0 版
- 公開版本

### <a name="version-111"></a>1.1.1 版
- 修正 SetupEntryPoint of NodeAgentService 中的錯誤，該錯誤導致無法安裝 NodeAgentNTService。

### <a name="version-120"></a>版本 1.2.0

- 關於系統重新啟動工作流程的錯誤修正。
- 由於準備修復工作期間健康情況檢查未如預期般發生，而在 RM 工作建立時進行的錯誤修正。
- 將 Windows 服務的啟動模式從自動更改為延遲自動。

### <a name="version-121"></a>1.2.1 版

- 縮小叢集範圍工作流程中的錯誤修正。 針對屬於不存在節點的 POA 修復工作，導入了記憶體回收邏輯。

### <a name="version-122"></a>1.2.2 版

- 其他錯誤修正。
- 現在已簽署二進位檔。
- 已新增應用程式的 sfpkg 連結。

### <a name="version-130"></a>版本 1.3.0

- 現在，將 InstallWindowsOSOnlyUpdates 設定為 false 便會安裝所有可用的更新。
- 已變更停用自動更新的邏輯。 這會修正 Server 2016 和以上版本未停用自動更新的錯誤。
- 適用于高級用例的 POA 微服務的參數化放置約束。

### <a name="version-131"></a>1.3.1 版
- 修復了由於禁用自動更新失敗而在 Windows Server 2012 R2 上或更早版本無法正常工作的 POA 1.3.0 的回歸。 
- 修正 InstallWindowsOSOnlyUpdates 組態一律挑選為 True 的錯誤 (bug)。
- 將 InstallWindowsOSOnlyUpdates 的預設值變更為 False。

### <a name="version-132"></a>1.3.2 版
- 修復了影響節點上的修補生命週期的問題，如果有名稱是當前節點名稱的子集的節點。 對於此類節點，可能錯過修補或重新開機掛起。

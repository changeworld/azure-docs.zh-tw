---
title: 修補 Service Fabric 叢集中的 Windows 作業系統
description: 本文討論如何使用修補協調流程應用程式，將 Service Fabric 叢集上的作業系統修補作業自動化。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80366324"
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>修補 Service Fabric 叢集中的 Windows 作業系統

> 
> [!IMPORTANT]
> 從2019年4月30日起，不再支援 Patch 協調流程應用程式版本 1.2. *。 請務必升級至最新版本。

> [!NOTE]
> 在[您的虛擬機器擴展集上取得自動 OS 映射升級](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade)是讓作業系統在 Azure 中進行修補的最佳作法。 以虛擬機器擴展集為基礎的自動 OS 映射升級將需要在擴展集上具有銀級或更高的持久性。
>

 修補程式協調流程應用程式（POA）是 Azure Service Fabric 修復管理員服務的包裝函式，可針對非 Azure 託管的叢集啟用以設定為基礎的 OS 修補程式排程。 非 Azure 主控的叢集不需要 POA，但需要排程更新網域的修補程式安裝，才能修補 Service Fabric 叢集主機，而不會產生停機時間。

POA 是 Service Fabric 應用程式，可將 Service Fabric 叢集上的作業系統修補自動化，而不會產生停機時間。

POA 提供下列功能：

- **自動化的作業系統更新安裝**。 會自動下載並安裝作業系統更新。 叢集節點會視需要重新開機，而不會導致叢集停機。

- **叢集感知的修補和健康情況整合**。 當 POA 正在套用更新時，它會監視叢集節點的健全狀況。 叢集節點會一次更新一個節點或一次一個更新網域。 如果叢集的健全狀況因修補程式而關閉，則會停止修補，以避免已經惡化問題。

## <a name="internal-details-of-poa"></a>POA 的內部詳細資料

POA 是由下列子元件所組成：

- **協調器服務**︰是具狀態服務，負責：
    - 協調整個叢集上的 Windows Update 作業。
    - 儲存已完成之 Windows Update 作業的結果。

- **節點代理程式服務**︰是無狀態服務，在所有 Service Fabric 叢集節點上執行。 此服務負責：
    - 啟動節點代理程式 NTService。
    - 監視節點代理程式 NTService。

- **節點代理程式 NTService**：在較高層級權限 (系統) 執行的 Windows NT 服務。 相比之下，節點代理程式服務和協調器服務則是在較低層級權限 (網路服務) 執行。 此服務會負責執行下列所有叢集節點上的 Windows Update 作業：
    - 停用節點上的自動 Windows 更新。
    - 根據使用者提供的原則下載並安裝 Windows 更新。
    - 重新開機 Windows 更新安裝後的電腦。
    - 將 Windows Update 的結果上傳至協調器服務。
    - 如果作業在耗盡所有重試之後失敗，則報告健康情況報告。

> [!NOTE]
> POA 會使用 Service Fabric 修復管理員服務來停用或啟用節點，並執行健康情況檢查。 POA 所建立的修復工作會追蹤每個節點的 Windows Update 進度。

## <a name="prerequisites"></a>先決條件

> [!NOTE]
> 所需的最小 .NET Framework 版本為4.6。

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>啟用修復管理員服務（如果尚未執行）

POA 需要在叢集上啟用修復管理員服務。

#### <a name="azure-clusters"></a>Azure 叢集

銀級耐久性層中的 Azure 叢集依預設會啟用修復管理員服務。 黃金持久性層中的 Azure 叢集可能會或可能未啟用修復管理員服務，視這些叢集的建立時間而定。 銅級耐久性層中的 Azure 叢集預設不會啟用修復管理員服務。 如果服務已啟用，您可以在 Service Fabric Explorer 的 [系統服務] 區段中看到它正在執行。

##### <a name="the-azure-portal"></a>Azure 入口網站
當您設定叢集時，可以從 Azure 入口網站啟用修復管理員。 當您設定叢集時，請選取 [**附加元件功能**] 底下的 [**包含修復管理員**] 選項。

![從 Azure 入口網站啟用修復管理員的影像](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="the-azure-resource-manager-deployment-model"></a>Azure Resource Manager 部署模型
或者，您可以使用[Azure Resource Manager 部署模型](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)，在新的和現有的 Service Fabric 叢集上啟用修復管理員服務。 取得您想要部署之叢集的範本。 您可以使用範例範本，或建立自訂的 Azure Resource Manager 部署模型範本。 

若要使用[Azure Resource Manager 部署模型範本](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)來啟用修復管理員服務，請執行下列動作：

1. 檢查以確定`apiVersion` *ServiceFabric/* 叢集資源已設定為*2017-07-01-preview* 。 如果不同，您必須更新`apiVersion`至*2017-07-01-preview*或更新版本：

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

1. 藉由在`addonFeatures` `fabricSettings`區段之後新增下列區段來啟用修復管理員服務：

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. 當您使用這些變更來更新叢集範本之後，請套用它們，然後讓更新完成。 您現在可以看到在您的叢集中執行的修復管理員服務。 在 Service Fabric Explorer 的 [系統服務] 區段中，它稱為*fabric：/system/RepairManagerService* 。 

### <a name="standalone-on-premises-clusters"></a>獨立的內部部署叢集

若要在新的或現有的 Service Fabric 叢集上啟用修復管理員服務，您可以使用[獨立 Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest)叢集的設定。

若要啟用修復管理員服務：

1. 檢查以確定`apiVersion`在[[一般](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest#general-cluster-configurations)叢集設定] 中，已設為*04-2017*或更新版本，如下所示：

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

1. 藉由在`addonFeatures` `fabricSettings`區段之後新增下列區段來啟用修復管理員服務，如下所示：

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

1. 使用更新後的叢集資訊清單，以這些變更更新您的叢集資訊清單[建立新](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-for-windows-server)叢集或[升級](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-windows-server)叢集設定。 

   使用更新後的叢集資訊清單執行叢集之後，您可以看到在您的叢集中執行的修復管理員服務。 它稱為*fabric：/System/RepairManagerService*，它位於 Service Fabric Explorer 的系統服務區段中。

### <a name="configure-windows-updates-for-all-nodes"></a>設定所有節點的 Windows 更新

自動 Windows 更新可能會導致可用性遺失，因為多個叢集節點可以同時重新開機。 根據預設，POA 會嘗試停用每個叢集節點上的自動 Windows 更新。 不過，如果設定是由系統管理員或群組原則所管理，建議您將 Windows Update 原則明確設定為「下載前通知」。

## <a name="download-the-application-package"></a>下載應用程式套件

若要下載應用程式封裝，請移至 GitHub 上的[Patch 協調流程應用程式版本頁面](https://github.com/microsoft/Service-Fabric-POA/releases/latest/)。

## <a name="configure-poa-behavior"></a>設定 POA 行為

您可以設定 POA 行為，以符合您的需求。 當您建立或更新應用程式時，藉由傳入應用程式參數來覆寫預設值。 您可以藉由指定`ApplicationParameter` `Start-ServiceFabricApplicationUpgrade`或`New-ServiceFabricApplication` Cmdlet 來提供應用程式參數。

| 參數        | 類型                          | 詳細資料 |
|:-|-|-|
|MaxResultsToCache    |long                              | 應快取的 Windows Update 結果數目上限。 <br><br>預設值為3000，假設： <br> &nbsp;&nbsp;-節點數目為20。 <br> &nbsp;&nbsp;-每個月對節點的更新數目為5。 <br> &nbsp;&nbsp;-每個作業的結果數目可以是10。 <br> &nbsp;&nbsp;-應該儲存過去三個月的結果。 |
|TaskApprovalPolicy   |例舉 <br> { NodeWise, UpgradeDomainWise }                          |TaskApprovalPolicy 會指出協調器服務在 Service Fabric 叢集節點中用來安裝 Windows 更新的原則。<br><br>允許的值包括： <br>*NodeWise*： Windows 更新會一次安裝一個節點。 <br> *UpgradeDomainWise*： Windows 更新會一次安裝一個更新網域。 （最多，屬於更新網域的所有節點都可以進行 Windows update）。<br><br> 若要協助決定哪一個原則最適合您的叢集，請參閱[常見問題](#frequently-asked-questions)一節。
|LogsDiskQuotaInMB   |long  <br> （預設值： *1024*）               | 修補程式協調流程應用程式記錄檔的大小上限（以 MB 為單位），可以在本機節點上保存。
| WUQuery               | 字串<br>（預設值： *IsInstalled = 0*）                | 用以取得 Windows 更新的查詢。 如需詳細資訊，請參閱 [WuQuery](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)。
| InstallWindowsOSOnlyUpdates | *布林值* <br> (預設值：False)                 | 使用此旗標可控制所應下載並安裝的更新。 允許下列值 <br>true - 只安裝 Windows 作業系統的更新。<br>false - 在電腦上安裝所有可用的更新。          |
| WUOperationTimeOutInMinutes | Int <br>（預設值： *90*）                   | 指定任何 Windows Update 作業的逾時 (搜尋或下載或安裝)。 如果作業未在指定的逾時內完成，它就會中止。       |
| WURescheduleCount     | Int <br> （預設值： *5*）                  | 如果作業持續失敗，服務會將 Windows update 重新排程的次數上限。          |
| WURescheduleTimeInMinutes | Int <br>（預設值： *30*） | 如果失敗持續發生，服務重新排定 Windows 更新的間隔。 |
| WUFrequency           | 以逗號分隔的字串（預設值：*每週、星期三、7:00:00*）     | 安裝 Windows 更新的頻率。 格式與可能的值如下： <br>&nbsp;&nbsp;-每月： DD，HH： MM： SS （例如，*每月，5，12：22： 32*）<br>欄位 DD （日）允許的值為1到28和 "last" 的數位。 <br> &nbsp;&nbsp;-每週、日、HH： MM： SS （例如，*每週、星期二、12:22:32*）  <br> &nbsp;&nbsp;-每天，HH： MM： SS （例如，*每日，12:22:32*）  <br> &nbsp;&nbsp;-  [*無*] 表示不應執行 Windows 更新。  <br><br> 時間為 UTC。|
| AcceptWindowsUpdateEula | 布林值 <br>（預設值： *true*） | 藉由設定這個旗標，應用程式會代表電腦的擁有者接受 Windows Update 的使用者授權合約 (EULA)。              |

> [!TIP]
> 如果您想要讓 Windows update 立即發生， `WUFrequency`請設定 [相對於應用程式部署時間]。 例如，假設您的測試叢集有五個節點，計劃於大約下午 5:00 UTC 部署應用程式。 如果您假設應用程式升級或部署最多需要30分鐘的時間，請將 Wufrequency 設定設定為*每日，17:30:00*。

## <a name="deploy-poa"></a>部署 POA

1. 完成準備叢集的所有必要條件步驟。
1. 部署 POA，就像任何其他 Service Fabric 應用程式一樣。 若要使用 PowerShell 部署它，請參閱[使用 Powershell 部署和移除應用程式](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications)。
1. 若要在部署時設定應用程式，可將 `ApplicationParameter` 傳遞給 `New-ServiceFabricApplication` Cmdlet。 為了您的方便，我們提供了 Deploy.ps1 指令碼以及我們的應用程式。 若要使用指令碼：

    - 使用 `Connect-ServiceFabricCluster` 連線至 Service Fabric 叢集。
    - 利用適當的 `ApplicationParameter` 值執行 Powershell 指令碼 Deploy.ps1。

> [!NOTE]
> 將腳本和應用程式資料夾*PatchOrchestrationApplication*放在相同的目錄中。

## <a name="upgrade-poa"></a>升級 POA

若要使用 PowerShell 升級您的 POA 版本，請遵循[使用 powershell Service Fabric 應用程式升級](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell)中的指示。

## <a name="remove-poa"></a>移除 POA

若要移除應用程式，請遵循[使用 PowerShell 部署和移除應用程式](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications)中的指示。

為了方便起見，我們提供瞭解除部署的 ps1 腳本以及應用程式。 若要使用指令碼：

  - 使用 ```Connect-ServiceFabricCluster``` 連線至 Service Fabric 叢集。
  - 執行 Powershell 指令碼 Undeploy.ps1。

> [!NOTE]
> 將腳本和應用程式資料夾*PatchOrchestrationApplication*放在相同的目錄中。

## <a name="view-the-windows-update-results"></a>檢視 Windows Update 結果

POA 會公開 REST Api，以向使用者顯示歷程記錄結果。 以下是結果 JSON 的範例：

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

下表描述 JSON 欄位：

欄位 | 值 | 詳細資料
-- | -- | --
OperationResult | 0 - 成功<br> 1 - 成功但發生錯誤<br> 2 - 失敗<br> 3 - 已中止<br> 4 - 中止但逾時 | 指出整體作業的結果，通常會牽涉到安裝一或多個更新。
ResultCode | 與 OperationResult 相同 | 此欄位指出個別更新之安裝作業的結果。
OperationType | 1 - 安裝<br> 0-搜尋和下載| 根據預設，安裝是顯示在結果中的唯一 OperationType。
WindowsUpdateQuery | 預設值為 "IsInstalled=0" | 用來搜尋更新的 Windows Update 查詢。 如需詳細資訊，請參閱[WuQuery](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)。
RebootRequired | true - 需要重新開機<br> false - 不需要重新開機 | 指出是否需要重新開機才能完成更新的安裝。
OperationStartTime | Datetime | 表示作業（下載/安裝）開始的時間。
OperationTime | Datetime | 表示作業（下載/安裝）已完成的時間。
HResult | 0-成功<br> 其他-失敗| 指出具有 updateID "7392acaf-6a85-427c-8a8d-058c25beb0d6" 的 Windows update 失敗原因。

如果尚未排程更新，JSON 結果會是空的。

登入叢集以查詢 Windows Update 結果。 找出協調員服務主要位址的複本 IP 位址，並從瀏覽器開啟下列&lt;URL： HTTP://replica-IP&gt;：&lt;ApplicationPort/PatchOrchestrationApplication/v1/GetWindowsUpdateResults.&gt;

協調器服務的 REST 端點具有動態連接埠。 若要檢查確切的 URL，請參閱 Service Fabric Explorer。 例如，您可以從*http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults*取得結果。

![REST 端點的影像](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)

如果在叢集上啟用反向 proxy，您也可以從叢集外部存取 URL。

您需要叫用的端點是*HTTP://&lt;SERVERURL&gt;：&lt;REVERSEPROXYPORT&gt;/PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults*。

若要在叢集上啟用反向 proxy，請遵循[Azure 中的反向 proxy Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy)中的指示。 

> 
> [!WARNING]
> 設定反向 proxy 之後，叢集中所有公開 HTTP 端點的微服務都可從叢集外部定址。

## <a name="diagnostics-and-health-events"></a>診斷和健康情況事件

本節討論如何在 Service Fabric 叢集上透過 POA 來檢查或診斷修補更新的問題。

> [!NOTE]
> 若要取得下列許多稱為「自我診斷」改良功能，您應該已安裝 POA version 1.4.0 或更新版本。

節點代理程式 NTService 會建立[修復](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtask?view=azure-dotnet)工作，以便在節點上安裝更新。 然後，協調器服務會根據工作核准原則來準備每個工作。 最後，修復管理員會核准備妥的工作，如果叢集處於狀況不良狀態，則不會核准任何工作。 

為了協助您瞭解更新如何在節點上繼續進行，讓我們逐步執行：

1. 在每個節點上執行的 NodeAgentNTService，會在排程的時間尋找可用的 Windows 更新。 如果有可用的更新，它會將它們下載到節點上。

1. 下載更新之後，節點代理程式 NTService 會為名稱為*\<POS___ unique_id>* 的節點建立對應的修復工作。 您可以使用[ServiceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps) Cmdlet 或在 [節點詳細資料] 區段中使用 SFX 來查看這些修復工作。 建立修復工作之後，它很快就會移至[*宣告*的狀態](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtaskstate?view=azure-dotnet)。

1. 協調器服務會定期尋找已*宣告*狀態的修復工作，然後更新它們以根據 taskapprovalpolicy 會*準備*狀態。 如果 Taskapprovalpolicy 會設定為 NodeWise，則只有在沒有其他修復工作正在*準備*、*核准*、*執行*或*還原*狀態時，才會備妥對應至節點的修復工作。 

   同樣地，在 UpgradeWise Taskapprovalpolicy 會的案例中，上述狀態中的工作僅適用于屬於相同更新網域的節點。 將修復工作移至*準備*狀態之後，會[停](https://docs.microsoft.com/powershell/module/servicefabric/disable-servicefabricnode?view=azureservicefabricps)用對應的 Service Fabric 節點，意圖設定為 [*重新開機*]。

   POA 版本1.4.0 和更新版本會在 CoordinatorService 上使用 ClusterPatchingStatus 屬性張貼事件，以顯示正在修補的節點。 更新會安裝在 _poanode_0 上，如下圖所示：

    [![叢集修補狀態的影像](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png)](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png#lightbox)

1. 停用節點之後，修復工作會移至 [*執行*中] 狀態。 
   
   > [!NOTE]
   > 停滯為*停*用狀態的節點可以封鎖新的修復工作，這會停止叢集上的修補作業。

1. 當修復工作處於 [*執行*中] 狀態時，該節點上的修補程式安裝就會開始。 安裝修補程式之後，節點可能會或可能不會重新開機，視修補程式而定。 接下來，修復工作會移至*還原*狀態，以 reenables 節點。 然後，修復工作會標示為已完成。

   在 POA 版本1.4.0 和更新版本中，您可以使用 WUOperationStatus-\<NodeName> 屬性來查看 NodeAgentService 的健康情況事件，以尋找更新的狀態。 下圖中反白顯示的區段會顯示*poanode_0*和*poanode_2*節點上的 Windows 更新狀態：

   [![Windows Update 作業狀態的影像](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png#lightbox)

   [![Windows Update 作業狀態的影像](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png#lightbox)

   您也可以使用 PowerShell 來取得詳細資料。 若要這麼做，您可以使用[ServiceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps)連接到叢集，並提取修復工作的狀態。 
   
   在下列範例中，"POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15" 工作處於*DownloadComplete*狀態。 這表示已在*poanode_2*節點上下載更新，並會在工作移至*執行*狀態時嘗試安裝。

   ``` powershell
    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k = Get-ServiceFabricRepairTask -TaskId "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15"

    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k.ExecutorData
    {"ExecutorSubState":2,"ExecutorTimeoutInMinutes":90,"RestartRequestedTime":"0001-01-01T00:00:00"}
    ```

   如果仍然發現更多問題，請使用 Windows 事件記錄檔登入您的虛擬機器（VM）或 Vm，並瞭解它們。 先前提到的修復工作只能存在於下列執行程式 substates 中：

      ExecutorSubState | 描述
    -- | -- 
      無 = 1 |  表示節點上沒有進行中的作業。 狀態可能是「轉換中」。
      DownloadCompleted = 2 | 表示下載作業已完成，但發生「成功」、「部分失敗」或「失敗」。
      InstallationApproved = 3 | 表示下載作業已稍早完成，修復管理員已核准安裝。
      InstallationInProgress = 4 | 對應至執行修復工作的狀態。
      InstallationCompleted = 5 | 表示安裝已完成，但成功、部分成功或失敗。
      RestartRequested = 6 | 表示修補程式安裝已完成，且節點上有暫止的重新開機動作。
      RestartNotNeeded = 7 |  表示在修補程式安裝完成之後，不需要重新開機。
      RestartCompleted = 8 | 表示重新開機已順利完成。
      OperationCompleted = 9 | 已成功完成 Windows Update 作業。
      OperationAborted = 10 | 表示 Windows Update 操作已中止。

1. 在 POA 版本1.4.0 和更新版本中，當節點更新嘗試完成時，會在 NodeAgentService 上公佈具有 "WUOperationStatus-[NodeName]" 屬性的事件，以在下一次嘗試下載並安裝 Windows 更新時通知您。 這會顯示在下圖中：

     [![Windows Update 作業狀態的影像](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png#lightbox)

### <a name="diagnostics-logs"></a>診斷記錄

修補協調流程應用程式記錄檔會收集為 Service Fabric 執行時間記錄的一部分。

您可以使用您選擇的診斷工具或管線來捕捉記錄。 POA 會使用下列固定提供者識別碼，透過[事件來源](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netframework-4.5.1)記錄事件：

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>健康狀態報告

POA 也會在下列案例中，針對節點代理程式服務或協調器服務發佈健康情況報告：

* 節點代理程式 NTService 關閉

   如果節點上的節點代理程式 NTService 關閉，就會產生節點代理程式服務的警告等級健康情況報告。

* 未啟用修復管理員服務

   如果在叢集上找不到修復管理員服務，就會產生協調器服務的警告層級健康情況報告。

## <a name="frequently-asked-questions"></a>常見問題集

**問：當 POA 正在執行時，為什麼會看到我的叢集處於錯誤狀態？**

答：在安裝過程中，POA 會停用或重新開機節點，這可能會暫時導致狀況不良的叢集。

根據應用程式的原則，可能會有一個節點在修補作業期間關閉，*或*整個更新網域會一次關閉。

在 Windows update 安裝結束時，節點會在重新開機後重新啟用。

在下列範例中，因為兩個節點關閉，且違反 MaxPercentageUnhealthyNodes 原則，叢集會暫時進入錯誤狀態。 此錯誤是暫時性的，直到修補作業可以開始為止。

![健康情況不良之叢集的圖片](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

如果問題持續發生，請參閱〈疑難排解〉一節。

**問：如果 POA 處於警告狀態，我可以做什麼？**

答：請檢查針對應用程式所公佈的健康情況報告是否指出根本原因。 警告通常包含問題的詳細資料。 如果問題是暫時性的，應用程式應該會自動復原。

**問：如果我的叢集狀況不良，而且我需要進行緊急的作業系統更新，該怎麼辦？**

答： POA 不會在叢集狀況不良時安裝更新。 嘗試讓叢集處於狀況良好的狀態，以解除封鎖 POA 工作流程。

**問：我應該將叢集的 Taskapprovalpolicy 會設為 "NodeWise" 或 "UpgradeDomainWise" 嗎？**

答：「UpgradeDomainWise」設定會以平行方式修補屬於更新網域的所有節點，以加速整體叢集修復。 在此過程中，屬於整個更新網域的節點無法使用（處於[*已停*用狀態](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabled)）。

相反地，「NodeWise」設定一次只會修補一個節點，這表示整體叢集修補可能需要較長的時間。 不過，在修補程式期間，最多隻會有一個節點無法使用（處於*已停*用狀態）。

如果您的叢集可容忍在修補週期期間在 N-1 個更新網域上執行（其中 N 是叢集上的更新網域總數），您可以將原則設定為「UpgradeDomainWise」。 否則，請將它設定為 "NodeWise"。

**問：修補節點需要多久的時間？**

答：修補節點可能需要幾分鐘的時間（例如， [Windows Defender 定義更新](https://www.microsoft.com/en-us/wdsi/definitions)）到數小時（例如[windows 累計更新](https://www.catalog.update.microsoft.com/Search.aspx?q=windows%20server%20cumulative%20update)）。 修補節點所需的時間主要取決於： 
 - 更新的大小。
 - 必須在修補視窗中套用的更新數目。
 - 安裝更新所需的時間、重新開機節點（如有必要），並完成重新開機後的安裝步驟。
 - VM 或電腦的效能，以及網路狀況。

**問：修補整個叢集需要多久的時間？**

答：修補整個叢集所需的時間取決於：

- 修補節點所需的時間。

- 協調器服務的原則。 預設原則 "NodeWise" 會導致一次只修補一個節點，這種方法比使用 "UpgradeDomainWise" 慢。 

   例如：如果節點需要約1小時來修補，請使用5個更新網域來修補20個節點（相同類型的節點）叢集，其中每個都包含4個節點，需要：
    - 針對 "NodeWise"： ~ 20 小時。
    - 針對 "UpgradeDomainWise"： ~ 5 小時。

- 叢集負載。 每次修補作業都需要將客戶工作負載重新放置到叢集中的其他可用節點。 在這段期間，正在修補的節點會處於[*停*用狀態](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabling)。 如果叢集在接近尖峰負載的情況下執行，則停用的程式會花費較長的時間。 因此，在這種負荷條件下，整體修補程式可能會變慢。

- 修補期間的叢集健康狀態失敗。 叢集的[健全狀況](https://docs.microsoft.com/azure/service-fabric/service-fabric-health-introduction)[降低](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet#System_Fabric_Health_HealthState_Error)會中斷修補程式。 此問題會加到修補整個叢集所需的整體時間。

**問：為什麼我會在 Windows Update 結果中看到透過 REST API 取得的部分更新，但不會在電腦上的 Windows Update 歷程記錄中顯示？**

答：某些產品更新只會出現在自己的更新或修補歷程記錄中。 例如，windows Defender 更新不一定會顯示在 Windows Server 2016 上的 Windows Update 歷程記錄中。

**問： POA 可以用來修補我的開發叢集（單節點叢集）嗎？**

答：否，POA 不能用來修補單節點叢集。 這項限制是根據設計，因為[Service Fabric 系統服務](https://docs.microsoft.com/azure/service-fabric/service-fabric-technical-overview#system-services)或其他客戶應用程式會產生停機時間。 因此，修復管理員不會核准修復工作的修補作業。

**問：在 Linux 上如何? 修補叢集節點？**

答：若要瞭解如何在 Linux 上協調更新，請參閱[Azure 虛擬機器擴展集的自動 OS 映射升級](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade)。

**問：為什麼更新週期會花很長的時間？**

答：查詢結果 JSON、輸入所有節點的更新週期，然後您可以嘗試使用 OperationStartTime 和 OperationTime （OperationCompletionTime）來找出每個節點上的更新安裝所花費的時間。 

如果有很大的時間範圍沒有發生任何更新，叢集可能處於錯誤狀態，因此修復管理員無法核准任何 POA 修復工作。 如果更新安裝在任何節點上花費了很長的時間，表示該節點在一段期間內可能尚未更新。 有很多更新可能會擱置安裝，這可能會導致延遲。 

可能也有可能因為節點修補停滯在*停*用狀態而遭到封鎖。 這種情況通常是因為停用節點可能會導致仲裁或資料遺失的情況。

**問：當 POA 修補時，為何必須停用節點？**

答： POA 會停用具有*重新開機*意圖的節點，這會停止或重新配置節點上執行的所有 Service Fabric 服務。 POA 會執行這項操作，以確保應用程式不會使用新的和舊的 Dll 混合，因此建議您不要在不停用的情況下修補節點。

**問：使用 POA 可以更新的節點數目上限為何？**

答： POA 會使用 Service Fabric 修復管理員來建立更新節點的修復工作。 不過，同時不能有250個以上的修復工作。 目前，POA 會同時為每個節點建立修復工作，因此 POA 可以更新叢集中的節點數不超過250個。 

## <a name="disclaimers"></a>免責聲明

- POA 會代表使用者接受 Windows Update 的使用者授權合約。 可在應用程式的設定中選擇性地將此設定關閉。

- POA 會收集遙測來追蹤使用方式和效能。 應用程式的遙測會遵循 Service Fabric 執行階段遙測設定 (預設為開啟) 的設定。

## <a name="troubleshooting"></a>疑難排解

本節提供修補節點問題的可能疑難排解解決方案。

### <a name="a-node-is-not-coming-back-to-up-state"></a>節點不會回到開啟狀態

* 節點可能會卡在*停*用狀態，因為：

  - 安全性檢查擱置。 若要補救這種情況，請確定有足夠多健康情況良好的節點。

* 節點可能會卡在*停*用狀態，因為：

  - 它已手動停用。
  - 因為進行中的 Azure 基礎結構作業，所以已停用此功能。
  - POA 會暫時停用此功能，以修補節點。

* 節點可能會卡在關閉狀態，因為：

  - 它是以手動方式置於關閉狀態。
  - 正在進行重新開機（可能是由 POA 所觸發）。
  - 它有故障的 VM 或電腦，或網路連線有問題。

### <a name="updates-were-skipped-on-some-nodes"></a>已略過某些節點上的更新

POA 會根據重新排定的原則，嘗試安裝 Windows update。 服務會根據應用程式原則，嘗試復原節點並略過更新。

在這種情況下，系統會針對節點代理程式服務產生警告等級的健康情況報告。 Windows Update 的結果也會包含失敗的可能原因。

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-is-being-installed"></a>安裝更新時，叢集的健全狀況會變成錯誤

有問題的 Windows update 可以關閉特定節點或更新網域上的應用程式或叢集的健全狀況。 POA 會停止任何後續的 Windows Update 作業，直到叢集再次狀況良好為止。

系統管理員必須介入，並判斷應用程式或叢集因 Windows Update 而變得狀況不良的原因。

## <a name="poa-release-notes"></a>POA 版本資訊

>[!NOTE]
> 針對 POA 版本的1.4.0 和更新版本，您可以在 GitHub 上的[修補程式協調流程應用程式版本頁面](https://github.com/microsoft/Service-Fabric-POA/releases/)上找到版本資訊和版本。

### <a name="version-110"></a>1.1.0 版
- 公開版本

### <a name="version-111"></a>1.1.1 版
- 修正 SetupEntryPoint of NodeAgentService 中的錯誤，該錯誤導致無法安裝 NodeAgentNTService。

### <a name="version-120"></a>版本 1.2.0

- 關於系統重新啟動工作流程的錯誤修正。
- 由於準備修復工作期間健康情況檢查未如預期般發生，而在 RM 工作建立時進行的錯誤修正。
- 已將 Windows 服務 POANodeSvc 的啟動模式從 [自動] 變更為 [延遲-自動]。

### <a name="version-121"></a>1.2.1 版

- 縮小叢集範圍工作流程中的錯誤修正。 針對屬於不存在節點的 POA 修復工作，導入了記憶體回收邏輯。

### <a name="version-122"></a>1.2.2 版

- 其他錯誤修正。
- 現在已簽署二進位檔。
- 已新增應用程式的 sfpkg 連結。

### <a name="version-130"></a>版本 1.3.0

- 現在，將 InstallWindowsOSOnlyUpdates 設定為 false 便會安裝所有可用的更新。
- 已變更停用自動更新的邏輯。 這會修正 Server 2016 和以上版本未停用自動更新的錯誤。
- POA 微服務的參數化放置條件約束，適用于 advanced 使用案例。

### <a name="version-131"></a>1.3.1 版
- 修正因停用自動更新而無法在 Windows Server 2012 R2 或更早版本上使用 POA 1.3.0 的回歸。 
- 修正 InstallWindowsOSOnlyUpdates 組態一律挑選為 True 的錯誤 (bug)。
- 將 InstallWindowsOSOnlyUpdates 的預設值變更為 False。

### <a name="version-132"></a>1.3.2 版
- 修正會影響節點上修補生命週期的問題（如果有節點的名稱是目前節點名稱的子集）。 針對這類節點，可能會遺漏修補或重新開機擱置中。

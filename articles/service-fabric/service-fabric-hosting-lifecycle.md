---
title: Azure Service Fabric 裝載啟用和停用生命週期
description: 瞭解應用程式的生命週期，以及節點上的 ServicePackage。
author: tugup
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: tugup
ms.openlocfilehash: d8585d0b39e4a4ef9cf77f40ea878ddb47bcb0de
ms.sourcegitcommit: beacda0b2b4b3a415b16ac2f58ddfb03dd1a04cf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/31/2020
ms.locfileid: "97831817"
---
# <a name="azure-service-fabric-hosting-life-cycle"></a>Azure Service Fabric 裝載生命週期

本文概述在節點上啟用應用程式時，Azure Service Fabric 中發生的事件。 它會說明各種控制行為的叢集設定。

繼續之前，請確定您已瞭解在 Service Fabric 中為 [應用程式建立模型][a1]所說明的概念和關聯性。 

> [!NOTE]
> 本文以特殊方式使用一些術語。 除非另有說明：
>
> - 「複本」是指具狀態服務的複本和無狀態服務的執行個體。
> - *CodePackage* 被視為相當於註冊 ServiceType 的 ServiceHost 進程。 它會裝載該 ServiceType 的服務複本。
>

## <a name="activate-an-applicationpackage-or-servicepackage"></a>啟用 ApplicationPackage 或 ServicePackage

若要啟用 ApplicationPackage 或 ServicePackage：

1. 下載 ApplicationPackage (例如 *ApplicationManifest.xml*) 。
2. 設定應用程式的環境。 例如，您的步驟包括建立使用者。
3. 開始追蹤應用程式以進行停用。
4. 下載 ServicePackage (例如 *ServiceManifest.xml*、程式碼、設定檔，以及) 的資料套件。
5. 設定 ServicePackage 的環境。 例如，您的步驟包括設定防火牆和配置端點的埠。
6. 開始追蹤停用的 ServicePackage。
7. 啟動 Codepackage 的 SetupEntryPoint，並等候它完成。
8. 啟動 Codepackage 的 MainEntryPoint。

### <a name="servicetype-blocklisting"></a>ServiceType 封鎖
`ServiceTypeDisableFailureThreshold` 決定允許的啟用、下載和 CodePackage 失敗的數目。 達到閾值之後，會為 ServiceType 排程封鎖。 第一個啟用失敗、下載失敗或 CodePackage 損毀會排程 ServiceType 封鎖。 

設定 `ServiceTypeDisableGraceInterval` 會決定在節點上封鎖 ServiceType 之前的寬限間隔。 當此程式啟動時，啟動、下載和 CodePackage 重新開機會以平行方式重試。 例如，重試表示 CodePackage 已排定在當機之後再次啟動，或 Service Fabric 將會再次嘗試下載套件。

當 ServiceType 封鎖時，您會看到健全狀況錯誤： `'System.Hosting' reported Error for property 'ServiceTypeRegistration:ServiceType'. The ServiceType was disabled on the node.`

如果發生下列任何事件，就會在節點上再次啟用 ServiceType：
- 啟用成功。 如果失敗，則會達到 `ActivationMaxFailureCount` 重試次數上限。
- 下載成功。 如果失敗，則會達到 `DeploymentMaxFailureCount` 重試次數上限。
- 已損毀的 CodePackage 會啟動，並成功註冊 ServiceType。

`ActivationMaxFailureCount` 和 `DeploymentMaxFailureCount` 是在節點上啟動或下載應用程式 Service Fabric 所需的最大嘗試次數。 達到最大值之後，Service Fabric 可讓 ServiceType 再次啟用。 

這些閾值提供服務另一個啟用機會。 如果啟用成功，則會自動修復問題。 

新放置或啟用的複本可以觸發新的啟用或下載作業。 此動作可能會成功，或再次觸發 ServiceType 封鎖。

> [!NOTE]
> 未註冊 ServiceType 的損毀 CodePackage 不會影響 ServiceType。 只有裝載複本的損毀 CodePackage 會影響 ServiceType。
>

### <a name="codepackage-crash"></a>CodePackage 損毀
當 CodePackage 損毀時，Service Fabric 會使用輪詢重新開機它。 無論 CodePackage 是否已向 Service Fabric 執行時間註冊型別，都會套用輪詢。

輪詢值為 `Min(RetryTime, ActivationMaxRetryInterval)` 。 此值會根據設定的設定，以常數、線性或指數量遞增 `ActivationRetryBackoffExponentiationBase` ：

- **常數**： If `ActivationRetryBackoffExponentiationBase == 0` 、then `RetryTime = ActivationRetryBackoffInterval` 。
- **線性**：如果  `ActivationRetryBackoffExponentiationBase == 0` ，則 `RetryTime = ContinuousFailureCount ActivationRetryBackoffInterval` 為，其中 `ContinuousFailureCount` 是 CodePackage 損毀或無法啟動的次數。
- **指數**： `RetryTime = (ActivationRetryBackoffInterval in seconds) * (ActivationRetryBackoffExponentiationBase ^ ContinuousFailureCount)` 。
    
您可以藉由變更值來控制行為。 例如，如果您想要數個快速重新開機嘗試，您可以將設定 `ActivationRetryBackoffExponentiationBase` 為 `0` ，並將設定為，以使用線性數量 `ActivationRetryBackoffInterval` `10` 。 因此，如果 CodePackage 損毀，開始間隔將會是10秒。 如果封裝持續損毀，輪詢會變更為20秒、30秒、40秒等等，直到 CodePackage 啟用成功或停用 CodePackage 為止。 
    
Service Fabric 關閉的時間量上限 (也就是，在控制失敗之後等候) `ActivationMaxRetryInterval` 。
    
如果您的 CodePackage 損毀並恢復運作，則必須隨時掌握所指定的時間週期 `CodePackageContinuousExitFailureResetInterval` 。 在此間隔之後，Service Fabric 會將 CodePackage 視為狀況良好。 Service Fabric 接著將先前的錯誤健康情況報告覆寫為 [確定]，然後重設 `ContinuousFailureCount` 。

### <a name="codepackage-not-registering-servicetype"></a>CodePackage 未註冊 ServiceType
如果 CodePackage 保持不變，而且預期會註冊 ServiceType 但不會，Service Fabric 會在之後產生警告健康情況報告 `ServiceTypeRegistrationTimeout` 。 報告指出 ServiceType 未在預期的時間內註冊。

### <a name="activation-failure"></a>啟用失敗
當 Service Fabric 在啟用期間發現錯誤時，它一律會使用線性輪詢，如同 CodePackage 損毀一樣。 啟動作業會在一段時間後重試： (0 + 10 + 20 + 30 + 40) = 100 秒。  (第一次重試是立即的。在此序列之後 ) ，就不會重試啟用。
    
最大啟用輪詢可以是 `ActivationMaxRetryInterval` 。 重試可以是 `ActivationMaxFailureCount` 。

### <a name="download-failure"></a>下載失敗
當 Service Fabric 在下載期間發現錯誤時，一律會使用線性輪詢。 啟動作業會在一段時間後重試： (0 + 10 + 20 + 30 + 40) = 100 秒。  (第一次重試是立即的。在此序列之後 ) ，則不會重試下載。 

下載的線性輪詢等於 `ContinuousFailureCount`  *  `DeploymentRetryBackoffInterval` 。 最大輪詢可以是 `DeploymentMaxRetryInterval` 。 就像啟用一樣，下載作業可以重試 `ActivationMaxFailureCount` 限制。

> [!NOTE]
> 變更這些設定之前，請記住下列範例：
>
>* 如果 CodePackage 持續損毀並關閉備份，將會停用 ServiceType。 但是，如果啟用設定有快速重新開機，則 CodePackage 可能會出現幾次，然後才會實際封鎖 ServiceType。 
>
>    例如，假設您的 CodePackage 出現、向 Service Fabric 註冊 ServiceType，然後損毀。 在這種情況下，在裝載接收到型別註冊之後， `ServiceTypeDisableGraceInterval` 就會取消句號。 此程式可以重複執行，直到您的 CodePackage 備份到大於句點的值為止 `ServiceTypeDisableGraceInterval` 。 然後，系統會將 ServiceType 封鎖在節點上。 ServiceType 封鎖所花費的時間可能會超過您的預期。
>
>* 在啟用的情況下，當 Service Fabric 系統需要在節點上放置複本時，重新設定代理程式會要求裝載子系統啟動應用程式。 它會每隔15秒重試啟用要求。  (期間是由 `RAPMessageRetryInterval` 設定設定所管理。 ) Service Fabric 無法知道 ServiceType 已封鎖，除非裝載中的啟動作業所需的時間比重試間隔更長，以及 `ServiceTypeDisableGraceInterval` 。 
>
>    例如，假設叢集的 `ActivationMaxFailureCount` 設為5，而且 `ActivationRetryBackoffInterval` 設定為1秒。 在這種情況下，啟用作業會在 (0 + 1 + 2 + 3 + 4) = 10 秒後產生間隔。  (第一次重試是立即的。在此序列之後 ) ，裝載會重試。 啟用作業完成，且不會在15秒後重試。 
>
>    Service Fabric 已在15秒內用盡所有允許的重試次數。 因此，重新設定代理程式的每次重試會在主控子系統中建立新的啟用作業，而模式會保持重複。 因此，ServiceType 永遠不會在節點上封鎖。 因為 ServiceType 不會在節點上封鎖，所以不會在不同的節點上移動和嘗試此複本。
> 

## <a name="deactivation"></a>停用

在節點上啟用 ServicePackage 時，會追蹤該節點是否停用。 停用的運作方式有兩種：

- **定期停用**：每次 `DeactivationScanInterval` 系統都會檢查 *從未* 裝載複本的服務封裝，並將它們標示為停用的候選項目。
- **ReplicaClose 停用**：如果複本已關閉，則 deactivator 會取得 `DecrementUsageCount` 。 當 ServicePackage 未裝載任何複本時，計數為0，因此 ServicePackage 是停用的候選項。

啟用模式會決定何時排定候選項目的停用狀態。 在共用模式中，停用的候選項目會在之後排程 `DeactivationGraceInterval` 。 在獨佔模式中，則是在之後排程 `ExclusiveModeDeactivationGraceInterval` 。 如果新的複本位置在這些時間之間抵達，則會取消停用。 

如需詳細資訊，請參閱 [獨佔模式與共用模式][a2]。

### <a name="periodic-deactivation"></a>定期停用
以下是一些定期停用的範例：

* **範例 1**：假設 deactivator 開始掃描時 (`DeactivationScanInterval`) 。 其下一次掃描將會 T.inv.2t。 假設 ServicePackage 啟用發生于 T + 1。 此 ServicePackage 不會裝載複本，因此需要停用它。 

    若要成為停用的候選項，ServicePackage 至少必須裝載沒有任何複本的時間。 它將符合在 T.inv.2t + 1 停用的資格。 因此，T.inv.2t 的掃描不會將此 ServicePackage 識別為停用的候選。 

    下一個停用週期3T 會將此 ServicePackage 排程為停用，因為現在封裝已處於非複本的時間 T 狀態。  

* **範例 2**：假設 ServicePackage 是在時間 t 1 啟動，而 Deactivator 在 t 上開始掃描。ServicePackage 不會裝載複本。 在下一個掃描中，T.inv.2t 會將 ServicePackage 識別為停用的候選項，因此將會排程停用。  

* **範例 3**：假設 ServicePackage 是在 t-1 啟動，而 Deactivator 在 t 上啟動掃描。ServicePackage 尚未裝載複本。 現在在 T + 1，會放置一個複本。 也就是說，裝載會取得 `IncrementUsageCount` ，這表示會建立複本。 

    在 T.inv.2t，此 ServicePackage 不會排定停用。 因為封裝包含複本，所以停用會遵循 ReplicaClose 邏輯，如下一節的說明。

* **範例 4**：假設您的 ServicePackage 為 10 GB。 因為封裝很大，所以需要一些時間才能在節點上下載。 當應用程式啟用時，deactivator 會追蹤其生命週期。 如果 `DeactivationScanInterval` 設定為較小的值，您的 ServicePackage 可能沒有時間在節點上啟動，因為它需要下載時間。 若要解決這個問題，您可以 [在節點上事先下載 ServicePackage][p1] 或增加 `DeactivationScanInterval` 。 

> [!NOTE]
> ServicePackage 可在 (`DeactivationScanInterval` 到 2 *) + 之間的任何位置停用 `DeactivationScanInterval` `DeactivationGraceInterval` / `ExclusiveModeDeactivationGraceInterval` 。 
>

### <a name="replicaclose-deactivation"></a>ReplicaClose 停用

> [!NOTE]
> 本節參考下列設定：
> - **DeactivationGraceInterval** /**ExclusiveModeDeactivationGraceInterval**：指定給 ServicePackage 的時間，以裝載另一個複本（如果它已裝載任何複本）。 
> - **DeactivationScanInterval**：指定給 ServicePackage 的最短時間，以裝載複本（如果它 *從未* 裝載任何複本），也就是未使用。
>

系統會保留 ServicePackage 保存的複本計數。 如果 ServicePackage 持有複本，而複本已關閉或關閉，則裝載會取得 `DecrementUsageCount` 。 當複本開啟時，裝載會取得 `IncrementUsageCount` 。 

遞減表示 ServicePackage 所裝載的複本數目已減少一個複本。 當計數降到0時，就會將 ServicePackage 排程為停用。 將停用之後的時間 `DeactivationGraceInterval` / `ExclusiveModeDeactivationGraceInterval` 。 

例如，假設在 T 進行遞減，並排程在 T.inv.2t + X () 停用 ServicePackage `DeactivationGraceInterval` / `ExclusiveModeDeactivationGraceInterval` 。 在這段期間內，如果 `IncrementUsage` 因為建立複本而取得，則會取消停用。

### <a name="ctrl--c"></a>Ctrl + C
如果 ServicePackage 傳遞， `DeactivationGraceInterval` / `ExclusiveModeDeactivationGraceInterval` 但仍未裝載複本，則無法取消停用。 Codepackage 會接收 Ctrl + C 處理常式。 現在停用管線必須完成，才能讓程式停止運作。 

在這段期間內，如果相同 ServicePackage 的新複本正在嘗試放置，它將會失敗，因為進程無法從停用轉換成啟用。

## <a name="cluster-configurations"></a>叢集組態

此區段會列出具有會影響啟用和停用之預設值的設定。

### <a name="servicetype"></a>ServiceType
- **ServiceTypeDisableFailureThreshold**：預設值：1。 失敗計數的臨界值;達到此閾值之後，FailoverManager 會通知您停用節點上的服務類型，並嘗試使用不同的節點進行放置。
- **ServiceTypeDisableGraceInterval**：預設值：30秒。 可以停用服務類型的時間間隔。
- **>servicetyperegistrationtimeout**：預設值：300秒。 ServiceType 註冊 Service Fabric 的超時時間。

### <a name="activation"></a>啟用
- **ActivationRetryBackoffInterval**： Default：10秒。 每個啟用失敗的輪詢間隔。
- **ActivationMaxFailureCount**：預設值：20。 系統在放棄之前，會重試失敗啟用的最大計數。 
- **ActivationRetryBackoffExponentiationBase**：預設值：1.5。
- **ActivationMaxRetryInterval**：預設值：3600秒。 失敗後的啟用輪詢重試間隔上限。
- **CodePackageContinuousExitFailureResetInterval**：預設值：300秒。 用來重設 CodePackage 連續結束失敗計數的逾時間隔。

### <a name="download"></a>下載
- **DeploymentRetryBackoffInterval**： Default：10。 部署失敗的輪詢間隔。
- **DeploymentMaxRetryInterval**：預設值：3600秒。 失敗後部署的最大輪詢間隔。
- **DeploymentMaxFailureCount**：預設值：20。 在 `DeploymentMaxFailureCount` 節點上部署應用程式之前，應用程式部署將會重試一次。

### <a name="deactivation"></a>停用
- **DeactivationScanInterval**：預設值：600秒。 提供給 ServicePackage 的最短時間，如果它從未裝載任何複本，則為裝載複本 (也就是，如果未使用) 。
- **DeactivationGraceInterval**：預設值：60秒。 在 *共用* 進程模型中，提供給 ServicePackage 的時間會在它已裝載任何複本之後，再次裝載另一個複本。
- **ExclusiveModeDeactivationGraceInterval**：預設值：1秒。 在 *專屬* 進程模型中，提供給 ServicePackage 的時間會在它已裝載任何複本之後，再次裝載另一個複本。

## <a name="next-steps"></a>後續步驟

- [對應用程式進行封裝][a3]並使它準備好進行部署。
- 在 PowerShell 中[部署和移除應用程式][a4]。

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-hosting-model.md
[a3]: service-fabric-package-apps.md
[a4]: service-fabric-deploy-remove-applications.md

[p1]: /powershell/module/servicefabric/copy-servicefabricservicepackagetonode

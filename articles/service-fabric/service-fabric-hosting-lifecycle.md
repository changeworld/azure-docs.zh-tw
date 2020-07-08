---
title: Azure Service Fabric 裝載啟用和停用生命週期
description: 說明節點上應用程式和 ServicePackage 的生命週期
author: tugup
ms.topic: conceptual
ms.date: 05/1/2020
ms.author: tugup
ms.openlocfilehash: da395f020cf3d299211427e740975764653b487d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85802008"
---
# <a name="azure-service-fabric-hosting-lifecycle"></a>Azure Service Fabric 裝載生命週期
本文概述在節點上啟動應用程式時所發生的事件，以及用來控制行為的各種叢集管理。

在進一步進行之前，請確定您已了解[在 Service Fabric 中模型化應用程式][a1]中所說明的各種概念與關聯性。 

> [!NOTE]
> 在本文中，除非明確指出是不同意義，否則：
>
> - 「複本」（ *replica* ）是指具狀態服務的複本和無狀態服務的實例。
> - *CodePackage*會被視為等同于註冊*servicetype*並裝載該*Servicetype*之服務複本的*ServiceHost*進程。
>

## <a name="activation-of-applicationservicepackage"></a>啟用應用程式/ServicePackage

啟用的管線如下所示：

1. 下載 ApplicationPackage。 例如： ApplicationManifest.xml 等。
2. 設定應用程式的環境，例如：建立使用者等等。
3. 開始追蹤應用程式以停用。
4. 下載 ServicePackage。 例如： ServiceManifest.xml、程式碼、設定、資料套件等等。
5. 設定服務套件的環境，例如：設定防火牆、配置端點的埠等等。
6. 開始追蹤 ServicePackage 以進行停用。
7. 開始 SetupEntryPoint Codepackage 並等候完成。
8. 開始 MainEntryPoint Codepackage。

### <a name="servicetype-blocklisting"></a>ServiceType 封鎖
**ServiceTypeDisableFailureThreshold**會判斷失敗的數目（啟動、下載、CodePackage 失敗），之後會將 ServiceType 排程為封鎖。 因此，第一次啟用/下載失敗或 CodePackage 損毀時，應觸發 ServiceType 封鎖的排程。 **ServiceTypeDisableGraceInterval**設定會決定在該節點上，ServiceType 最後會標示為封鎖的寬限期間隔。 請注意，這在啟用/下載/CodePackage 重新開機的情況下，應該仍會在內部重試模式，並由裝載子系統進行追蹤。 正在重試，例如： CodePackage 會排程在當機之後重新開機，或 Service Fabric 會再次嘗試下載套件。
一旦封鎖之後，您應該會看到錯誤「「系統。裝載」回報的屬性 ' ServiceTypeRegistration： ServiceType ' 發生錯誤。 ServiceType 已在節點上停用。」

系統會在節點上重新啟用 ServiceType 
- 如果啟用作業成功，或在失敗時到達**ActivationMaxFailureCount**重試。
- 如果下載作業成功，或在失敗時到達**DeploymentMaxFailureCount**重試。
- 如果具有損毀的 CodePackage 會啟動備份並成功註冊 ServiceType。

在**ActivationMaxFailureCount**DeploymentMaxFailureCount 重試之後再次啟用 ServiceType 的原因 / **DeploymentMaxFailureCount**是，它們是在節點上啟用/下載應用程式 Service Fabric 將會執行的最大嘗試次數。 如果不成功，則不會重試目前的作業，而且因為 Service Fabric 想要為服務提供另一個啟用的機會，這可能會成功，而導致問題自動修復，它會系結至啟用/下載作業的生命週期。 藉由放置複本所觸發的新啟動/下載作業，可能會再次觸發 ServiceType 封鎖或可成功。

> [!NOTE]
> 如果未註冊 ServiceType 的 CodePackage 損毀，則不會影響 ServiceType。 只有裝載複本損毀的 CodePackage 會影響 ServiceType。
>

### <a name="codepackage-crash"></a>CodePackage 損毀
當 CodePackage 損毀時，Service Fabric 會使用反向來重新開機它，而輪詢則與程式碼套件是否已向我們註冊型別無關。

「輪詢」值一律為 Min （RetryTime， **ActivationMaxRetryInterval**），而且此值可以是以**ActivationRetryBackoffExponentiationBase** config 為基礎的常數、線性或指數。

- 常數： If **ActivationRetryBackoffExponentiationBase** = = 0 then RetryTime = **ActivationRetryBackoffInterval**;
- 線性： If **ActivationRetryBackoffExponentiationBase** = = 0 then RetryTime = ContinuousFailureCount * **ActivationRetryBackoffInterval** ，其中 ContinousFailureCount 是 CodePackage 損毀或無法啟動的次數。
- 指數： RetryTime = （以秒為單位的**ActivationRetryBackoffInterval** ） * （**ActivationRetryBackoffExponentiationBase** ^ ContinuousFailureCount）;
    
您可以視需要控制快速重新開機的行為。 讓我們來談談線性。 這表示如果 CodePackage 損毀，則啟動間隔會在10，20，30 40 秒之後，直到 CodePackage 停用為止。 
    
在失敗後 Service Fabric 關閉的最大時間量（等待）是由**ActivationMaxRetryInterval**所控制
    
如果您的 CodePackage 損毀並恢復運作，則必須隨時掌握**CodePackageContinuousExitFailureResetInterval** ，讓 Service Fabric 將其視為狀況良好，此時會將健康狀態報表覆寫為 OK，並重設 ContinousFailureCount。

### <a name="codepackage-not-registering-servicetype"></a>CodePackage 未註冊 ServiceType
如果 CodePackage 保持運作，而且預期會向我們註冊 ServiceType，但永遠不會這樣做，則在這種情況下 Service Fabric 會在**servicetyperegistrationtimeout 來設定**之後產生警告 HealthReport，指出 ServiceType 未在超時時間內設定。

### <a name="activation-failure"></a>啟用失敗
Service Fabric 在啟用期間發現錯誤時，一律會使用線性退避（與 CodePackage 損毀相同）。 這表示啟用作業會在（0 + 10 + 20 + 30 + 40） = 100 秒後放棄（第一次重試是立即的）。 不會重試此啟用。
    
啟用輪詢的最大值可以是**ActivationMaxRetryInterval** ，並重試**ActivationMaxFailureCount**。

### <a name="download-failure"></a>下載失敗
Service Fabric 在下載期間遇到錯誤時，一律會使用線性退避。 這表示啟用作業會在（0 + 10 + 20 + 30 + 40） = 100 秒後放棄（第一次重試是立即的）。 不會重試此下載。 適用于下載的線性輪詢會等於 ContinuousFailureCount ***DeploymentRetryBackoffInterval** ，而且可以將 max 改為**DeploymentMaxRetryInterval**。 如同啟用，下載作業可以重試**ActivationMaxFailureCount**。

> [!NOTE]
> 在您變更這些內容之前，以下是一些您應該牢記在心的範例。

* 如果 CodePackage 持續當機，且已關閉，將會停用 ServiceType。 但是，如果啟用設定是讓它快速重新開機，則 CodePackage 可能會出現幾次，才會看到 ServiceType 的停用。 針對 ex：假設您的 CodePackage 已啟動，請向 Service Fabric 註冊 ServiceType，然後當機。 在此情況下，一旦裝載收到類型註冊，就會取消**ServiceTypeDisableGraceInterval**期限。 這可能會重複，直到您的 CodePackage 會被視為大於**ServiceTypeDisableGraceInterval**的值，然後才會在節點上停用 ServiceType。 因此，在節點上停用您的 ServiceType 之前，可能會有一段時間。

* 在啟用的情況下，當 Service Fabric 系統需要在節點上放置複本時，RA （ReconfigurationAgent）會要求裝載子系統啟動應用程式，並每隔15秒（**RAPMessageRetryInterval**）重試啟用要求。 為了讓 Service Fabric 系統知道 ServiceType 已停用，裝載中的啟用作業必須比重試間隔和**ServiceTypeDisableGraceInterval**存留一段較長的時間。 例如：讓叢集將設定**ActivationMaxFailureCount**設為5，並將**ActivationRetryBackoffInterval**設定為1秒。這表示啟用作業將會在（0 + 1 + 2 + 3 + 4） = 10 秒（第一次重試是立即），而且在該主機重新嘗試之後放棄。 在此情況下，啟用作業將會完成，且不會在15秒後重試。 發生的原因是 Service Fabric 在15秒內耗盡所有重試。 因此，ReconfigurationAgent 的每次重試都會在裝載子系統中建立新的啟用作業，而此模式會持續重複，且不會在節點上停用 ServiceType。 因為 ServiceType 不會在節點 Sf 系統的 component FM （傳送 failovermanager）上停用，所以不會將複本移至不同的節點。
> 

## <a name="deactivation"></a>停用

在節點上啟用 ServicePackage 時，會追蹤其是否停用。 Deactivator 是追蹤它的實體。
Deactivator 的運作方式有兩種：

1.  定期：在每次**DeactivationScanInterval**時，它會檢查是否有 ServicePackages，其中從未裝載過複本，並將其標示為要停用的候選項目。
2.  ReplicaClose：如果複本已關閉，Deactivator 會取得 DecrementUsageCount。 如果計數為0，表示 ServicePackage 並未裝載任何複本，因此是停用的候選項。

 根據啟用模式[獨佔/共用][a2]，在**DeactivationGraceInterval** for SharedMode/ **ExclusiveModeDeactivationGraceInterval** for ExclusiveMode 之後，會排定停用的候選項目。 如果在這段時間內，新的複本位置會進入，則會取消停用。

### <a name="periodically"></a>定期
範例1：假設 Deactivator 會在 Time T （**DeactivationScanInterval**）進行掃描。 其下一次掃描將位於2T。 假設在 T + 1 發生 ServicePackage 啟用。 此 ServicePackage 並未裝載複本，因此需要停用。 若要讓 ServicePackage 成為停用的候選項，它必須處於無複本的狀態，至少為 T 時間。 這表示，在 2T + 1 就有資格停用。 因此，2T 掃描時，不會將此 ServicePackage 尋找為停用的候選。 下一個 [停用週期] 3T 會將此 ServicePackage 排程為停用，因為它現在已不是時間 T 的複本狀態。  

範例2：假設 ServicePackage 會在時間 T-1 啟動，而 Deactivator 會在 T 進行掃描。ServicePackage 不會裝載複本。 然後在下次掃描2T，此 ServicePackage 將會被視為停用的候選項，因此會排程停用。  

範例3：假設 ServicePackage 會在 T-1 啟動，而 Deactivator 則會在 T 進行掃描。ServicePackage 尚未裝載複本。 現在在 T + 1，會放置複本，亦即 裝載會取得 IncrementUsageCount，這表示會建立複本。 現在在2T，此 ServicePackage 將不會排程停用。 現在，停用將會移至下面所述的 ReplicaClose 邏輯。

範例4：假設您的 ServicePackage 很大（例如 10 GB），那麼在節點上下載可能需要一些時間。 一旦應用程式啟動之後，Deactivator 就會追蹤其生命週期。 現在，如果您的**DeactivationScanInterval**設定很小，您可能會遇到 ServicePackage 無法在節點上啟動的問題，因為所有時間都已下載。 若要解決這個問題，您可以在[節點上預先下載 ServicePackage][p1]。 

> [!NOTE]
> 因此，ServicePackage 可以在（**DeactivationScanInterval**到 2 ***DeactivationScanInterval**） + **DeactivationGraceInterval**ExclusiveModeDeactivationGraceInterval 之間的任何位置停用 / ** **。 
>

### <a name="replica-close"></a>複本關閉：
Deactivator 會保留 ServicePackage 保存的複本計數。 如果 ServicePackage 正在保存複本，而複本已關閉/關閉，則裝載會取得 DecrementUsageCount。 當開啟複本時，裝載會取得 IncrementUsageCount。 遞減表示 ServicePackage 現在裝載一個較少的複本，而當計數降到0時，則 ServicePackage 會排定為停用，而它將停用的時間則為**DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**。 

例如：假設在 T 發生遞減，而 ServicePackage 排定在 2T + X （**DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**）停用。 如果在這段期間內，裝載會取得 IncrementUsage，表示複本已建立，則會取消停用。

> [!NOTE]
>因此，這些設定基本上代表： **DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**：在裝載任何複本之後，讓 ServicePackage 再次裝載另一個複本的時間。 
**DeactivationScanInterval**：指定給 ServicePackage 裝載複本的最短時間（如果它從未裝載任何複本，亦即 如果未使用，則為。
>

### <a name="ctrlc"></a>Ctrl+C
一旦 ServicePackage 通過**DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval** ，而且仍然未裝載複本，停用就不會可取消。 CodePackage 會發出 Ctrl + C 處理常式，這表示現在停用管線必須執行，才能使進程關閉。 在這段期間，如果相同 ServicePackage 的新複本嘗試放置，將會失敗，因為我們無法從停用轉換為啟用。

## <a name="cluster-configs"></a>叢集的

使用預設值來影響啟用/decativation。

### <a name="servicetype"></a>ServiceType
**ServiceTypeDisableFailureThreshold**：預設值1。 失敗計數的閾值，在這之後會通知 FM （傳送 failovermanager），以停用該節點上的服務類型，並嘗試不同的節點來放置。
**ServiceTypeDisableGraceInterval**：預設值為30秒。可以停用服務類型的時間間隔。
**Servicetyperegistrationtimeout 來設定**：預設值300秒。要向 Service Fabric 註冊的 ServiceType 的超時時間。

### <a name="activation"></a>啟用
**ActivationRetryBackoffInterval**：預設值為10秒。每次啟用失敗時的輪詢間隔。
**ActivationMaxFailureCount**：預設值為20。 在放棄之前，系統將重試失敗啟用的最大計數。 
**ActivationRetryBackoffExponentiationBase**：預設值1.5。
**ActivationMaxRetryInterval**：預設值3600秒。在失敗時啟用的最大輪詢。
**CodePackageContinuousExitFailureResetInterval**：預設值300秒。為 CodePackage 重設連續結束失敗計數的時間。

### <a name="download"></a>下載
**DeploymentRetryBackoffInterval**：預設值為10。 部署失敗的輪詢間隔。
**DeploymentMaxRetryInterval**：預設值3600秒。失敗時部署的最大輪詢。
**DeploymentMaxFailureCount**：預設值為20。 應用程式部署會先重試 DeploymentMaxFailureCount 次數，才讓節點上該應用程式的部署失敗。

### <a name="deactivation"></a>停用
**DeactivationScanInterval**：預設值600秒。指定給 ServicePackage 以裝載複本的最小時間（如果它從未裝載任何複本，亦即 如果未使用，則為。
**DeactivationGraceInterval**：預設值60秒。當 ServicePackage 在**共用**進程模型的情況下裝載任何複本之後，再次提供給另一個複本的時間。
**ExclusiveModeDeactivationGraceInterval**：預設值為1秒。當 ServicePackage 在**獨佔**進程模型中裝載任何複本之後，再次指定給另一個複本的時間。

## <a name="next-steps"></a>後續步驟
[對應用程式進行封裝][a3]並使它準備好進行部署。

[部署與移除應用程式][a4]。 此文章說明如何使用 PowerShell 來管理應用程式執行個體。

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-hosting-model.md
[a3]: service-fabric-package-apps.md
[a4]: service-fabric-deploy-remove-applications.md

[p1]: https://docs.microsoft.com/powershell/module/servicefabric/copy-servicefabricservicepackagetonode

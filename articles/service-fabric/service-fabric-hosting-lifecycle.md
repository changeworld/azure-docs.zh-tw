---
title: Azure Service Fabric 裝載啟用和停用生命週期
description: 說明節點上的應用程式和 ServicePackage 生命週期
author: tugup
ms.topic: conceptual
ms.date: 05/1/2020
ms.author: tugup
ms.openlocfilehash: a39aecf16d1c3303c0a590b389ba2aa69d4472f2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87405121"
---
# <a name="azure-service-fabric-hosting-lifecycle"></a>Azure Service Fabric 裝載生命週期
本文概述在節點上啟動應用程式時所發生的事件，以及用來控制行為的各種叢集的相關資訊。

在進一步進行之前，請確定您已了解[在 Service Fabric 中模型化應用程式][a1]中所說明的各種概念與關聯性。 

> [!NOTE]
> 在本文中，除非明確指出是不同意義，否則：
>
> - 「複本」（ *replica* ）是指具狀態服務的複本和無狀態服務的實例。
> - *CodePackage*被視為相當於會註冊*servicetype*的*ServiceHost*進程，並裝載該*servicetype*的服務複本。
>

## <a name="activation-of-applicationservicepackage"></a>應用程式/ServicePackage 啟用

啟用的管線如下所示：

1. 下載 ApplicationPackage。 例如： ApplicationManifest.xml 等等。
2. 設定適用于應用程式的環境，例如：建立使用者等等。
3. 開始追蹤應用程式以進行停用。
4. 下載 ServicePackage。 例如： ServiceManifest.xml、code、config、data 套件等。
5. 設定服務封裝的環境，例如：設定防火牆、配置端點的埠等。
6. 開始追蹤 ServicePackage 以進行停用。
7. 開始 SetupEntryPoint Codepackage 並等候完成。
8. 開始 MainEntryPoint Codepackage。

### <a name="servicetype-blocklisting"></a>ServiceType 封鎖
**ServiceTypeDisableFailureThreshold** 會判斷 (啟用、下載、CodePackage) 失敗的失敗次數，在這之後，會為 ServiceType 排程封鎖。 因此，第一次啟用/下載失敗或 CodePackage 損毀時，應該會觸發 ServiceType 封鎖的排程。 **ServiceTypeDisableGraceInterval**設定會決定在該間隔之後，ServiceType 最後在該節點上標示為封鎖的寬限期。 請注意，若要這麼做，啟用/下載/CodePackage 重新開機應該仍處於內部重試模式，並藉由裝載子系統進行追蹤。 重試，例如： CodePackage 會排定在損毀後重新開機，或 Service Fabric 將會再次嘗試下載套件。
封鎖之後，您應該會看到錯誤「系統. 裝載」屬性 ' >servicetyperegistration： ServiceType ' 回報錯誤。 節點上的 ServiceType 已停用。」

節點上的 ServiceType 將會重新啟用 
- 如果啟用作業成功或達到 **ActivationMaxFailureCount** 失敗時的重試次數。
- 如果下載作業成功或達到 **DeploymentMaxFailureCount** 失敗時的重試次數。
- 如果有損毀的 CodePackage 開始備份，並成功註冊 ServiceType。

在**ActivationMaxFailureCount**DeploymentMaxFailureCount 重試之後再次啟用 ServiceType 的原因 / **DeploymentMaxFailureCount**是，Service Fabric 將會執行的最大嘗試次數，以在節點上啟動/下載應用程式。 如果不成功，則不會重試目前的作業，因為 Service Fabric 想要為服務提供另一項啟用的機會（這可能會成功，而導致問題自動修復），它會系結至啟用/下載作業的生命週期。 藉由放置複本所觸發的新啟用/下載作業，可以再次觸發 ServiceType 封鎖或成功。

> [!NOTE]
> 如果未註冊 ServiceType 的 CodePackage 損毀，則不會影響 ServiceType。 只有裝載複本損毀的 CodePackage 會影響 ServiceType。
>

### <a name="codepackage-crash"></a>CodePackage 損毀
當 CodePackage 損毀時，Service Fabric 會使用反向重新開機它，而反向處理則與程式碼套件是否已向我們註冊類型無關。

反向值一律為 Min (RetryTime， **ActivationMaxRetryInterval**) ，而且此值可以是常數、線性或指數（以 **ActivationRetryBackoffExponentiationBase** 設定為基礎）。

- 常數： If **ActivationRetryBackoffExponentiationBase** = = 0 then RetryTime = **ActivationRetryBackoffInterval**;
- 線性： If  **ActivationRetryBackoffExponentiationBase** = = 0 then RetryTime = ContinuousFailureCount * **ActivationRetryBackoffInterval** ，其中 ContinousFailureCount 是 CodePackage 損毀或無法啟動的次數。
- 指數： RetryTime = (**ActivationRetryBackoffInterval** （秒）) * (**ActivationRetryBackoffExponentiationBase** ^ ContinuousFailureCount) ;
    
您可以依照想要的方式來控制行為，例如快速重新開機。 讓我們來談談線性。 這表示，如果 CodePackage 當機，則啟動間隔將會是10、20、30 40 秒，直到 CodePackage 停用為止。 
    
在**ActivationMaxRetryInterval**管理失敗之後，Service Fabric 關閉 (等候) 的最大時間量
    
如果您的 CodePackage 損毀並重新啟動，則需要保持 **CodePackageContinuousExitFailureResetInterval** ，讓 Service Fabric 將它視為狀況良好，此時它會將健康情況報告覆寫為正常，並重設 ContinousFailureCount。

### <a name="codepackage-not-registering-servicetype"></a>CodePackage 未註冊 ServiceType
如果 CodePackage 保持運作，而且預期會向我們註冊 ServiceType，但永遠不會這樣做，則在這種情況下，Service Fabric 將會在 **>servicetyperegistrationtimeout** 指出 ServiceType 未在超時時間內進行設定時，產生警告 >healthreport。

### <a name="activation-failure"></a>啟用失敗
Service Fabric 在啟用期間發現錯誤時，一律會使用與 CodePackage 損毀) 相同的線性退 (。 這表示啟用作業將在 (0 + 10 + 20 + 30 + 40) = 100 秒後放棄 (第一次重試是立即) 。 此啟用不會重試。
    
可以 **ActivationMaxRetryInterval** 和重試 **ActivationMaxFailureCount**的最大啟用輪詢。

### <a name="download-failure"></a>下載失敗
當 Service Fabric 在下載期間發生錯誤時，一律會使用線性反向。 這表示啟用作業將在 (0 + 10 + 20 + 30 + 40) = 100 秒後放棄 (第一次重試是立即) 。 不重試此下載。 適用于下載的線性輪詢會等於 ContinuousFailureCount ***DeploymentRetryBackoffInterval** ，並可將最大值回復到 **DeploymentMaxRetryInterval**。 就像啟用一樣，下載作業可以重試 **ActivationMaxFailureCount**。

> [!NOTE]
> 在變更這些設計之前，以下是一些您應該記住的範例。

* 如果 CodePackage 持續損毀並退出，則會停用 ServiceType。 但是，如果啟用設定是讓它快速重新開機，則 CodePackage 可能會出現幾次，然後才會看到 ServiceType 的停用。 例如：假設您的 CodePackage 出現、向 Service Fabric 註冊 ServiceType，然後損毀。 在此情況下，一旦裝載收到型別註冊， **ServiceTypeDisableGraceInterval** 期限就會取消。 這可能會重複，直到您的 CodePackage 可以使用大於  **ServiceTypeDisableGraceInterval** 的值，然後才會在節點上停用 ServiceType。 因此，在節點上停用 ServiceType 之前，可能會是一段時間。

* 在啟用的情況下，當 Service Fabric 系統必須在節點上放置複本時，RA (ReconfigurationAgent) 會要求裝載子系統啟動應用程式，並每15秒 (**RAPMessageRetryInterval**) 重試啟用要求。 針對 Service Fabric 系統知道是否已停用 ServiceType，裝載中的啟用作業必須存留較長的時間，而不是重試間隔和 **ServiceTypeDisableGraceInterval**。 例如：讓叢集將設定 **ActivationMaxFailureCount** 設為5，並將 **ActivationRetryBackoffInterval** 設定為1秒。這表示啟動作業將會在 (0 + 1 + 2 + 3 + 4) = 10 秒後放棄 (第一次重試是立即) ，且該主機放棄重試。 在此情況下，啟用作業將會完成，而且不會在15秒後重試。 發生的原因是 Service Fabric 在15秒內耗盡所有重試。 因此，ReconfigurationAgent 的每次重試會在主控子系統中建立新的啟用作業，而且此模式將永遠保持重複，且不會在節點上停用 ServiceType。 由於 ServiceType 不會在節點上停用，因此 Sf 系統的元件 FM (FailoverManager) 不會將複本移至不同的節點。
> 

## <a name="deactivation"></a>停用

在節點上啟用 ServicePackage 時，會追蹤該節點是否停用。 Deactivator 是追蹤它的實體。
Deactivator 的運作方式有兩種：

1.  定期：在每個 **DeactivationScanInterval**上，它會檢查是否有從未裝載複本的 ServicePackages，並將它們標示為停用的候選項目。
2.  ReplicaClose：如果複本已關閉，Deactivator 會取得 DecrementUsageCount。 如果計數設為0，表示 ServicePackage 並未裝載任何複本，因此是停用的候選項。

 根據啟用模式 [獨佔/共用][a2]，在 **DeactivationGraceInterval** for SharedMode/ **ExclusiveModeDeactivationGraceInterval** for ExclusiveMode 之後，會排定停用的候選項目。 如果在這段時間內，會有新的複本放置，則會取消停用。

### <a name="periodically"></a>定期：
範例1：假設 Deactivator 會掃描時間 T (**DeactivationScanInterval**) 。 其下一次掃描將會 T.inv.2t。 假設 ServicePackage 啟用發生于 T + 1。 此 ServicePackage 並未裝載複本，因此必須停用。 若要讓 ServicePackage 成為停用的候選項，它必須處於沒有複本的狀態，至少為 T 時間。 這表示它將符合在 T.inv.2t + 1 停用的資格。 因此，T.inv.2t 的掃描不會將此 ServicePackage 視為停用的候選。 下一個停用迴圈3T 會將此 ServicePackage 排程為停用，因為現在它已不是時間 T 的複本狀態。  

範例2：假設 ServicePackage 是在時間 T 1 啟動，而 Deactivator 則是在 T 進行掃描。ServicePackage 不會裝載複本。 然後，在下次掃描 T.inv.2t 此 ServicePackage 將會被視為停用的候選，因此將會排程停用。  

範例3：假設 ServicePackage 是在 T –1啟動，而 Deactivator 則是在 T 進行掃描。ServicePackage 尚未裝載複本。 現在在 T + 1 會放置複本，亦即 裝載會取得 IncrementUsageCount，這表示會建立複本。 現在 T.inv.2t 此 ServicePackage 不會排定停用。 現在，停用將會移至下面所述的 ReplicaClose 邏輯。

範例4：假設您的 ServicePackage 很大（例如 10 GB），則可能需要一些時間才能在節點上下載。 一旦啟動應用程式，Deactivator 就會追蹤其生命週期。 現在，如果您的 **DeactivationScanInterval** 設定較小，則可能會遇到 ServicePackage 無法在節點上啟動的問題，因為所有時間都在下載中。 若要解決此問題，您可以 [在節點上預先下載 ServicePackage][p1]。 

> [!NOTE]
> 如此一來，ServicePackage 就可以在 (**DeactivationScanInterval**到 2 ***DeactivationScanInterval**) + **DeactivationGraceInterval**ExclusiveModeDeactivationGraceInterval 之間的任何位置被停用 / ** **。 
>

### <a name="replica-close"></a>複本關閉：
Deactivator 會保留 ServicePackage 保存的複本計數。 如果 ServicePackage 持有複本，而複本已關閉/關閉，則裝載會取得 DecrementUsageCount。 當複本開啟時，裝載會取得 IncrementUsageCount。 遞減表示 ServicePackage 現在裝載一個較少的複本，而當計數降到0時，則會將 ServicePackage 排定為停用，而下次停用的時間則為**DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**。 

例如：假設在 T 進行遞減，而 ServicePackage 排定在 t.inv.2t + X (**DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**) 停用。 如果在這段期間，裝載會取得 IncrementUsage，表示複本已建立，則停用已取消。

> [!NOTE]
>基本上，這些設定的意思是： **DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**：指定給 ServicePackage 的時間在裝載任何複本之後，再次裝載另一個複本。 
**DeactivationScanInterval**：提供給 ServicePackage 的最短時間，以裝載複本（如果它從未裝載任何複本，亦即 如果未使用，則為。
>

### <a name="ctrlc"></a>Ctrl+C
一旦 ServicePackage 通過**DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**且仍未裝載複本，則停用為非可取消。 CodePackage 會發出 Ctrl + C 處理常式，表示現在停用的管線必須經過才能讓進程停止運作。 在這段期間內，如果相同 ServicePackage 的新複本正在嘗試放置，將會失敗，因為我們無法從停用轉換成啟用。

## <a name="cluster-configs"></a>叢集的進行

預設值會影響啟用/decativation。

### <a name="servicetype"></a>ServiceType
**ServiceTypeDisableFailureThreshold**：預設值1。 失敗計數的臨界值，在這之後會通知 FM (FailoverManager) 停用該節點上的服務類型，並嘗試使用不同的節點進行放置。
**ServiceTypeDisableGraceInterval**：預設為30秒。可以停用服務類型的時間間隔。
**>servicetyperegistrationtimeout**：預設值300秒。ServiceType 註冊 Service Fabric 的超時時間。

### <a name="activation"></a>啟用
**ActivationRetryBackoffInterval**：預設值為10秒。每個啟用失敗的輪詢間隔。
**ActivationMaxFailureCount**：預設值20。 在放棄之前，系統會重試失敗啟用的最大計數。 
**ActivationRetryBackoffExponentiationBase**：預設值1.5。
**ActivationMaxRetryInterval**：預設值3600秒。失敗時啟用的最大回復。
**CodePackageContinuousExitFailureResetInterval**：預設值300秒。重設 CodePackage 連續結束失敗計數的超時時間。

### <a name="download"></a>下載
**DeploymentRetryBackoffInterval**：預設值為10。 部署失敗的輪詢間隔。
**DeploymentMaxRetryInterval**：預設值3600秒。失敗時部署的最大回復。
**DeploymentMaxFailureCount**：預設值20。 應用程式部署會先重試 DeploymentMaxFailureCount 次數，才讓節點上該應用程式的部署失敗。

### <a name="deactivation"></a>停用
**DeactivationScanInterval**：預設值600秒。提供給 ServicePackage 的最短時間，如果從未裝載任何複本，即 如果未使用，則為。
**DeactivationGraceInterval**：預設值60秒。指定給 ServicePackage 的時間，在 **共用** 進程模型的情況下裝載任何複本之後，再次裝載另一個複本。
**ExclusiveModeDeactivationGraceInterval**：預設值為1秒。指定給 ServicePackage 的時間，在其裝載任何複本以供 **獨佔** 進程模型使用之後，再次裝載另一個複本。

## <a name="next-steps"></a>接下來的步驟
[對應用程式進行封裝][a3]並使它準備好進行部署。

[部署與移除應用程式][a4]。 此文章說明如何使用 PowerShell 來管理應用程式執行個體。

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-hosting-model.md
[a3]: service-fabric-package-apps.md
[a4]: service-fabric-deploy-remove-applications.md

[p1]: /powershell/module/servicefabric/copy-servicefabricservicepackagetonode

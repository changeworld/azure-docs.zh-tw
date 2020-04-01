---
title: 進階應用程式升級主題
description: 本文章涵蓋升級 Service Fabric 應用程式相關的一些進階主題。
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 182ab6dc1663e160561b8941ebf3a36b5af3d950
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422815"
---
# <a name="service-fabric-application-upgrade-advanced-topics"></a>服務結構應用程式升級:進階主題

## <a name="add-or-remove-service-types-during-an-application-upgrade"></a>在應用程式升級期間新增或移除服務類型

如果新服務類型新增至已發行應用程式作為一項升級，新服務類型就會新增至部署的應用程式中。 這類升級不會影響已經是應用程式一部分的任何服務執行個體，但是已新增的服務類型執行個體必須針對作用中的新服務類型建立 (請參閱 [New-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps))。

同樣地，服務類型也可以從應用程式移除，作為升級的一部分。 不過，即將移除服務類型的所有服務執行個體都必須移除，才能繼續執行升級 (請參閱 [Remove-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricservice?view=azureservicefabricps))。

## <a name="avoid-connection-drops-during-stateless-service-planned-downtime-preview"></a>避免在無狀態服務計劃停機期間連接中斷(預覽)

對於計劃無狀態的實例停機時間(如應用程式/群集升級或節點停用),由於實例關閉后關閉公開的終結點,連接可能會被刪除,從而導致強制連接關閉。

為了避免這種情況,請在服務配置中添加*實例關閉延遲持續時間*來配置*RequestDrain(* 預覽)功能,以便在接收來自群集內其他服務的請求時進行排空,並且使用反向代理或使用具有通知模型的解析 API 來更新終結點。 這可確保在關閉實例*之前*,在延遲開始之前刪除無狀態實例通告的終結點。 此延遲使現有請求在實例實際關閉之前正常耗盡。 用戶端在啟動延遲時通過回調函數通知終結點更改,以便他們可以重新解析終結點,並避免向即將關閉的實例發送新請求。

### <a name="service-configuration"></a>服務設定

有幾種方法可以配置服務端的延遲。

 * **建立新服務時**,指定`-InstanceCloseDelayDuration`:

    ```powershell
    New-ServiceFabricService -Stateless [-ServiceName] <Uri> -InstanceCloseDelayDuration <TimeSpan>`
    ```

 * **在應用程式清單中的預設部份定義服務時**,分配`InstanceCloseDelayDurationSeconds`屬性 :

    ```xml
          <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]" InstanceCloseDelayDurationSeconds="15">
              <SingletonPartition />
          </StatelessService>
    ```

 * **更新現有服務時**,指定`-InstanceCloseDelayDuration`:

    ```powershell
    Update-ServiceFabricService [-Stateless] [-ServiceName] <Uri> [-InstanceCloseDelayDuration <TimeSpan>]`
    ```

### <a name="client-configuration"></a>用戶端組態

要在終結點更改時接收通知,客戶端應註冊回調,請參閱[服務通知篩選器描述](https://docs.microsoft.com/dotnet/api/system.fabric.description.servicenotificationfilterdescription)。
更改通知表示終結點已更改,用戶端應重新解析終結點,並且不要使用不再通告的終結點,因為它們將很快關閉。

### <a name="optional-upgrade-overrides"></a>選擇升級

除了設定每個服務的預設延遲持續時間外,您還可以使用相同的`InstanceCloseDelayDurationSec`( ) 選項覆寫應用程式/群集升級期間的延遲:

```powershell
Start-ServiceFabricApplicationUpgrade [-ApplicationName] <Uri> [-ApplicationTypeVersion] <String> [-InstanceCloseDelayDurationSec <UInt32>]

Start-ServiceFabricClusterUpgrade [-CodePackageVersion] <String> [-ClusterManifestVersion] <String> [-InstanceCloseDelayDurationSec <UInt32>]
```

延遲持續時間僅適用於調用的升級實例,並且不會更改各個服務延遲配置。 例如,可以使用它指定延遲`0`,以便跳過任何預配置的升級延遲。

> [!NOTE]
> 對於來自 Azure 負載均衡器的請求,不遵循排空請求的設置。 如果呼叫服務使用基於投訴的解析,則不遵循該設置。
>
>

> [!NOTE]
> 當群集代碼版本7.1.XXX或以上時,可以使用上述更新 ServiceFabricService cmdlet 在現有服務中配置此功能。
>
>

## <a name="manual-upgrade-mode"></a>手動升級模式

> [!NOTE]
> 針對所有 Service Fabric 升級建議使用「Monitored」** 升級模式。
> 只有對於失敗或已暫止的升級，才應該考量「UnmonitoredManual」** 升級模式。 
>
>

在「Monitored」** 模式中，Service Fabric 會套用健康情況原則，以確保應用程式在升級程序期間狀況良好。 如果違反健康情況原則，則升級會根據指定的 FailureAction** 暫止或自動復原。

在「UnmonitoredManual」** 模式中，應用程式系統管理員具有升級程序的完整控制權。 當套用自訂健康情況評估原則，或執行非傳統升級以完全略過健康情況監視 (例如應用程式已經有資料遺失) 時，此模式非常有用。 在此模式中執行的升級會在完成每個 UD 之後自行暫止，必須明確地使用 [Resume-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps) 才能繼續。 當升級已暫止並準備好讓使用者繼續時，其升級狀態將會顯示 RollforwardPending** (請參閱 [UpgradeState](https://docs.microsoft.com/dotnet/api/system.fabric.applicationupgradestate?view=azure-dotnet))。

最後，「UnmonitoredAuto」** 模式適用於在服務開發或測試期間執行快速升級反覆項目，因為不需要使用者輸入，而且不會評估應用程式健康情況原則。

## <a name="upgrade-with-a-diff-package"></a>使用差異封裝進行升級

並非佈建完整應用程式套件，升級也可以藉由佈建差異套件來執行，該套件只包含更新的程式碼/設定/資料套件，以及完整應用程式資訊清單和完整服務資訊清單。 只有在第一次將應用程式安裝至叢集時需要完整的應用程式套件。 後續升級可以從完整應用程式套件或差異套件進行。  

在應用程式套件中找不到的差異套件之應用程式資訊清單或服務資訊清單中的任何參考，會自動取代為目前佈建的版本。

使用差異套件的案例包括：

* 當您有大型應用程式套件參考數個服務資訊清單檔案及/或數個程式碼套件、組態套件或資料套件時。
* 當您有部署系統會直接從您的應用程式建置程序產生組建版面配置時。 在此情況下，即使程式碼沒有任何變更，新建立的組件也會有不同的總和檢查碼。 使用完整的應用程式封裝需要您在所有的程式碼封裝上更新版本。 使用差異封裝，您只需提供已變更的檔案和已變更版本的資訊清單檔案。

使用 Visual Studio 升級應用程式時，會自動發佈差異套件。 若要手動建立差異套件，就必須更新應用程式資訊清單和服務資訊清單，但只有變更過的套件才要放入最終的應用程式套件。

比方說，讓我們從下列應用程式開始 (為方便了解而提供版本號碼)︰

```text
app1           1.0.0
  service1     1.0.0
    code       1.0.0
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

假設您想要使用差異套件，只更新 service1 的程式碼套件。 更新的應用程式會有下列版本變更︰

```text
app1           2.0.0      <-- new version
  service1     2.0.0      <-- new version
    code       2.0.0      <-- new version
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

在此情況下，您會將應用程式資訊清單更新為 2.0.0，並更新 service1 的服務資訊清單以反映程式碼套件更新。 應用程式封裝的資料夾會有下列結構︰

```text
app1/
  service1/
    code/
```

換句話說，正常建立完整應用程式套件，然後移除版本未變更的任何程式碼/設定/資料套件資料夾。

## <a name="upgrade-application-parameters-independently-of-version"></a>獨立於版本升級應用程式參數

有時,最好在不更改清單版本的情況下更改 Service Fabric 應用程式的參數。 通過使用 **-應用程式參數**標誌和**啟動服務 Fabric 應用程式升級**Azure 服務結構 PowerShell cmdlet,可以很方便地做到這一點。 假定具有以下屬性的服務結構應用程式:

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "1"; "NewParameter" = "testBefore" }
```

現在,使用**啟動-服務Fabric應用程式升級**cmdlet 升級應用程式。 此示例顯示受監視的升級,但也可以使用不受監視的升級。 要檢視此 cmdlet 接受的標誌的完整說明,請參閱 Azure[服務結構 PowerShell 模組參考](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps#parameters)

```PowerShell
PS C:\> $appParams = @{ "ImportantParameter" = "2"; "NewParameter" = "testAfter"}

PS C:\> Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/Application1 -ApplicationTypeVers
ion 1.0.0 -ApplicationParameter $appParams -Monitored

```

升級後,確認應用程式具有更新的參數和相同的版本:

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "2"; "NewParameter" = "testAfter" }
```

## <a name="roll-back-application-upgrades"></a>捲軸應用程式

雖然升級可以向前復原為三個模式其中之一 (Monitored**、UnmonitoredAuto** 或 UnmonitoredManual**)，但是只能復原為 UnmonitoredAuto** 或 UnmonitoredManual** 模式。 在「UnmonitoredAuto」** 模式中復原的運作方式與向前復原相同，例外的是 UpgradeReplicaSetCheckTimeout** 的預設值不同 - 請參閱[應用程式升級參數](service-fabric-application-upgrade-parameters.md)。 在「UnmonitoredManual」** 模式中復原的運作方式與向前復原相同 - 復原會在完成每個 UD 之後自行暫止，必須明確地使用 [Resume-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps) 以繼續復原。

當「Monitored」** 模式中升級的健康情況原則，違反 FailureAction**「復原」** 時 (請參閱[應用程式升級參數](service-fabric-application-upgrade-parameters.md)) 或明確地使用 [Start-ServiceFabricApplicationRollback](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationrollback?view=azureservicefabricps) 時，復原會自動觸發。

在復原期間，UpgradeReplicaSetCheckTimeout** 的值和模式仍然可以使用 [Update-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricapplicationupgrade?view=azureservicefabricps) 隨時變更。

## <a name="next-steps"></a>後續步驟
[使用 Visual Studio 升級應用程式](service-fabric-application-upgrade-tutorial.md)會引導您使用 Visual Studio 進行應用程式升級。

[使用 Powershell 升級應用程式](service-fabric-application-upgrade-tutorial-powershell.md)會引導您使用 PowerShell 進行應用程式升級。

使用 [升級參數](service-fabric-application-upgrade-parameters.md)來控制您應用程式的升級方式。

通過學習如何使用[資料序列化](service-fabric-application-upgrade-data-serialization.md),使應用程式升級相容。

通過參考[疑難解答應用程式升級](service-fabric-application-upgrade-troubleshooting.md)中的步驟,修復應用程序升級中的常見問題。

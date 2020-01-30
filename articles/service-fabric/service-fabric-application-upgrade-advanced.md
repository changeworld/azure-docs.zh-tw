---
title: 先進的應用程式升級主題
description: 本文章涵蓋升級 Service Fabric 應用程式相關的一些進階主題。
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 09f3fdf1f26a13c6722eb039e132256f33be38ff
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845440"
---
# <a name="service-fabric-application-upgrade-advanced-topics"></a>Service Fabric 應用程式升級： Advanced 主題

## <a name="add-or-remove-service-types-during-an-application-upgrade"></a>在應用程式升級期間新增或移除服務類型

如果新服務類型新增至已發行應用程式作為一項升級，新服務類型就會新增至部署的應用程式中。 這類升級不會影響已經是應用程式一部分的任何服務執行個體，但是已新增的服務類型執行個體必須針對作用中的新服務類型建立 (請參閱 [New-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps))。

同樣地，服務類型也可以從應用程式移除，作為升級的一部分。 不過，即將移除服務類型的所有服務執行個體都必須移除，才能繼續執行升級 (請參閱 [Remove-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricservice?view=azureservicefabricps))。

## <a name="avoid-connection-drops-during-stateless-service-planned-downtime-preview"></a>避免在無狀態服務方案停機期間中斷連接（預覽）

針對規劃的無狀態實例停機（例如應用程式/叢集升級或節點停用），連接可能會因為公開的端點在關閉後遭到移除。

若要避免這種情況，請在服務設定中新增複本*實例關閉延遲持續時間*，以設定*RequestDrain* （預覽）功能。 這可確保在延遲計時器開始關閉實例*之前*，會移除無狀態實例所通告的端點。 此延遲可讓現有的要求在實例實際停機之前正常地清空。 用戶端會收到回呼函式的端點變更通知，因此可以重新解析端點，並避免將新的要求傳送至實例停止運作。

### <a name="service-configuration"></a>服務設定

有數種方式可以設定服務端的延遲。

 * **建立新服務時**，請指定 `-InstanceCloseDelayDuration`：

    ```powershell
    New-ServiceFabricService -Stateless [-ServiceName] <Uri> -InstanceCloseDelayDuration <TimeSpan>`
    ```

 * 在**應用程式資訊清單的 [預設值] 區段中定義服務時**，請指派 `InstanceCloseDelayDurationSeconds` 屬性：

    ```xml
          <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]" InstanceCloseDelayDurationSeconds="15">
              <SingletonPartition />
          </StatelessService>
    ```

 * **更新現有的服務時**，請指定 `-InstanceCloseDelayDuration`：

    ```powershell
    Update-ServiceFabricService [-Stateless] [-ServiceName] <Uri> [-InstanceCloseDelayDuration <TimeSpan>]`
    ```

### <a name="client-configuration"></a>用戶端組態

若要在端點變更時收到通知，用戶端可以註冊回呼（`ServiceManager_ServiceNotificationFilterMatched`），如下所示： 

```csharp
    var filterDescription = new ServiceNotificationFilterDescription
    {
        Name = new Uri(serviceName),
        MatchNamePrefix = true
    };
    fbClient.ServiceManager.ServiceNotificationFilterMatched += ServiceManager_ServiceNotificationFilterMatched;
    await fbClient.ServiceManager.RegisterServiceNotificationFilterAsync(filterDescription);

private static void ServiceManager_ServiceNotificationFilterMatched(object sender, EventArgs e)
{
      // Resolve service to get a new endpoint list
}
```

變更通知會指出端點已變更，用戶端應該重新解析端點，而不使用不再公告的端點，因為它們很快就會停止。

### <a name="optional-upgrade-overrides"></a>選用的升級覆寫

除了設定每個服務的預設延遲持續時間，您也可以使用相同的（`InstanceCloseDelayDurationSec`）選項，覆寫應用程式/叢集升級期間的延遲：

```powershell
Start-ServiceFabricApplicationUpgrade [-ApplicationName] <Uri> [-ApplicationTypeVersion] <String> [-InstanceCloseDelayDurationSec <UInt32>]

Start-ServiceFabricClusterUpgrade [-CodePackageVersion] <String> [-ClusterManifestVersion] <String> [-InstanceCloseDelayDurationSec <UInt32>]
```

延遲持續時間只適用于叫用的升級實例，否則不會變更個別的服務延遲設定。 例如，您可以使用這個來指定 `0` 的延遲，以便略過任何預先設定的升級延遲。

## <a name="manual-upgrade-mode"></a>手動升級模式

> [!NOTE]
> 針對所有 Service Fabric 升級建議使用「Monitored」升級模式。
> 只有對於失敗或已暫止的升級，才應該考量「UnmonitoredManual」升級模式。 
>
>

在「Monitored」模式中，Service Fabric 會套用健康情況原則，以確保應用程式在升級程序期間狀況良好。 如果違反健康情況原則，則升級會根據指定的 FailureAction 暫止或自動復原。

在「UnmonitoredManual」模式中，應用程式系統管理員具有升級程序的完整控制權。 當套用自訂健康情況評估原則，或執行非傳統升級以完全略過健康情況監視 (例如應用程式已經有資料遺失) 時，此模式非常有用。 在此模式中執行的升級會在完成每個 UD 之後自行暫止，必須明確地使用 [Resume-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps) 才能繼續。 當升級已暫止並準備好讓使用者繼續時，其升級狀態將會顯示 RollforwardPending (請參閱 [UpgradeState](https://docs.microsoft.com/dotnet/api/system.fabric.applicationupgradestate?view=azure-dotnet))。

最後，「UnmonitoredAuto」模式適用於在服務開發或測試期間執行快速升級反覆項目，因為不需要使用者輸入，而且不會評估應用程式健康情況原則。

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

## <a name="upgrade-application-parameters-independently-of-version"></a>獨立升級應用程式參數版本

有時候，您可以變更 Service Fabric 應用程式的參數，而不需要變更資訊清單版本。 您可以使用 **-ApplicationParameter**旗標搭配**Get-servicefabricapplicationupgrade** Azure Service Fabric PowerShell Cmdlet，輕鬆完成這項作業。 假設有一個具有下列屬性的 Service Fabric 應用程式：

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "1"; "NewParameter" = "testBefore" }
```

現在，使用**get-servicefabricapplicationupgrade** Cmdlet 來升級應用程式。 這個範例會顯示受監視的升級，但也可以使用未受監視的升級。 若要查看此 Cmdlet 接受之旗標的完整描述，請參閱[Azure Service Fabric PowerShell 模組參考](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps#parameters)

```PowerShell
PS C:\> $appParams = @{ "ImportantParameter" = "2"; "NewParameter" = "testAfter"}

PS C:\> Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/Application1 -ApplicationTypeVers
ion 1.0.0 -ApplicationParameter $appParams -Monitored

```

升級之後，請確認應用程式具有已更新的參數和相同的版本：

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "2"; "NewParameter" = "testAfter" }
```

## <a name="roll-back-application-upgrades"></a>復原應用程式升級

雖然升級可以向前復原為三個模式其中之一 (Monitored、UnmonitoredAuto 或 UnmonitoredManual)，但是只能復原為 UnmonitoredAuto 或 UnmonitoredManual 模式。 在「UnmonitoredAuto」模式中復原的運作方式與向前復原相同，例外的是 UpgradeReplicaSetCheckTimeout 的預設值不同 - 請參閱[應用程式升級參數](service-fabric-application-upgrade-parameters.md)。 在「UnmonitoredManual」模式中復原的運作方式與向前復原相同 - 復原會在完成每個 UD 之後自行暫止，必須明確地使用 [Resume-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps) 以繼續復原。

當「Monitored」模式中升級的健康情況原則，違反 FailureAction「復原」時 (請參閱[應用程式升級參數](service-fabric-application-upgrade-parameters.md)) 或明確地使用 [Start-ServiceFabricApplicationRollback](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationrollback?view=azureservicefabricps) 時，復原會自動觸發。

在復原期間，UpgradeReplicaSetCheckTimeout 的值和模式仍然可以使用 [Update-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricapplicationupgrade?view=azureservicefabricps) 隨時變更。

## <a name="next-steps"></a>後續步驟
[使用 Visual Studio 升級您的應用程式](service-fabric-application-upgrade-tutorial.md) 將引導您完成使用 Visual Studio 進行應用程式升級的步驟。

[使用 PowerShell 升級您的應用程式](service-fabric-application-upgrade-tutorial-powershell.md) 將引導您完成使用 PowerShell 進行應用程式升級的步驟。

使用 [升級參數](service-fabric-application-upgrade-parameters.md)來控制您應用程式的升級方式。

了解如何使用 [資料序列化](service-fabric-application-upgrade-data-serialization.md)，以讓您的應用程式升級相容。

參考 [疑難排解應用程式升級](service-fabric-application-upgrade-troubleshooting.md)中的步驟，以修正應用程式升級中常見的問題。

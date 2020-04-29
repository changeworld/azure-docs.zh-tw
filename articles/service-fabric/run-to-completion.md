---
title: Service Fabric 中的 RunToCompletion 語義
description: 描述 Service Fabric 中的 RunToCompletion 語義。
author: shsha-msft
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: shsha
ms.openlocfilehash: adf4b11412aa752144d4ed4fef06d2de1d76598d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431289"
---
# <a name="runtocompletion"></a>RunToCompletion

從7.1 版開始，Service Fabric 支援[容器][containers-introduction-link]和[來賓可執行檔][guest-executables-introduction-link]應用程式的**RunToCompletion**語義。 這些語義可讓應用程式和服務完成工作並結束，相對於一律執行應用程式和服務。

繼續進行本文之前，建議您先熟悉[Service Fabric 應用程式模型][application-model-link]和[Service Fabric 裝載模型][hosting-model-link]。

> [!NOTE]
> 使用[Reliable Services][reliable-services-link]程式設計模型所撰寫的服務目前不支援 RunToCompletion 的語義。
 
## <a name="runtocompletion-semantics-and-specification"></a>RunToCompletion 的語義和規格
匯[入 ServiceManifest][application-and-service-manifests-link]時，可以將 RunToCompletion 的語義指定為**ExecutionPolicy** 。 所有組成 ServiceManifest 的 Codepackage 都會繼承指定的原則。 下列 ApplicationManifest 程式碼片段提供範例。

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="RunToCompletionServicePackage" ServiceManifestVersion="1.0"/>
  <Policies>
    <ExecutionPolicy Type="RunToCompletion" Restart="OnFailure"/>
  </Policies>
</ServiceManifestImport>
```
**ExecutionPolicy**允許下列兩個屬性：
* **類型：** **RunToCompletion**目前是此屬性唯一允許的值。
* **重新開機：** 這個屬性會指定在失敗時，套用至組成 ServicePackage 之 Codepackage 的重新開機原則。 以**非零結束代碼**結束的 CodePackage 會被視為失敗。 此屬性允許的值為**OnFailure** ，而且**永遠不會**使用**OnFailure**做為預設值。

當重新開機原則設定為**OnFailure**時，如果有任何 CodePackage 失敗 **（非零的結束代碼）**，則會重新開機，並在重複失敗之間進行轉型。 當重新開機原則設定為 [**永不**] 時，如果有任何 CodePackage 失敗，則 DeployedServicePackage 的部署狀態會標示為 [**失敗**]，但允許其他 codepackage 繼續執行。 如果組成 ServicePackage 的所有 Codepackage 都執行到成功完成 **（結束代碼0）**，則 DeployedServicePackage 的部署狀態會標示為**RanToCompletion**。 

## <a name="complete-example-using-runtocompletion-semantics"></a>使用 RunToCompletion 語義的完整範例

讓我們看看使用 RunToCompletion 語義的完整範例。

> [!IMPORTANT]
> 下列範例假設您已熟悉如何[使用 Service Fabric 和 Docker 建立 Windows 容器應用程式][containers-getting-started-link]。
>
> 這個範例會參考 mcr.microsoft.com/windows/nanoserver:1809。 Windows Server 容器在主機 OS 的所有版本之間不相容。 若要深入了解，請參閱 [Windows 容器版本相容性](https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/version-compatibility)。

下列 ServiceManifest 描述由兩個代表容器的 Codepackage 所組成的 ServicePackage。 *RunToCompletionCodePackage1*只會將訊息記錄到**stdout**並結束。 *RunToCompletionCodePackage2*會 ping 回送位址一段時間，然後結束代碼為**0**、 **1**或**2**。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ServiceManifest Name="WindowsRunToCompletionServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>Windows RunToCompletion Service</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="WindowsRunToCompletionServiceType"  UseImplicitHost="true"/>
  </ServiceTypes>
  <CodePackage Name="RunToCompletionCodePackage1" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from RunToCompletionCodePackage1 &amp;&amp; exit 0</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="RunToCompletionCodePackage2" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/v,/c,ping 127.0.0.1 &amp;&amp; set /a exitCode=%random% % 3 &amp;&amp; exit !exitCode!</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>
</ServiceManifest>
```

下列 ApplicationManifest 描述以上述的 ServiceManifest 為基礎的應用程式。 它會使用**OnFailure**的重新開機原則指定*WindowsRunToCompletionServicePackage*的**RunToCompletion** **ExecutionPolicy** 。 啟用*WindowsRunToCompletionServicePackage*時，將會啟動其構成 codepackage。 *RunToCompletionCodePackage1*應該在第一次啟用時成功結束。 不過， *RunToCompletionCodePackage2*可能會失敗 **（非零的結束代碼）**，在這種情況下，它會在**OnFailure**重新開機原則之後重新開機。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WindowsRunToCompletionApplicationType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <Description>Windows RunToCompletion Application</Description>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="WindowsRunToCompletionServicePackage" ServiceManifestVersion="1.0"/>
    <Policies>
      <ExecutionPolicy Type="RunToCompletion" Restart="OnFailure"/>
    </Policies>
  </ServiceManifestImport>

  <DefaultServices>
    <Service Name="WindowsRunToCompletionService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="WindowsRunToCompletionServiceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
## <a name="querying-deployment-status-of-a-deployedservicepackage"></a>查詢 DeployedServicePackage 的部署狀態
DeployedServicePackage 的部署狀態可以使用[ServiceFabricDeployedServicePackage][deployed-service-package-link]從 PowerShell 查詢，或從 c # 使用[FabricClient][fabric-client-link] API [fabricclient.queryclient.getdeployedservicepackagelistasync （string，Uri，string）][deployed-service-package-fabricclient-link]

## <a name="considerations-when-using-runtocompletion-semantics"></a>使用 RunToCompletion 語義時的考慮

針對目前的 RunToCompletion 支援，應注意下列幾點。
* 這些語義僅支援[容器][containers-introduction-link]和[來賓可執行檔][guest-executables-introduction-link]應用程式。
* 不允許具有 RunToCompletion 語義之應用程式的升級案例。 如有必要，使用者應該刪除並重新建立這類應用程式。
* 容錯移轉事件可能會導致 Codepackage 在成功完成之後、相同節點或叢集的其他節點上重新執行。 容錯移轉事件的範例包括：節點重新開機，以及在節點上 Service Fabric 執行時間升級。

## <a name="next-steps"></a>後續步驟

如需相關資訊，請參閱下列文章。

* [Service Fabric 和容器。][containers-introduction-link]
* [Service Fabric 和來賓可執行檔。][guest-executables-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[containers-getting-started-link]: service-fabric-get-started-containers.md
[guest-executables-introduction-link]: service-fabric-guest-executables-introduction.md
[reliable-services-link]: service-fabric-reliable-services-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md
[application-and-service-manifests-link]: service-fabric-application-and-service-manifests.md
[setup-entry-point-link]: service-fabric-run-script-at-service-startup.md
[deployed-service-package-working-with-link]: service-fabric-hosting-model.md#work-with-a-deployed-service-package
[deployed-code-package-link]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedcodepackage
[deployed-service-package-link]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedservicePackage
[fabric-client-link]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient
[deployed-service-package-fabricclient-link]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync


---
title: 在服務結構中執行完成語意
description: 描述服務結構中的 Runto 完成語義。
author: shsha-msft
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: shsha
ms.openlocfilehash: adf4b11412aa752144d4ed4fef06d2de1d76598d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431289"
---
# <a name="runtocompletion"></a>執行完成

從版本 7.1 開始,Service Fabric 支援[容器][containers-introduction-link]和[來賓可執行][guest-executables-introduction-link]應用程式的**RunTo 完成**語義。 這些語義支援始終運行應用程式和服務的應用程式和服務完成任務和退出。

在繼續本文之前,我們建議熟悉[服務結構應用程式模型][application-model-link][和服務結構託管模型][hosting-model-link]。

> [!NOTE]
> 使用[可靠服務][reliable-services-link]程式設計模型編寫的服務目前不支援 RunTo 完成語義。
 
## <a name="runtocompletion-semantics-and-specification"></a>執行完成文意和規範
[匯入服務清單時,][application-and-service-manifests-link]可以將 Runto 完成文意指定為**執行原則**。 指定的策略由包含服務清單的所有代碼包繼承。 以下應用程式清單.xml 代碼段提供了一個範例。

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="RunToCompletionServicePackage" ServiceManifestVersion="1.0"/>
  <Policies>
    <ExecutionPolicy Type="RunToCompletion" Restart="OnFailure"/>
  </Policies>
</ServiceManifestImport>
```
**執行原則**允許以下兩個屬性:
* **類型****:RunTo 完成**目前是此屬性的唯一允許值。
* **重新啟動:** 此屬性指定在失敗時應用於包含服務包的代碼包的重新啟動策略。 使用**非零退出代碼**退出的代碼包被視為失敗。 此屬性的允許值為 **「打開失敗****」,從不**以 **「未失敗**」為預設值。

當重新啟動策略設定為**OnFailure**時,如果任何程式碼包失敗 **(非零退出代碼),** 則重新啟動它,在重複失敗之間回退。 如果任何程式碼包失敗,重新啟動策略設置為 **「從不**」,則部署服務包的部署狀態將標記為**失敗**,但允許其他程式碼包繼續執行。 如果包含服務套件的所有程式碼套件都執行到成功完成 **(離開代碼 0),** 則部署服務套件的部署狀態將標記為**RanTo 完成**。 

## <a name="complete-example-using-runtocompletion-semantics"></a>使用 Runto 完成文意完成範例

讓我們看一個使用 RunToComplete 語義的完整範例。

> [!IMPORTANT]
> 下面的範例假定熟悉使用 Service Fabric 和 Docker 建立[Windows 容器應用程式][containers-getting-started-link]。
>
> 此示例引用mcr.microsoft.com/windows/nanoserver:1809。 Windows Server 容器在主機 OS 的所有版本之間不相容。 若要深入了解，請參閱 [Windows 容器版本相容性](https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/version-compatibility)。

以下 ServiceManifest.xml 描述了由兩個代碼包組成的服務包,它們表示容器。 *RunTo 完成代碼包1*只需記錄一條消息以**進行停滯**和退出。 *RunToTo完成代碼包2* ping回環位址一段時間,然後退出代碼**為0,1**或**2。** **1**

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

以下應用程式清單.xml 描述了基於上述討論的 ServiceManifest.xml 的應用程式。 它為*WindowsRunto 完成服務套件*指定**執行完成****執行策略**,並具有 **「OnFailure」** 的重新啟動策略。 啟動*WindowsRunTo 完成服務包*後,其組成代碼包將啟動。 *運行完成代碼包1*應在第一次啟動時成功退出。 但是 *,Runto完成代碼包2*可能會失敗 **(非零退出代碼),** 在這種情況下,它將重新啟動,因為重新啟動策略是**OnFail。**

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
## <a name="querying-deployment-status-of-a-deployedservicepackage"></a>查詢已部署服務套件的部署狀態
可以使用[取得服務交換服務套件][deployed-service-package-link]從 PowerShell 查詢已部署服務套件的部署狀態,也可以使用[FabricClient][fabric-client-link] API[取得服務套件Listasync(字串、Uri、字串)][deployed-service-package-fabricclient-link]從 C# 查詢

## <a name="considerations-when-using-runtocompletion-semantics"></a>使用 Runto 完成文意時的注意事項

當前運行完成支援應記下以下幾點。
* 這些語義僅支援[容器][containers-introduction-link]和[來賓可執行][guest-executables-introduction-link]應用程式。
* 不允許使用 RunTo 完成語義的應用程式升級方案。 如有必要,使用者應刪除並重新創建此類應用程式。
* 故障轉移事件可能導致程式碼包在成功完成後在同一節點或群集的其他節點上重新執行。 故障轉移事件的範例包括節點上的節點重新啟動和服務結構運行時升級。

## <a name="next-steps"></a>後續步驟

有關相關信息,請參閱以下文章。

* [服務結構和容器。][containers-introduction-link]
* [服務結構和來賓可執行檔。][guest-executables-introduction-link]

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


---
title: Service Fabric 中的初始化運算式 Codepackage
description: 描述 Service Fabric 中的初始化運算式 Codepackage。
author: shsha-msft
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: shsha
ms.openlocfilehash: 3be079b97c2660437344f88203fdda06cc6d6740
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86258973"
---
# <a name="initializer-codepackages"></a>初始設定式 Codepackage

從7.1 版開始，Service Fabric 支援[容器][containers-introduction-link]和[來賓可執行檔][guest-executables-introduction-link]應用程式的**初始化運算式 codepackage** 。 初始化運算式 Codepackage 可讓您在其他 Codepackage 開始執行之前，先在 ServicePackage 範圍執行初始化。 其與 ServicePackage 的關聯性類似于 CodePackage 的[SetupEntryPoint][setup-entry-point-link] 。

繼續進行本文之前，建議您先熟悉[Service Fabric 應用程式模型][application-model-link]和[Service Fabric 裝載模型][hosting-model-link]。

> [!NOTE]
> 使用[Reliable Services][reliable-services-link]程式設計模型所撰寫的服務目前不支援初始化運算式 codepackage。
 
## <a name="semantics"></a>語意

初始化運算式 CodePackage 預期會執行**成功完成 (結束代碼 0) **。 失敗的初始化運算式 CodePackage 會重新開機，直到成功完成為止。 系統會允許多個初始化運算式 Codepackage，並在 ServicePackage 開始執行中的其他 Codepackage 之前，依照**指定的順序**，**順序**執行到**成功完成**。

## <a name="specifying-initializer-codepackages"></a>指定初始化運算式 Codepackage
您可以藉由將 ServiceManifest 中的**初始化運算式**屬性設定為**True** ，將 CodePackage 標記為初始化運算式。 當有多個初始化運算式 Codepackage 時，可以透過**ExecOrder**屬性指定其執行順序。 **ExecOrder**必須是非負值的整數，而且只對初始化運算式 codepackage 有效。 較低值為**ExecOrder**的初始化運算式 codepackage 會先執行。 如果未指定初始化運算式 CodePackage 的**ExecOrder** ，則會假設為預設值0。 未指定具有相同**ExecOrder**值之初始化運算式 codepackage 的相對執行順序。

下列 ServiceManifest 程式碼片段描述三個已標記為初始化運算式的 Codepackage。 當此 ServicePackage 啟動時，會先執行*InitCodePackage0* ，因為它的最小值為**ExecOrder**。 成功完成 (結束代碼 0) *InitCodePackage0*時，就會執行*InitCodePackage1* 。 最後，當*InitCodePackage1*成功完成時，就會執行*WorkloadCodePackage* 。

```xml
<CodePackage Name="InitCodePackage0" Version="1.0" Initializer="true" ExecOrder="0">
  ...
</CodePackage>

<CodePackage Name="InitCodePackage1" Version="1.0" Initializer="true" ExecOrder="1">
  ...
</CodePackage>

<CodePackage Name="WorkloadCodePackage" Version="1.0">
  ...
</CodePackage>
```
## <a name="complete-example-using-initializer-codepackages"></a>使用初始化運算式 Codepackage 的完整範例

讓我們來看一個使用初始化運算式 Codepackage 的完整範例。

> [!IMPORTANT]
> 下列範例假設您已熟悉如何[使用 Service Fabric 和 Docker 建立 Windows 容器應用程式][containers-getting-started-link]。
>
> 這個範例會參考 mcr.microsoft.com/windows/nanoserver:1809。 Windows Server 容器在主機 OS 的所有版本之間不相容。 若要深入了解，請參閱 [Windows 容器版本相容性](/virtualization/windowscontainers/deploy-containers/version-compatibility)。

下列 ServiceManifest.xml 是以先前所述的 ServiceManifest 程式碼片段為基礎。 *InitCodePackage0*、 *InitCodePackage1*和*WorkloadCodePackage*都是代表容器的 codepackage。 啟用時，會先執行*InitCodePackage0* 。 它會將訊息記錄到檔案並結束。 接下來，會執行*InitCodePackage1* ，這也會將訊息記錄到檔案並結束。 最後， *WorkloadCodePackage*會開始執行。 它也會將訊息記錄到檔案中，將檔案的內容輸出到**stdout** ，然後永遠進行 ping。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ServiceManifest Name="WindowsInitCodePackageServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>Windows Init CodePackage Service</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="WindowsInitCodePackageServiceType"  UseImplicitHost="true"/>
  </ServiceTypes>
  <CodePackage Name="InitCodePackage0" Version="1.0" Initializer="true" ExecOrder="0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from InitCodePackage0. &gt; C:\WorkspaceOnContainer\log.txt</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="InitCodePackage1" Version="1.0" Initializer="true" ExecOrder="1">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from InitCodePackage1. &gt;&gt; C:\WorkspaceOnContainer\log.txt</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="WorkloadCodePackage" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from WorkloadCodePackage. &gt;&gt; C:\WorkspaceOnContainer\log.txt &amp;&amp; type C:\WorkspaceOnContainer\log.txt &amp;&amp; ping -t 127.0.0.1 &gt; nul</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>
</ServiceManifest>
```

下列 ApplicationManifest.xml 會根據上面討論的 ServiceManifest.xml 來描述應用程式。 請注意，它會為所有容器指定相同的**磁片**區掛接，亦即**C:\WorkspaceOnHost**會裝載在所有三個容器的**C:\WorkspaceOnContainer**上。 最後的結果是所有容器都會依照啟動的順序寫入相同的記錄檔。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WindowsInitCodePackageApplicationType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <Description>Windows Init CodePackage Application</Description>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="WindowsInitCodePackageServicePackage" ServiceManifestVersion="1.0"/>
    <Policies>
      <ContainerHostPolicies CodePackageRef="InitCodePackage0" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>

     <ContainerHostPolicies CodePackageRef="InitCodePackage1" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>

      <ContainerHostPolicies CodePackageRef="WorkloadCodePackage" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>

  <DefaultServices>
    <Service Name="WindowsInitCodePackageService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="WindowsInitCodePackageServiceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
成功啟用 ServicePackage 之後， **C:\WorkspaceOnHost\log.txt**的內容應該如下所示。

```console
C:\Users\test>type C:\WorkspaceOnHost\log.txt
Hi from InitCodePackage0.
Hi from InitCodePackage1.
Hi from WorkloadCodePackage.
```

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
[setup-entry-point-link]: service-fabric-run-script-at-service-startup.md

---
title: 服務結構中初始化程式
description: 描述服務結構中的初始化器代碼包。
author: shsha-msft
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: shsha
ms.openlocfilehash: 8483e00f55d0dd49ba57db58b99b237ce0a169e5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430626"
---
# <a name="initializer-codepackages"></a>初始化器碼包

從版本 7.1 開始,Service Fabric 支援[容器][containers-introduction-link]和[來賓可執行][guest-executables-introduction-link]應用程式的**初始化器代碼包**。 初始化器代碼包提供了在其他代碼包開始執行之前在服務包作用域執行初始化的機會。 它們與服務包的關係類似於代碼包的[SetupEntryPoint。][setup-entry-point-link]

在繼續本文之前,我們建議熟悉[服務結構應用程式模型][application-model-link][和服務結構託管模型][hosting-model-link]。

> [!NOTE]
> 使用[可靠服務][reliable-services-link]程式設計模型編寫的服務目前不支援初始化碼包。
 
## <a name="semantics"></a>語意

初始化器代碼包預計將運行到**成功完成(退出代碼 0)。** 故障的初始化程式代碼包將重新啟動,直到它成功完成。 在服務包中的其他程式碼包開始執行**之前,允許**並**sequentially**按指定順序執行多個初始化器代碼包,並按**指定順序**執行。

## <a name="specifying-initializer-codepackages"></a>指定初始化器碼包
透過在服務清單中將**初始化器**屬性設定為**true,** 可以將程式碼包標記為初始化程式。 當有多個初始化器代碼包時,可以通過**ExecOrder**屬性指定其執行順序。 **ExecOrder**必須是非負整數,並且僅適用於初始化代碼包。 首先執行具有**執行 Order**較低值的初始化代碼包。 如果未為初始化器代碼包指定**ExecOrder,** 則假定預設值 0。 未指定具有相同**ExecOrder**值的初始化代碼包的相對執行順序。

以下服務清單代碼段描述了三個代碼包,其中兩個標記為初始化程式。 啟動此服務套件時,首先執行*InitCodePackage0,* 因為它具有最低值**ExecOrder**。 成功完成*InitCode 包 0*後,將執行*InitCode 包 1。* 最後,在成功完成*InitCode 套件 1*後,將執行*工作負載程式*。

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
## <a name="complete-example-using-initializer-codepackages"></a>使用初始化器碼套件的完整範例

讓我們看一個使用初始化器代碼包的完整範例。

> [!IMPORTANT]
> 下面的範例假定熟悉使用 Service Fabric 和 Docker 建立[Windows 容器應用程式][containers-getting-started-link]。
>
> 此示例引用mcr.microsoft.com/windows/nanoserver:1809。 Windows Server 容器在主機 OS 的所有版本之間不相容。 若要深入了解，請參閱 [Windows 容器版本相容性](https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/version-compatibility)。

以下服務清單.xml 基於前面介紹的服務清單代碼段。 *InitCode包0* *,InitCode包1*和*工作負載代碼包*是表示容器的代碼包。 啟動後,首先執行*InitCode 包 0。* 它將消息記錄到檔並退出。 接下來,執行*InitCodePackage1,* 該程式還會將消息記錄到檔和退出。 最後,*工作負載程式碼包*開始執行。 它還將消息記錄到檔,輸出檔的內容以**進行停滯**,然後永久 ping。

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

以下應用程式清單.xml 描述了基於上述討論的 ServiceManifest.xml 的應用程式。 請注意,它為所有容器指定相同的**卷**裝載,即**C:_WorkspaceOnHost**安裝在所有三個容器上的**C:_WorkspaceOn容器**中。 最終結果是,所有容器都按啟動順序寫入同一日誌檔。

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
成功啟動服務包后 **,C:_WorkspaceOnHost_log.txt**的內容應如下。

```console
C:\Users\test>type C:\WorkspaceOnHost\log.txt
Hi from InitCodePackage0.
Hi from InitCodePackage1.
Hi from WorkloadCodePackage.
```

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
[setup-entry-point-link]: service-fabric-run-script-at-service-startup.md


---
title: 描述 Azure 服務結構應用和服務
description: 說明如何使用資訊清單來描述 Service Fabric 應用程式和服務。
ms.topic: conceptual
ms.date: 8/12/2019
ms.openlocfilehash: 6014ef6a9b6ec810aafd5e5be96223b8ed92d576
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75349971"
---
# <a name="service-fabric-application-and-service-manifests"></a>Service Fabric 應用程式和服務資訊清單
本文說明如何使用 ApplicationManifest.xml 和 ServiceManifest.xml 檔案來定義 Service Fabric 應用程式和服務及設定其版本。  如需更詳細的範例，請參閱[應用程式和服務資訊清單範例](service-fabric-manifest-examples.md)。  這些資訊清單檔的 XML 結構描述記載於 [ServiceFabricServiceModel.xsd 結構描述文件](service-fabric-service-model-schema.md)中。

> [!WARNING]
> 資訊清單 XML 檔案結構描述會強制修正子元素順序。  部分的因應措施是在 Visual Studio 中開啟 "C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd"，然後撰寫或修改任何的 Service Fabric 資訊清單。 這會讓您能夠檢查子元素的順序，並提供 Intelli-sense。

## <a name="describe-a-service-in-servicemanifestxml"></a>在 ServiceManifest.xml 中描述服務
服務資訊清單以宣告方式定義服務類型和版本。 它會指定如服務類型的服務中繼資料、健康狀態屬性、負載平衡度量、服務二進位檔和組態檔。  換句話說，它會描述組成服務封裝的程式碼、組態和資料封裝，以支援一或多個服務類型。 服務資訊清單可以包含多個程式碼、組態和資料套件，而這些套件可以獨立設定版本。 以下是[投票範例應用程式](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart)之 ASP.NET Core Web 前端服務的服務資訊清單 (這裡有一些[更詳細的範例](service-fabric-manifest-examples.md))：

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType. 
         This name must match the string used in RegisterServiceType call in Program.cs. -->
    <StatelessServiceType ServiceTypeName="VotingWebType" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

**版本** 屬性為非結構化字串，不是由系統剖析。 版本屬性用於設定每個元件的版本以進行升級。

**ServiceTypes** 會宣告此資訊清單中 **CodePackages** 支援哪些服務類型。 當針對其中一種服務類型來具現化服務時，會藉由執行其進入點來啟動此資訊清單中宣告的所有程式碼封裝。 產生的處理程序預期在執行階段註冊支援的服務類型。 服務類型是在資訊清單層級而非程式碼套件層級宣告。 因此如果有多個程式碼封裝，每當系統尋找任何一個宣告的服務類型時，它們都會啟動。

**EntryPoint** 指定的可執行檔通常是長時間執行的服務主機。 **SetupEntryPoint** 是以與 Service Fabric 相同的認證執行的特殊權限進入點 (通常 *LocalSystem* 帳戶)，優先於任何其他進入點。  有個別設定的進入點，就不需要使用較高權限來長時間執行服務主機。 **EntryPoint** 指定的可執行檔是在 **SetupEntryPoint** 成功結束之後執行。 如果程序曾經終止或當機，則產生的程序會受到監視並重新啟動 (以 **SetupEntryPoint**再次開始)。  

使用 **SetupEntryPoint** 的一般案例，是當您在服務啟動之前執行可執行檔，或使用提高的權限來執行作業時。 例如：

* 設定及初始化服務可執行檔需要的環境變數。 這不限於透過 Service Fabric 程式設計模型撰寫的執行檔。 例如，npm.exe 部署 node.js 應用程式，需要設定某些環境變數。
* 透過安裝安全性憑證設定存取控制。

如需有關如何設定 SetupEntryPoint 的詳細資訊，請參閱[設定服務設定進入點的原則](service-fabric-application-runas-security.md)

**EnvironmentVariables** (未設定於前一個範例中) 提供針對此程式碼套件設定的環境變數清單。 您可以在 `ApplicationManifest.xml` 中覆寫環境變數，以針對不同的服務執行個體提供不同的值。 

**DataPackage** (未設定於前一個範例中) 會宣告 **Name** 屬性所命名的資料夾，包含由程序在執行階段使用的任意靜態資料。

**ConfigPackage** 宣告 **Name** 屬性所命名的資料夾，其中包含 *Settings.xml* 檔案。 設定檔案包含程序在執行階段讀回的使用者定義、成對的索引鍵/值設定等區段。 在升級期間，如果只有 **ConfigPackage** **版本**已變更，則不會重新啟動執行中程序。 相反地，回呼會通知程序組態設定已變更，因此它們可以動態方式重新載入。 以下是 *Settings.xml* 檔案的範例：

```xml
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MyConfigurationSection">
    <Parameter Name="MySettingA" Value="Example1" />
    <Parameter Name="MySettingB" Value="Example2" />
  </Section>
</Settings>
```

服務交換矩陣服務**終結點**是服務結構資源的示例。 無需更改已編譯的代碼，即可聲明/更改服務結構資源。 透過應用程式資訊清單中的 **SecurityGroup**，即可控制存取服務資訊清單中指定的 Service Fabric 資源。 在服務資訊清單中定義端點資源時，若沒有明確指定連接埠，Service Fabric 會從保留的應用程式連接埠範圍指派連接埠。 深入了解[指定或覆寫端點資源](service-fabric-service-manifest-resources.md)。

 
> [!WARNING]
> 根據設計，靜態埠不應與 ClusterManifest 中指定的應用程式埠範圍重疊。 如果指定靜態埠，請將其分配到應用程式埠範圍之外，否則將導致埠衝突。 使用版本 6.5CU2 時，我們將在檢測到此類衝突時發出**運行狀況警告**，但允許部署繼續與已發貨的 6.5 行為同步。 但是，我們可能會阻止應用程式部署從下一個主要版本。
>

<!--
For more information about other features supported by service manifests, refer to the following articles:

*TODO: LoadMetrics, PlacementConstraints, ServicePlacementPolicies
*TODO: Health properties
*TODO: Trace filters
*TODO: Configuration overrides
-->

## <a name="describe-an-application-in-applicationmanifestxml"></a>在 ApplicationManifest.xml 中描述應用程式
應用程式資訊清單以宣告方式描述應用程式類型和版本。 它指定服務組成中繼資料，例如穩定的名稱、分割配置、執行個體計數/複寫因數、安全性/隔離原則、安置限制、組態覆寫和組成服務類型。 也說明要放置應用程式的負載平衡網域。

因此，應用程式資訊清單描述應用程式層級的元素，並參考用來組成應用程式類型的一個或多個服務資訊清單。 以下是[投票範例應用程式](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart)的應用程式資訊清單 (這裡有一些[更詳細的範例](service-fabric-manifest-examples.md))：

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="VotingData_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingData_PartitionCount" DefaultValue="1" />
    <Parameter Name="VotingData_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingWeb_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingDataPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="VotingData">
      <StatefulService ServiceTypeName="VotingDataType" TargetReplicaSetSize="[VotingData_TargetReplicaSetSize]" MinReplicaSetSize="[VotingData_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[VotingData_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    <Service Name="VotingWeb" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="VotingWebType" InstanceCount="[VotingWeb_InstanceCount]">
        <SingletonPartition />
         <PlacementConstraints>(NodeType==NodeType0)</PlacementConstraints
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

如同服務資訊清單， **版本** 屬性為非結構化字串，不是由系統剖析。 版本屬性也用於設定每個元件的版本以進行升級。

**Parameters** 定義整個應用程式資訊清單使用的參數。 這些參數的值可在具現化應用程式時予以提供，而且可覆寫應用程式或服務的組態設定。  如果未在應用程式具現化期間變更預設參數值，則會使用此值。 若要了解如何針對個別環境維護不同的應用程式和服務參數，請參閱 [管理多個環境的應用程式參數](service-fabric-manage-multiple-environment-app-configuration.md)

**ServiceManifestImport** 包含組成此應用程式類型之服務資訊清單的參考。 應用程式資訊清單可以包含多個服務資訊清單匯入，而且可以獨立設定每個匯入的版本。 匯入的服務資訊清單會決定此應用程式類型中哪些服務類型有效。 在 ServiceManifestImport 內，您可覆寫 Settings.xml 中的組態值和 ServiceManifest.xml 檔案中的環境變數。 可以在已匯入的服務資訊清單上設定端點繫結、安全性和存取以及套件共用的 **Policies** (未設定於前一個範例中)。  如需詳細資訊，請參閱[設定應用程式的安全性原則](service-fabric-application-runas-security.md)。

**DefaultServices** 宣告服務執行個體，該執行個體會在每次應用程式針對此應用程式類型具現化時建立。 預設服務只是為了方便起見，在建立之後，其行為在每個方面就像正常的服務。 它們會與應用程式執行個體中的其他任何服務一起升級，也可以一起移除。 應用程式資訊清單可以包含多個預設服務。

**Certificates** (未設定於前一個範例中) 會宣告用來[設定 HTTPS 端點](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service)或[在應用程式資訊清單中加密密碼](service-fabric-application-secret-management.md)。

**放置約束**是定義服務應運行位置的語句。 這些語句附加到您為一個或多個節點屬性選擇的各個服務。 有關詳細資訊，請參閱[放置約束和節點屬性語法](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#placement-constraints-and-node-property-syntax)

**策略**（未在上例中設置）描述日誌集合、[預設運行為](service-fabric-application-runas-security.md)、[運行狀況](service-fabric-health-introduction.md#health-policies)和在應用程式級別設置[的安全訪問](service-fabric-application-runas-security.md)策略，包括服務是否有權訪問 Service Fabric 運行時。

> [!NOTE] 
> 預設情況下，Service Fabric 應用程式可以訪問 Service Fabric 運行時，其形式是終結點接受特定于應用程式的請求，環境變數指向包含 Fabric 和應用程式特定檔的主機上的檔路徑. 請考慮在應用程式承載不受信任的代碼（即其來源不明或應用程式擁有者知道執行不安全的代碼）時禁用此訪問。 有關詳細資訊，請參閱[服務結構中的安全最佳實踐](service-fabric-best-practices-security.md#platform-isolation)。 
>

**Principals** (未設定於前一個範例中) 說明[執行服務和保護服務資源](service-fabric-application-runas-security.md)所需的安全性主體 (使用者或群組)。  主體會在 **Policies** 區段中參考。





<!--
For more information about other features supported by application manifests, refer to the following articles:

*TODO: Security, Principals, RunAs, SecurityAccessPolicy
*TODO: Service Templates
-->



## <a name="next-steps"></a>後續步驟
- [封裝應用程式](service-fabric-package-apps.md)並讓它準備好進行部署。
- [部署與移除應用程式](service-fabric-deploy-remove-applications.md)。
- [為不同的應用程式執行個體設定參數和環境變數](service-fabric-manage-multiple-environment-app-configuration.md)。
- [為應用程式佈建安全性原則](service-fabric-application-runas-security.md)。
- [設定 HTTPS 端點](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service)。
- [在 Service Fabric 應用程式中加密密碼](service-fabric-application-secret-management.md)

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png




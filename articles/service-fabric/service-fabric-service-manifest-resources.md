---
title: 指定 Service Fabric 服務端點
description: 如何在服務資訊清單中描述端點資源，包括如何設定 HTTPS 端點
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: 88e71d15829e68bde635f5b4d40224b8fa914f40
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81417584"
---
# <a name="specify-resources-in-a-service-manifest"></a>在服務資訊清單中指定資源
## <a name="overview"></a>總覽
服務資訊清單可讓服務所使用的資源進行宣告或變更，而不需要變更已編譯的程式碼。 Service Fabric 支援設定服務的端點資源。 透過應用程式資訊清單中的 SecurityGroup，即可控制存取服務資訊清單中的指定資源。 資源宣告可讓您在部署階段變更這些資源，也就是服務不需要導入新的組態機制。 ServiceManifest.xml 檔案的結構描述定義是和 Service Fabric SDK 及工具一起安裝在 *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*。

## <a name="endpoints"></a>端點
在服務資訊清單中定義端點資源時，若沒有明確指定連接埠，Service Fabric 會從保留的應用程式連接埠範圍指派連接埠。 例如，請看本段落之後提供的資訊清單片段中所指定的端點 *ServiceEndpoint1* 。 此外，服務也可以在資源中要求特定連接埠。 不同的連接埠號碼可以指派給在不同叢集節點上執行的服務複本，而在同一節點上執行的服務複本可以共用連接埠。 然後服務複本就可以在需要時使用這些連接埠進行複寫和接聽用戶端要求。

啟動指定 HTTPs 端點的服務時，Service Fabric 將會設定埠的存取控制專案、將指定的伺服器憑證系結至埠，同時也將服務執行的身分識別授與憑證的私密金鑰許可權。 每次 Service Fabric 啟動，或應用程式的憑證宣告透過升級變更時，就會叫用啟用流程。 端點憑證也會針對變更/更新進行監視，並且會視需要定期重新套用許可權。

服務終止後，Service Fabric 將會清除端點存取控制專案，並移除憑證系結。 不過，將不會清除任何套用至憑證私密金鑰的許可權。

> [!WARNING] 
> 依照設計，靜態埠不應該與 ClusterManifest 中指定的應用程式埠範圍重迭。 如果您指定靜態通訊埠，請在應用程式埠範圍外指派它，否則會導致埠衝突。 在 release 6.5 CU2 中，我們會在偵測到這類衝突時發出**健全狀況警告**，但讓部署繼續與隨附的6.5 行為同步。 不過，我們可能會防止應用程式部署到下一個主要版本。
>
> 在7.0 版中，我們會在偵測到應用程式埠範圍的使用量超過 HostingConfig：： ApplicationPortExhaustThresholdPercentage （預設80%）時發出**健康情況警告**。
>

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
    <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
    <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
  </Endpoints>
</Resources>
```

如果單一服務套件中有多個程式碼套件，則也需要在 [端點]**** 區段中參考程式碼套件。  例如，如果 **ServiceEndpoint2a** 和 **ServiceEndpoint2b** 是來自參考不同程式碼套件之相同服務套件的端點，則對應至每個端點的程式碼套件釐清如下：

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint2a" Protocol="http" Port="802" CodePackageRef="Code1"/>
    <Endpoint Name="ServiceEndpoint2b" Protocol="http" Port="801" CodePackageRef="Code2"/>
  </Endpoints>
</Resources>
```

請參閱 [設定具狀態的 Reliable Services](service-fabric-reliable-services-configuration.md) ，從設定封裝設定檔 (settings.xml) 深入了解參考端點。

## <a name="example-specifying-an-http-endpoint-for-your-service"></a>範例：指定服務的 HTTP 端點
以下服務資訊清單在 &lt;Resources&gt; 項目中定義了一個 TCP 端點資源和兩個 HTTP 端點資源。

Service Fabric 會自動將 HTTP 端點處理為 ACL。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Stateful1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         This name must match the string used in the RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="Stateful1Type" HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>Stateful1.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an
       independently updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port number on which to
           listen. Note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
      <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
      <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
      <Endpoint Name="ServiceEndpoint4" Protocol="https" Port="14023"/>

      <!-- This endpoint is used by the replicator for replicating the state of your service.
           This endpoint is configured through the ReplicatorSettings config section in the Settings.xml
           file under the ConfigPackage. -->
      <Endpoint Name="ReplicatorEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="example-specifying-an-https-endpoint-for-your-service"></a>範例：指定服務的 HTTPS 端點
HTTPS 通訊協定提供伺服器驗證，也能用於加密用戶端-伺服器通訊。 若要在 Service Fabric 服務上啟用 HTTPS，請在服務資訊清單的 [資源] -> [端點] -> [端點]** 區段指定通訊協定，如先前針對 *ServiceEndpoint3* 端點所示。

> [!NOTE]
> 服務的通訊協定不能在應用程式升級期間變更。 如果它在升級期間變更，將會發生中斷變更。
> 

> [!WARNING] 
> 使用 HTTPS 時，請勿對部署至相同節點的不同服務執行個體 (獨立於應用程式) 使用相同連接埠和憑證。 在不同的應用程式執行個體中，使用相同連接埠來升級兩個不同的服務，將會導致升級失敗。 如需詳細資訊，請參閱[使用 HTTPS 端點來升級多個應用程式](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints)。
>

以下是示範 HTTPS 端點所需設定的範例 ApplicationManifest。 伺服器/端點憑證可能會以指紋或主體一般名稱宣告，而且必須提供值。 EndpointRef 是 ServiceManifest 中 EndpointResource 的參考，其通訊協定必須設定為 ' HTTPs ' 通訊協定。 您可以加入一個以上的 EndpointCertificate。  

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Application1Type"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <EndpointBindingPolicy CertificateRef="SslCertByTP" EndpointRef="ServiceEndpoint3"/>
      <EndpointBindingPolicy CertificateRef="SslCertByCN" EndpointRef="ServiceEndpoint4"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types when an instance of this
         application type is created. You can also create one or more instances of service type by using the
         Service Fabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Stateful1">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_ ]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
  </DefaultServices>
  <Certificates>
    <EndpointCertificate Name="SslCertByTP" X509FindValue="FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF F0" X509StoreName="MY" />  
    <EndpointCertificate Name="SslCertByCN" X509FindType="FindBySubjectName" X509FindValue="ServiceFabric-EndpointCertificateBinding-Test" X509StoreName="MY" />  
  </Certificates>
</ApplicationManifest>
```

若是 Linux 叢集，**MY** 存放區預設為資料夾 **/var/lib/sfcerts**。


## <a name="overriding-endpoints-in-servicemanifestxml"></a>在 ServiceManifest.xml 中覆寫端點

在 ApplicationManifest 中新增 ResourceOverrides 區段，可成為 ConfigOverrides 區段的同層級。 在本節中，您可以在服務資訊清單指定的資源區段中，指定 [端點] 區段的覆寫。 執行階段 5.7.217/SDK 2.7.217 支援覆寫端點。

若要使用 ApplicationParameters 覆寫 ServiceManifest 中的端點，請變更 Secretscertificate，如下所示：

在 ServiceManifestImport 區段中，新增新的區段 "ResourceOverrides"。

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <ResourceOverrides>
      <Endpoints>
        <Endpoint Name="ServiceEndpoint" Port="[Port]" Protocol="[Protocol]" Type="[Type]" />
        <Endpoint Name="ServiceEndpoint1" Port="[Port1]" Protocol="[Protocol1] "/>
      </Endpoints>
    </ResourceOverrides>
        <Policies>
           <EndpointBindingPolicy CertificateRef="SslCertByTP" EndpointRef="ServiceEndpoint"/>
        </Policies>
  </ServiceManifestImport>
```

在參數中新增下列項目：

```xml
  <Parameters>
    <Parameter Name="Port" DefaultValue="" />
    <Parameter Name="Protocol" DefaultValue="" />
    <Parameter Name="Type" DefaultValue="" />
    <Parameter Name="Port1" DefaultValue="" />
    <Parameter Name="Protocol1" DefaultValue="" />
  </Parameters>
```

部署應用程式時，您可以傳入這些值作為 ApplicationParameters。  例如：

```powershell
PS C:\> New-ServiceFabricApplication -ApplicationName fabric:/myapp -ApplicationTypeName "AppType" -ApplicationTypeVersion "1.0.0" -ApplicationParameter @{Port='1001'; Protocol='https'; Type='Input'; Port1='2001'; Protocol='http'}
```

注意：如果提供給 ApplicationParameters 的值為空白，我們要回到 ServiceManifest 中提供的預設值，來取得對應的 EndPointName。

例如：

如果在您指定的 ServiceManifest 中

```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint1" Protocol="tcp"/>
    </Endpoints>
  </Resources>
```

且應用程式參數的 Port1 和 Protocol1 值是 Null 或空白。 連接埠仍是由 ServiceFabric 決定。 且通訊協定將 tcp。

假設您指定錯誤的值。 就像針對埠所指定的字串值為 "Foo"，而不是 int。 Remove-servicefabricapplication 命令將會失敗並出現錯誤：區段 ' ResourceOverrides ' 中名為 ' ServiceEndpoint1 ' 屬性 ' Port1 ' 的覆寫參數無效。 指定的值是 'Foo'，而所需為 'int'。

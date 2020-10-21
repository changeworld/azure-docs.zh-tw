---
title: 將憑證匯入容器
description: 立即了解如何將憑證檔案匯入到 Service Fabric 容器服務。
ms.topic: conceptual
ms.date: 2/23/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 219882a3f7f6db665f1ec311098ef53464773b71
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92313690"
---
# <a name="import-a-certificate-file-into-a-container-running-on-service-fabric"></a>將憑證檔案匯入到 Service Fabric 上執行的容器

> [!NOTE]
> 針對在 Azure 上執行的 Service Fabric 叢集，建議使用 [Service Fabric 應用程式受控識別](./concepts-managed-identity.md) ，從容器內布建應用程式憑證。 受控識別可讓您隔離服務層級的秘密和憑證，並允許應用程式憑證布建成為應用程式工作流程的一部分，而不是基礎結構的工作流程。 CertificateRef 機制將在未來的版本中淘汰。

您可以藉由指定憑證來保護您的容器服務。 Service Fabric 為容器內的服務提供了一種機制，供其存取 Windows 或 Linux 叢集 (5.7 版或更高版本) 節點上所安裝的憑證。 此憑證必須安裝於叢集所有節點上 LocalMachine 底下的憑證存放區中。 對應至此憑證的私密金鑰必須可供使用、可存取，而且 (在 Windows 上) 可匯出。 憑證資訊會在應用程式資訊清單的 `ContainerHostPolicies` 標記底下提供，如下列程式碼片段所示：

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
    <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[Thumbprint1]"/>
    <CertificateRef Name="MyCert2" X509FindValue="[Thumbprint2]"/>
 ```

對於 Windows 叢集，在啟動應用程式時，執行階段會將每個參考的憑證及其對應的私密金鑰匯出為 PFX 檔案，並使用隨機產生的密碼來保護。 PFX 檔案和密碼檔案可分別使用下列環境變數，從容器內部加以存取： 

* Certificates_ServicePackageName_CodePackageName_CertName_PFX
* Certificates_ServicePackageName_CodePackageName_CertName_Password

對於 Linux 叢集，會從容器上 X509StoreName 指定的存放區複製憑證 (PEM)。 Linux 上對應的環境變數為：

* Certificates_ServicePackageName_CodePackageName_CertName_PEM
* Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey

請注意，和檔案 `PEM` 都 `PrivateKey` 包含憑證和未加密的私密金鑰。

或者，如果您已經有所需格式的憑證，而且要在容器內存取該憑證，您可以在應用程式套件內建立資料套件，並在應用程式資訊清單中指定下列項目：

```xml
<ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
  <CertificateRef Name="MyCert1" DataPackageRef="[DataPackageName]" DataPackageVersion="[Version]" RelativePath="[Relative Path to certificate inside DataPackage]" Password="[password]" IsPasswordEncrypted="[true/false]"/>
 ```

容器服務或流程會負責將憑證檔案匯入容器。 若要匯入憑證，您可以使用 `setupentrypoint.sh` 指令碼，或在容器流程內執行自訂程式碼。 用來匯入 PFX 檔的 C# 程式碼範例如下：

```csharp
string certificateFilePath = Environment.GetEnvironmentVariable("Certificates_MyServicePackage_NodeContainerService.Code_MyCert1_PFX");
string passwordFilePath = Environment.GetEnvironmentVariable("Certificates_MyServicePackage_NodeContainerService.Code_MyCert1_Password");
X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
string password = File.ReadAllLines(passwordFilePath, Encoding.Default)[0];
password = password.Replace("\0", string.Empty);
X509Certificate2 cert = new X509Certificate2(certificateFilePath, password, X509KeyStorageFlags.MachineKeySet | X509KeyStorageFlags.PersistKeySet);
store.Open(OpenFlags.ReadWrite);
store.Add(cert);
store.Close();
```
此 PFX 憑證可用於驗證應用程式或服務，也可以用來保護與其他服務的通訊。 根據預設，檔案只會列入系統的 ACL。 您可以視服務的需求，將它列入其他帳戶的 ACL。

若要了解下一個步驟，請閱讀下列文章：

* [將 Windows 容器部署至 Windows Server 2016 上的 Service Fabric](service-fabric-get-started-containers.md)
* [將 Docker 容器部署至 Linux 上的 Service Fabric](service-fabric-get-started-containers-linux.md)
---
title: 在 Azure 中的 Service Fabric 上建立 Windows 容器應用程式
description: 在本教學課程中，您會在 Azure Service Fabric 上建立第一個 Windows 容器應用程式。
ms.topic: quickstart
ms.date: 07/10/2019
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 477d47fabc59c5718c449418f225d6a38838b270
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "75466277"
---
# <a name="quickstart-deploy-windows-containers-to-service-fabric"></a>快速入門：將 Windows 容器部署至 Service Fabric

Azure Service Fabric 是一個分散式系統平台，可讓您部署及管理可調整和可信賴的微服務與容器。

在 Service Fabric 叢集上的 Windows 容器中執行現有的應用程式，不需要變更您的應用程式。 本快速入門示範如何在 Service Fabric 應用程式中部署預先建立的 Docker 容器映像。 當您完成時，您會有執行中的 Windows Server Core 2016 Server 和 IIS 容器。 本快速入門會說明如何部署 Windows 容器。 請閱讀[本快速入門](service-fabric-quickstart-containers-linux.md)來部署 Linux 容器。

![IIS 預設網頁][iis-default]

在此快速入門中，您將了解如何：

* 封裝 Docker 映像容器
* 設定通訊
* 建置及封裝 Service Fabric 應用程式
* 將容器應用程式部署至 Azure


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶 (您可以建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))。
* 執行下列項目的開發電腦︰
  * Visual Studio 2019 或 Windows 2019。
  * [Service Fabric SDK 和工具](service-fabric-get-started.md)。

## <a name="package-a-docker-image-container-with-visual-studio"></a>使用 Visual Studio 封裝 Docker 映像容器

Service Fabric SDK 和工具會提供一個服務範本，協助您將容器部署到 Service Fabric 叢集。

以「系統管理員」身分啟動 Visual Studio。  選取 [File] \(檔案\)   >  [New] \(新增\)   >  [Project] \(專案\)  。

選取 [Service Fabric 應用程式]  、將它命名為 "MyFirstContainer"，然後按一下 [建立]  。

從 [裝載的容器和應用程式]  範本選取 [容器]  。

在 [映像名稱]  中，輸入 "mcr.microsoft.com/windows/servercore/iis:windowsservercore-ltsc2016"、[Windows Server Core Server 和 IIS 基底映像](https://hub.docker.com/_/microsoft-windows-servercore-iis)。

設定容器連接埠對主機的連接埠對應，以便讓通訊埠 80 上服務的連入要求對應到容器上的連接埠 80。  將 [容器連接埠]  設定為 "80"，並將 [主機連接埠]  設定為 "80"。  

將您的服務命名為 "MyContainerService"，然後按一下 [確定]  。

![[新增服務] 對話方塊][new-service]

## <a name="specify-the-os-build-for-your-container-image"></a>指定容器映像的作業系統組建

使用特定 Windows Server 版本所建置的容器，不能在執行不同 Windows Server 版本的主機上執行。 例如，使用 Windows Server 1709 版本所建置的容器，無法在執行 Windows Server 2016 的主機上執行。 若要深入了解，請參閱 [Windows Server 容器作業系統和主機作業系統的相容性](service-fabric-get-started-containers.md#windows-server-container-os-and-host-os-compatibility)。 

使用 6.1 版及更新版本的 Service Fabric 執行階段時，您可以為每個容器指定多個作業系統映像，並為每個映像標上其所要部署到的作業系統組建版本。 這有助於確保應用程式會在執行不同 Windows 作業系統版本的主機上執行。 若要深入了解，請參閱[指定作業系統組建專屬的容器映像](service-fabric-get-started-containers.md#specify-os-build-specific-container-images)。 

Microsoft 針對建置於不同 Windows Server 版本的 IIS 版本，發行了不同映像。 若要確定 Service Fabric 所部署的容器，會與應用程式部署所在叢集節點上所執行的 Windows Server 版本相容，請在 ApplicationManifest.xml  檔案中新增下列幾行。 Windows Server 2016 的組建版本為 14393，而 Windows Server 1709 版本的組建版本為 16299。

```xml
    <ContainerHostPolicies CodePackageRef="Code"> 
      <ImageOverrides> 
        ...
          <Image Name="mcr.microsoft.com/windows/servercore/iis:windowsservercore-1803" /> 
          <Image Name= "mcr.microsoft.com/windows/servercore/iis:windowsservercore-ltsc2016" Os="14393" /> 
          <Image Name="mcr.microsoft.com/windows/servercore/iis:windowsservercore-1709" Os="16299" /> 
      </ImageOverrides> 
    </ContainerHostPolicies> 
```

服務資訊清單會繼續只為 nanoserver `mcr.microsoft.com/windows/servercore/iis:windowsservercore-ltsc2016` 指定一個映像。

此外，在 *ApplicationManifest.xml* 檔案中，將 **PasswordEncrypted** 變更為 **false**。 對於 Docker Hub 上的公用容器映像，帳戶和密碼都是空白的，所以我們會關閉加密功能，因為加密空白密碼將會產生建置錯誤。

```xml
<RepositoryCredentials AccountName="" Password="" PasswordEncrypted="false" />
```

## <a name="create-a-cluster"></a>建立叢集

以下範例指令碼會建立一個使用 X.509 憑證保護的五節點 Service Fabric 叢集。 此命令會建立自我簽署的憑證，並將它上傳到新的金鑰保存庫。 憑證也會複製到本機目錄。 您可以在[建立 Service Fabric 叢集](scripts/service-fabric-powershell-create-secure-cluster-cert.md)中，深入了解如何使用這個指令碼建立叢集。

您可以視需要使用 [Azure PowerShell 指南](/powershell/azure/overview)中的指示來安裝 Azure PowerShell。

執行以下程式碼之前，請在 PowerShell 中執行 `Connect-AzAccount` 來建立與 Azure 的連線。

將以下指令碼複製到剪貼簿，然後開啟 [Windows PowerShell ISE]  。  將內容貼到空的 Untitled1.ps1 視窗中。 然後提供指令碼中的變數值：`subscriptionId`、`certpwd`、`certfolder`、`adminuser`、`adminpwd` 等等。  您為 `certfolder` 指定的目錄必須先存在，您才能執行指令碼。

[!code-powershell[main](../../powershell_scripts/service-fabric/create-secure-cluster/create-secure-cluster.ps1 "Create a Service Fabric cluster")]

針對這些變數提供您的值，按 **F5** 來執行指令碼。

執行指令碼並建立叢集之後，在輸出中尋找 `ClusterEndpoint`。 例如：

```powershell
...
ClusterEndpoint : https://southcentralus.servicefabric.azure.com/runtime/clusters/b76e757d-0b97-4037-a184-9046a7c818c0
```

### <a name="install-the-certificate-for-the-cluster"></a>安裝叢集的憑證

現在，我們將在 CurrentUser\My  憑證存放區中安裝 PFX。 PFX 檔案會位於您使用上述 PowerShell 指令碼中的 `certfolder` 環境變數所指定的目錄。

切換至該目錄，然後執行下列 PowerShell 命令，取代您 `certfolder` 目錄中的 PFX 檔案名稱，以及您在 `certpwd` 變數中指定的密碼。 在此範例中，目前的目錄設定為 PowerShell 指令碼中 `certfolder` 變數所指定的目錄。 `Import-PfxCertificate` 命令會從該處執行：

```powershell
PS C:\mycertificates> Import-PfxCertificate -FilePath .\mysfclustergroup20190130193456.pfx -CertStoreLocation Cert:\CurrentUser\My -Password (ConvertTo-SecureString Password#1234 -AsPlainText -Force)
```

此命令會傳回指紋：

```powershell
  ...
  PSParentPath: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

Thumbprint                                Subject
----------                                -------
0AC30A2FA770BEF566226CFCF75A6515D73FC686  CN=mysfcluster.SouthCentralUS.cloudapp.azure.com
```

請記住指紋值以在後續步驟中使用。

## <a name="deploy-the-application-to-azure-using-visual-studio"></a>使用 Visual Studio 將應用程式部署至 Azure

應用程式備妥後，即可直接從 Visual Studio 將其部署到叢集。

以滑鼠右鍵按一下 [方案總管] 中的 **MyFirstContainer**，並選擇 [發佈]  。 [發行] 對話方塊隨即出現。

當您執行上述 **命令時，在 PowerShell 視窗中複製**CN =`Import-PfxCertificate` 後面的內容，並在其中新增連接埠 `19000`。 例如： `mysfcluster.SouthCentralUS.cloudapp.azure.com:19000` 。 將它複製到 [連線端點]  欄位中。 請記住這個值，因為您會在之後的步驟中用到該值。

按一下 [進階連線參數]  並確認連線參數資訊。  *FindValue* 和 *ServerCertThumbprint* 值必須符合您在前一個步驟中執行 `Import-PfxCertificate` 時所安裝的憑證指紋。

![[發佈] 對話方塊](./media/service-fabric-quickstart-containers/publish-app.png)

按一下 [發佈]  。

叢集中的每個應用程式都必須有一個唯一的名稱。 如果有名稱衝突，請重新命名 Visual Studio 專案，然後重新部署。

開啟瀏覽器並瀏覽至您在前一個步驟中放入 [連線端點]  欄位中的位址。 您可以選擇性地在 URL 前面加上配置識別碼 `http://`，並將連接埠 `:80` 附加到 URL。 例如，http:\//mysfcluster.SouthCentralUS.cloudapp.azure.com:80。

 您應會看到 IIS 預設網頁：![IIS 預設網頁][iis-default]

## <a name="clean-up"></a>清除

當叢集在執行時，您需要繼續支付費用。 請考慮[刪除您的叢集](service-fabric-cluster-delete.md)。

## <a name="next-steps"></a>後續步驟

在此快速入門中，您已了解如何：

* 封裝 Docker 映像容器
* 設定通訊
* 建置及封裝 Service Fabric 應用程式
* 將容器應用程式部署至 Azure

若要深入了解如何在 Service Fabric 中使用 Windows 容器，請繼續進行教學課程以了解 Windows 容器應用程式。

> [!div class="nextstepaction"]
> [建立 Windows 容器應用程式](./service-fabric-host-app-in-a-container.md)

[iis-default]: ./media/service-fabric-quickstart-containers/iis-default.png
[publish-dialog]: ./media/service-fabric-quickstart-containers/publish-dialog.png
[new-service]: ./media/service-fabric-quickstart-containers/NewService.png

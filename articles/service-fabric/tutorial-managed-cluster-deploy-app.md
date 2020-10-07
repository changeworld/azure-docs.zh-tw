---
title: 透過 PowerShell 將應用程式部署到 Service Fabric 受控叢集 (預覽)
description: 在本教學課程中，您將連線到 Service Fabric 受控叢集，並透過 PowerShell 部署應用程式。
ms.topic: tutorial
ms.date: 09/28/2020
ms.openlocfilehash: 36a91d2852bcda5f958441b48ef4721d6ccc83c4
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/28/2020
ms.locfileid: "91410202"
---
# <a name="tutorial-deploy-an-app-to-a-service-fabric-managed-cluster-preview"></a>教學課程：將應用程式部署到 Service Fabric 受控叢集 (預覽)

在本教學課程系列中，我們將討論：

> [!div class="checklist"]
> * [如何部署 Service Fabric 受控叢集](tutorial-managed-cluster-deploy.md)
> * [如何擴增 Service Fabric 受控叢集](tutorial-managed-cluster-scale.md)
> * [如何新增及移除 Service Fabric 受控叢集中的節點](tutorial-managed-cluster-add-remove-node-type.md)
> * 如何將應用程式部署到 Service Fabric 受控叢集

本系列的這個部分涵蓋如何：

> [!div class="checklist"]
> * 連線到您的 Service Fabric 受控叢集
> * 將應用程式上傳到叢集
> * 在叢集中具現化應用程式
> * 從叢集移除應用程式

## <a name="prerequisites"></a>必要條件

* Service Fabric 受控叢集 (請參閱[部署受控叢集](tutorial-managed-cluster-deploy.md))。

## <a name="connect-to-your-cluster"></a>連線至您的叢集

若要連線到您的叢集，您將需要叢集憑證指紋。 您可以在資源部署的叢集屬性輸出中，或藉由查詢現有資源上的叢集屬性，來尋找這個值。

下列命令可以用來查詢叢集憑證指紋的叢集資源。

```powershell
$serverThumbprint = (Get-AzResource -ResourceId /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ServiceFabric/managedclusters/mysfcluster).Properties.clusterCertificateThumbprint
```

透過叢集憑證指紋，您就可以連線到您的叢集。

```powershell
$connectionEndpoint = "mysfcluster.eastus2.cloudapp.azure.com:19000"
Connect-ServiceFabricCluster -ConnectionEndpoint $connectionEndpoint -KeepAliveIntervalInSec 10 `
      -X509Credential `
      -ServerCertThumbprint $serverThumbprint  `
      -FindType FindByThumbprint `
      -FindValue $clientThumbprint `
      -StoreLocation CurrentUser `
      -StoreName My

```

### <a name="upload-an-application-package"></a>上傳應用程式套件

在本教學課程中，我們將使用 [Service Fabric 投票應用程式](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/voting-sample-no-reverse-proxy)範例。 如需透過 PowerShell 部署 Service Fabric 應用程式的詳細資料，請參閱 [Service Fabric 部署及移除應用程式](service-fabric-deploy-remove-applications.md)。

> [!NOTE]
> 在 Service Fabric 受控叢集預覽中，您將無法直接從 Visual Studio 發佈應用程式。

您必須先[封裝應用程式以進行部署](service-fabric-package-apps.md)。 在本教學課程中，請遵循從 Visual Studio 內封裝應用程式的步驟。 請務必記下已封裝應用程式的路徑，因為其將用於下列路徑。

建立應用程式封裝之後，您就可以將應用程式封裝上傳到您的叢集。 更新 `$path` 值，以代表應用程式封裝所在的路徑，然後執行下列動作：

```powershell
$path = "C:\Users\<user>\Documents\service-fabric-dotnet-quickstart\Voting\pkg\Debug"
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -CompressPackage
Register-ServiceFabricApplicationType -ApplicationPathInImageStore Debug
```

### <a name="create-an-application"></a>建立應用程式

您可以使用 New-ServiceFabricApplication Cmdlet，從任何已成功註冊的應用程式類型版本，將應用程式具現化。 每個應用程式名稱的開頭必須為 "fabric:" 配置，而且必須是每個應用程式執行個體的唯一名稱。 如果已在目標應用程式類型的應用程式資訊清單中定義預設服務，也會一併建立這些服務。

```powershell
New-ServiceFabricApplication fabric:/Voting VotingType 1.0.0
```

此作業完成後，您應該會看到應用程式執行個體正在 Service Fabric Explorer 中執行。

### <a name="remove-an-application"></a>移除應用程式

當不再需要應用程式執行個體時，您可以使用 `Remove-ServiceFabricApplication` Cmdlet，依名稱將其永久移除，這也會自動移除屬於該應用程式的所有服務，並永久移除所有服務狀態。

```powershell
Remove-ServiceFabricApplication fabric:/Voting
```

## <a name="next-steps"></a>後續步驟

在此步驟中，我們已將應用程式部署到 Service Fabric 受控叢集。 若要深入了解 Service Fabric 受控叢集，請參閱：

> [!div class="nextstepaction"]
> [Service Fabric 受控叢集常見問題集](faq-managed-cluster.md)

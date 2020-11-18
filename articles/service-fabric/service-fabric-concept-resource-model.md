---
title: Azure Service Fabric 應用程式資源模型
description: 本文概述如何使用 Azure Resource Manager 來管理 Azure Service Fabric 應用程式。
ms.topic: conceptual
ms.date: 10/21/2019
ms.custom: sfrev
ms.openlocfilehash: 3968fde0222dcee8047e7490dba78879ab6110e2
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94681681"
---
# <a name="service-fabric-application-resource-model"></a>Service Fabric 應用程式資源模型

您有多個選項可在 Service Fabric 叢集上部署 Azure Service Fabric 應用程式。 我們建議使用 Azure Resource Manager。 如果您使用 Resource Manager，您可以在 JSON 中描述應用程式和服務，然後將它們部署在與叢集相同的 Resource Manager 範本中。 不同于使用 PowerShell 或 Azure CLI 來部署和管理應用程式，如果您使用 Resource Manager，則不需要等待叢集準備就緒;應用程式註冊、布建和部署都可以在一個步驟中進行。 使用 Resource Manager 是在您的叢集中管理應用程式生命週期的最佳方式。 如需詳細資訊，請參閱 [最佳做法：基礎結構即程式碼](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources)。

以 Resource Manager 中的資源來管理您的應用程式，可協助您在這些方面獲得改進：

* 審核記錄： Resource Manager 審核每個作業，並保留詳細的活動記錄。 活動記錄可協助您追蹤對應用程式所做的任何變更，以及對叢集所做的任何變更。
* 角色型存取控制：您可以使用相同的 Resource Manager 範本，管理叢集的存取權，以及部署在叢集上的應用程式。
* 管理效率：使用 Resource Manager 可提供單一位置 (Azure 入口網站的) ，以管理您的叢集和重要的應用程式部署。

在本檔中，您將瞭解如何：

> [!div class="checklist"]
>
> * 使用 Resource Manager 部署應用程式資源。
> * 使用 Resource Manager 升級應用程式資源。
> * 刪除應用程式資源。

## <a name="deploy-application-resources"></a>部署應用程式資源

使用 Resource Manager 應用程式資源模型來部署應用程式和其服務所需的高階步驟如下：
1. 封裝應用程式程式碼。
1. 上傳套件。
1. 以應用程式資源的形式，參考封裝在 Resource Manager 範本中的位置。 

如需詳細資訊，請參閱 [封裝應用程式](service-fabric-package-apps.md#create-an-sfpkg)。

然後，您會建立 Resource Manager 範本、使用應用程式詳細資料來更新參數檔案，以及在 Service Fabric 叢集上部署範本。 [探索範例](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM)。

### <a name="create-a-storage-account"></a>建立儲存體帳戶

若要從 Resource Manager 範本部署應用程式，您必須有儲存體帳戶。 儲存體帳戶是用來暫存應用程式映射。 

您可以重複使用現有的儲存體帳戶，也可以建立新的儲存體帳戶來暫存您的應用程式。 如果您使用現有的儲存體帳戶，您可以略過此步驟。 

![建立儲存體帳戶][CreateStorageAccount]

### <a name="configure-your-storage-account"></a>設定您的儲存體帳戶

建立儲存體帳戶之後，您可以建立可暫存應用程式的 blob 容器。 在 Azure 入口網站中，移至您要用來儲存應用程式的 Azure 儲存體帳戶。 選取 [ **blob**  >  **新增容器**]。 

您可以藉由將公用存取層級設定為 [ **私人**] 來保護叢集中的資源。 您可以透過多種方式授與存取權：

* 使用 [Azure Active Directory](../storage/common/storage-auth-aad-app.md)來授權 blob 和佇列的存取權。
* [在 Azure 入口網站中使用 AZURE RBAC](../storage/common/storage-auth-aad-rbac-portal.md)來授與 azure blob 和佇列資料的存取權。
* 使用 [共用存取](/rest/api/storageservices/delegate-access-with-shared-access-signature)簽章來委派存取權。

下列螢幕擷取畫面中的範例會使用 blob 的匿名讀取權限。

![建立 Blob][CreateBlob]

### <a name="stage-the-application-in-your-storage-account"></a>在您的儲存體帳戶中暫存應用程式

在您可以部署應用程式之前，您必須先將應用程式暫存在 blob 儲存體中。 在本教學課程中，我們會手動建立應用程式套件。 請記住，這個步驟可以自動化。 如需詳細資訊，請參閱 [封裝應用程式](service-fabric-package-apps.md#create-an-sfpkg)。 

在本教學課程中，我們使用 [投票範例應用程式](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart)。

1. 在 Visual Studio 中，以滑鼠右鍵按一下 **投票** 專案，然後選取 [ **封裝**]。

   ![封裝應用程式][PackageApplication]  
1. 移至 *.\service-fabric-dotnet-quickstart\Voting\pkg\Debug* 目錄。 將內容壓縮成名為 *Voting.zip* 的檔案。 *ApplicationManifest.xml* 檔案應位於 zip 檔案中的根目錄。

   ![Zip 應用程式][ZipApplication]  
1. 將檔案重新命名，以將副檔名從 .zip 變更為 *sfpkg*。

1. 在 [Azure 入口網站] 中，于儲存體帳戶的 [ **應用程式** ] 容器中選取 **[上傳**]，然後上傳 **sfpkg**。 

   ![上傳應用程式套件][UploadAppPkg]

現在，應用程式已暫存，您可以建立 Resource Manager 範本以部署應用程式。

### <a name="create-the-resource-manager-template"></a>建立 Resource Manager 範本

範例應用程式包含可用來部署應用程式 [Azure Resource Manager 範本](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM) 。 範本檔案名 *UserApp.js開啟* 並 *UserApp.Parameters.js*。

> [!NOTE]
> 檔案 *上的UserApp.Parameters.js* 必須以您的叢集名稱更新。
>
>

| 參數              | 描述                                 | 範例                                                      | 註解                                                     |
| ---------------------- | ------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| clusterName            | 您正在部署的叢集名稱 | sf-cluster123                                                |                                                              |
| 應用程式            | 應用程式的名稱                 | 投票                                                       |
| applicationTypeName    | 應用程式的類型名稱           | >votingtype                                                   | 必須符合 ApplicationManifest.xml                 |
| >applicationtypeversion | 應用程式類型的版本         | 1.0.0                                                        | 必須符合 ApplicationManifest.xml                 |
| serviceName            | 服務的名稱         | 投票 ~ VotingWeb                                             | 必須是 ApplicationName ~ ServiceType 的格式            |
| serviceTypeName        | 服務的類型名稱                | VotingWeb                                                    | 必須符合 ServiceManifest.xml                 |
| appPackageUrl          | 應用程式的 blob 儲存體 URL     | HTTPs： \/ /servicefabricapps.blob.core.windows.net/apps/Voting.sfpkg | 在 blob 儲存體中，應用程式套件的 URL (設定 URL 的程式，將于本文稍後說明)  |

```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

### <a name="deploy-the-application"></a>部署應用程式

執行 **>new-azresourcegroupdeployment** 指令程式，將應用程式部署至包含叢集的資源群組：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "sf-cluster-rg" -TemplateParameterFile ".\UserApp.Parameters.json" -TemplateFile ".\UserApp.json" -Verbose
```

## <a name="upgrade-the-service-fabric-application-by-using-resource-manager"></a>使用 Resource Manager 升級 Service Fabric 應用程式

基於下列其中一個原因，您可能會將已部署的應用程式升級為 Service Fabric 叢集：

* 新的服務會新增至應用程式。 將服務新增至應用程式時，必須將服務定義加入 *service-manifest.xml* 和 *application-manifest.xml* 檔案中。 若要反映新版本的應用程式，您也必須將 [UserApp.Parameters.js](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/blob/master/ARM/UserApp.Parameters.json)中的應用程式類型版本從1.0.0 變更為1.0.1：

    ```json
    "applicationTypeVersion": {
        "value": "1.0.1"
    },
    "serviceName2": {
        "value": "Voting~VotingData"
    },
    "serviceTypeName2": {
        "value": "VotingDataType"
    }
    ```

* 現有服務的新版本會新增至應用程式。 範例包括應用程式的程式碼變更，以及應用程式類型版本和名稱的更新。 針對此升級，請更新 UserApp.Parameters.js，如下所示：

    ```json
     "applicationTypeVersion": {
        "value": "1.0.1"
    },
    ```

## <a name="delete-application-resources"></a>刪除應用程式資源

若要刪除使用 Resource Manager 中的應用程式資源模型所部署的應用程式：

1. 使用 [get-azresource](/powershell/module/az.resources/get-azresource?view=azps-2.5.0) Cmdlet 取得應用程式的資源識別碼：

    ```powershell
    Get-AzResource  -Name <String> | f1
    ```

1. 使用 [get-azresource](/powershell/module/az.resources/remove-azresource?view=azps-2.5.0) Cmdlet 來刪除應用程式資源：

    ```powershell
    Remove-AzResource  -ResourceId <String> [-Force] [-ApiVersion <String>]
    ```

## <a name="next-steps"></a>後續步驟

取得應用程式資源模型的相關資訊：

* [在 Service Fabric 中模型化應用程式](service-fabric-application-model.md)
* [Service Fabric 應用程式和服務資訊清單](service-fabric-application-and-service-manifests.md)
* [最佳做法：基礎結構即程式碼](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources)
* [以 Azure 資源的形式管理應用程式和服務](service-fabric-best-practices-infrastructure-as-code.md)


<!--Image references-->
[CreateStorageAccount]: ./media/service-fabric-application-model/create-storage-account.png
[CreateBlob]: ./media/service-fabric-application-model/create-blob.png
[PackageApplication]: ./media/service-fabric-application-model/package-application.png
[ZipApplication]: ./media/service-fabric-application-model/zip-application.png
[UploadAppPkg]: ./media/service-fabric-application-model/upload-app-pkg.png

---
title: Azure 服務結構應用程式資源模型
description: 本文概述了使用 Azure 資源管理員管理 Azure 服務結構應用程式。
ms.topic: conceptual
ms.date: 10/21/2019
ms.custom: sfrev
ms.openlocfilehash: 7a9f59e3e44d3302ac19c7a9e7e77beb51947ce4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682635"
---
# <a name="service-fabric-application-resource-model"></a>服務結構應用程式資源模型

在 Service Fabric 群集上部署 Azure 服務結構應用程式有多個選項。 我們建議使用 Azure 資源管理員。 如果使用資源管理員,則可以在 JSON 中描述應用程式和服務,然後將它們部署在群集相同的資源管理器範本中。 與使用 PowerShell 或 Azure CLI 部署和管理應用程式不同,如果使用資源管理器,則不必等待群集準備就緒;因此,無需等待群集準備就緒。應用程式註冊、預配和部署都可以在一個步驟中發生。 使用資源管理員是管理群集中應用程式生命週期的最佳方式。 有關詳細資訊,請參閱[最佳實務:基礎結構作為代碼](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources)。

將應用程式作為資源管理員中的資源管理可説明您在以下方面獲得改進:

* 審核追蹤:資源管理器審核每個操作並保留詳細的活動日誌。 活動日誌可以説明您追蹤對應用程式和群集所做的任何更改。
* 基於角色的存取控制:您可以使用同一資源管理器範本管理對群集和群集上部署的應用程式的訪問。
* 管理效率:使用資源管理器為您提供一個位置(Azure 門戶),用於管理群集和關鍵應用程式部署。

在本文中,您將學習如何:

> [!div class="checklist"]
>
> * 使用資源管理員部署應用程式資源。
> * 使用資源管理員升級應用程式資源。
> * 刪除應用程式資源。

## <a name="deploy-application-resources"></a>部署應用程式資源

使用資源管理員應用程式資源管理員資源管理員模型部署應用程式及其服務所採用的進階步驟包括:
1. 打包應用程式代碼。
1. 上傳包。
1. 將包在資源管理器範本中的位置作為應用程式資源引用。 

有關詳細資訊,請查看[包裝應用程式](service-fabric-package-apps.md#create-an-sfpkg)。

然後,創建資源管理器範本,使用應用程式詳細資訊更新參數檔,並在 Service Fabric 群集上部署該範本。 [探索範例](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM)。

### <a name="create-a-storage-account"></a>建立儲存體帳戶

要從資源管理器範本部署應用程式,您必須具有存儲帳戶。 存儲帳戶用於暫存應用程式映射。 

您可以重用現有存儲帳戶,也可以創建新存儲帳戶來暫存應用程式。 如果使用現有存儲帳戶,則可以跳過此步驟。 

![建立儲存體帳戶][CreateStorageAccount]

### <a name="configure-your-storage-account"></a>設定儲存帳戶

創建存儲帳戶後,將創建一個Blob容器,其中可以暫存應用程式。 在 Azure 門戶中,轉到要存儲應用程式的 Azure 存儲帳戶。 選擇**Blob** > **新增容器**。 

可以通過將公共訪問級別設置為**私有**,保護群集中的資源。 您可以通過多種方式授予存取權限:

* 使用[Azure 活動目錄](../storage/common/storage-auth-aad-app.md)授權存取 Blob 和佇列。
* 通過在[Azure 門戶中使用 RBAC](../storage/common/storage-auth-aad-rbac-portal.md)來授予對 Azure blob 和佇列數據的訪問。
* 使用[共享訪問簽名](https://docs.microsoft.com/rest/api/storageservices/delegate-access-with-shared-access-signature)委派訪問許可權。

以下螢幕截圖中的示例使用 blob 的匿名讀取訪問。

![建立 Blob][CreateBlob]

### <a name="stage-the-application-in-your-storage-account"></a>在儲存帳戶中暫存應用程式

在部署應用程式之前,必須在 Blob 儲存中暫存應用程式。 在本教程中,我們手動創建應用程式包。 請記住,此步驟可以自動執行。 有關詳細資訊,請參閱[打包應用程式](service-fabric-package-apps.md#create-an-sfpkg)。 

在本教學中,我們使用[投票範例應用程式](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart)。

1. 在可視化工作室中,右鍵單擊**投票**專案,然後選擇 **「包**」。。

   ![套件應用程式][PackageApplication]  
1. 轉到 *._服務-結構-點-快速入門\投票\pkg_調試*目錄。 將內容壓縮到名為 *"投票.zip"* 的檔案中。 *應用程式清單.xml*檔案應位於 zip 檔案中的根目錄。

   ![郵編應用程式][ZipApplication]  
1. 重新命名檔案以將副檔名從 .zip 更改為 *.sfpkg*。

1. 在 Azure 門戶中,在儲存帳戶**的應用**容器中,選擇 **「上載**」,然後上傳**投票.sfpkg**。 

   ![上傳應用程式][UploadAppPkg]

現在,應用程式現在暫存,您可以創建資源管理器範本來部署應用程式。

### <a name="create-the-resource-manager-template"></a>建立 Resource Manager 範本

範例應用程式包含可用於部署應用程式的[Azure 資源管理員樣本](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM)。 樣本檔名稱是*UserApp.json*和*UserApp.parameter.json*。

> [!NOTE]
> 必須使用群集的名稱更新*UserApp.parameters.json*檔。
>
>

| 參數              | 描述                                 | 範例                                                      | 註解                                                     |
| ---------------------- | ------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| clusterName            | 要部署到的群組的名稱 | sf-群集123                                                |                                                              |
| 應用程式            | 應用程式的名稱                 | 投票                                                       |
| 應用程式類型名稱    | 應用程式的類型名稱           | 投票類型                                                   | 必須符合應用程式清單.xml                 |
| 應用程式類型版本 | 應用程式類型的版本         | 1.0.0                                                        | 必須符合應用程式清單.xml                 |
| serviceName            | 服務的名稱         | 投票_投票網路                                             | 必須採用應用程式名稱_服務類型格式            |
| 服務類型名稱        | 服務的類型名稱                | VotingWeb                                                    | 必須符合 ServiceManifest.xml                 |
| 應用程式套件網址          | 應用程式的 blob 存放 URL     | Https:\//servicefabricapps.blob.core.windows.net/apps/Voting.sfpkg | blob 儲存中應用程式套件的網址(本文稍後將介紹設定網址的過程) |

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

執行**New-AzResourceGroup 部署**cmdlet 以將應用程式部署到包含群組的資源群組:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "sf-cluster-rg" -TemplateParameterFile ".\UserApp.Parameters.json" -TemplateFile ".\UserApp.json" -Verbose
```

## <a name="upgrade-the-service-fabric-application-by-using-resource-manager"></a>使用資源管理員升級服務結構應用程式

出於以下原因之一,可以升級已部署到 Service Fabric 叢集的應用程式:

* 新服務將添加到應用程式中。 將服務添加到應用程式時,必須將服務定義添加到*服務-清單.xml*和*應用程式-清單.xml*檔中。 要反映應用程式的新版本,還必須在[UserApp.parameter.json](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/blob/master/ARM/UserApp.Parameters.json)中將應用程式類型版本從 1.0.0 更改為 1.0.1:

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

* 現有服務的新版本將添加到應用程式中。 範例包括應用程式代碼更改和應用程式類型版本和名稱的更新。 對於此升級,請更新 UserApp.parameters.json,如下所示:

    ```json
     "applicationTypeVersion": {
        "value": "1.0.1"
    },
    ```

## <a name="delete-application-resources"></a>移除應用程式資源

要刪除在資源管理員中使用應用程式資源模型部署的應用程式,請執行以下規定:

1. 使用[Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource?view=azps-2.5.0) cmdlet 取得應用程式的資源代碼:

    ```powershell
    Get-AzResource  -Name <String> | f1
    ```

1. 使用['移除-AzResource](https://docs.microsoft.com/powershell/module/az.resources/remove-azresource?view=azps-2.5.0) cmdlet'移除應用程式資源:

    ```powershell
    Remove-AzResource  -ResourceId <String> [-Force] [-ApiVersion <String>]
    ```

## <a name="next-steps"></a>後續步驟

取得有關應用程式資源模型的資訊:

* [在 Service Fabric 中模型化應用程式](service-fabric-application-model.md)
* [Service Fabric 應用程式和服務資訊清單](service-fabric-application-and-service-manifests.md)
* [最佳實作:基礎架構作為代碼](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources)
* [將應用程式和服務作為 Azure 資源管理](service-fabric-best-practices-infrastructure-as-code.md)


<!--Image references-->
[CreateStorageAccount]: ./media/service-fabric-application-model/create-storage-account.png
[CreateBlob]: ./media/service-fabric-application-model/create-blob.png
[PackageApplication]: ./media/service-fabric-application-model/package-application.png
[ZipApplication]: ./media/service-fabric-application-model/zip-application.png
[UploadAppPkg]: ./media/service-fabric-application-model/upload-app-pkg.png

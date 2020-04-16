---
title: 使用系統配置的 MI 部署服務結構應用
description: 本文介紹如何將系統分配的託管識別分配給 Azure 服務結構應用程式
ms.topic: article
ms.date: 07/25/2019
ms.openlocfilehash: c5c7a17c51eee18d9b7276f2c57289a5de5c8181
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415645"
---
# <a name="deploy-service-fabric-application-with-system-assigned-managed-identity"></a>使用系統配置的託管識別部署服務結構應用程式

為了訪問 Azure 服務結構應用程式的託管標識功能,必須首先在群集上啟用託管標識權杖服務。 此服務負責使用其託管標識對 Service Fabric 應用程式進行身份驗證,並代表它們獲取訪問權杖。 啟用服務後,您可以在左側窗格中的 **「系統**」部分下的「服務結構資源管理器」中看到它,該功能在其他系統服務旁邊的名稱**結構:/系統/託管身份令牌服務**下運行。

> [!NOTE] 
> 支援從 API`"2019-06-01-preview"`版本 開始部署具有託管標識的服務結構應用程式。 您還可以對應用程式類型、應用程式類型版本和服務資源使用相同的 API 版本。 受支援的最小服務交換矩陣運行時為6.5 CU2。 在附加項目中,產生/包環境還應具有 CU2 或更高版本中的 SF .Net SDK

## <a name="system-assigned-managed-identity"></a>系統指派的受控識別

### <a name="application-template"></a>應用程式範本

要啟用具有系統分配的託管標識的應用程式,請將**識別**屬性添加到應用程式資源,其中類型**系統分配**如下示例所示:

```json
    {
      "apiVersion": "2019-06-01-preview",
      "type": "Microsoft.ServiceFabric/clusters/applications",
      "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]"
      ],
      "identity": {
        "type" : "systemAssigned"
      },
      "properties": {
        "typeName": "[parameters('applicationTypeName')]",
        "typeVersion": "[parameters('applicationTypeVersion')]",
        "parameters": {
        }
      }
    }
```
此屬性聲明(對 Azure 資源管理員以及託管標識和服務結構資源提供程式分別聲明此資源應具有隱式`system assigned`( ) 託管標識。

### <a name="application-and-service-package"></a>應用程式和服務套件

1. 更新應用程式清單以在 **「主體」** 部分中添加**託管標識**元素,其中包含單個條目,如下所示:

    **ApplicationManifest.xml**

    ```xml
    <Principals>
      <ManagedIdentities>
        <ManagedIdentity Name="SystemAssigned" />
      </ManagedIdentities>
    </Principals>
    ```
    這將作為資源分配給應用程式的標識映射到友好名稱,以便進一步分配給包含應用程式的服務。 

2. 在與正在分配託管標識的服務對應**的服務清單導入**部分中,添加**標識綁定策略**元素,如下所示:

    **ApplicationManifest.xml**

      ```xml
        <ServiceManifestImport>
          <Policies>
            <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="SystemAssigned" />
          </Policies>
        </ServiceManifestImport>
      ```

    此元素將應用程式的標識分配給服務;如果沒有此分配,服務將無法訪問應用程式的標識。 在上面的代碼段中,`SystemAssigned`標識(這是一個保留關鍵字)在友好`WebAdmin`名稱 下映射到服務的定義。

3. 更新服務清單以在 **「資源」** 部分中加入**託管識別**元素,其名稱與`IdentityBindingPolicy`應用程式`ServiceIdentityRef`清單中 定義中的設定值匹配:

    **ServiceManifest.xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```
    這是標識與服務的等效映射,但從服務定義的角度來看。 此處引用標識的友好名稱`WebAdmin`( , 如應用程式清單中聲明的那樣。

## <a name="next-steps"></a>後續步驟
* 檢視 Azure 服務結構中的[託管識別支援](./concepts-managed-identity.md)
* [部署新](./configure-new-azure-service-fabric-enable-managed-identity.md)具有託管識別支援的 Azure 服務結構叢集 
* 在現有 Azure 服務結構群集中[啟用託管識別](./configure-existing-cluster-enable-managed-identity-token-service.md)
* 利用服務結構應用程式的[管理員(來自原始碼代碼](./how-to-managed-identity-service-fabric-app-code.md))
* [使用使用者配置的託管識別部署 Azure 服務結構應用程式](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [授予 Azure 服務結構應用程式對其他 Azure 資源的存取](./how-to-grant-access-other-resources.md)

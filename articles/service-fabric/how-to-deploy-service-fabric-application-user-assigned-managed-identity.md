---
title: 使用使用者分配的託管標識部署應用
description: 本文介紹如何使用使用者分配的託管標識部署 Service Fabric 應用程式
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: a5eeaf0d6420fa36c0a78f7553ddfd82197d8ec4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610330"
---
# <a name="deploy-service-fabric-application-with-a-user-assigned-managed-identity-preview"></a>使用使用者分配的託管標識部署服務結構應用程式（預覽）

要部署具有託管標識的服務結構應用程式，需要通過 Azure 資源管理器（通常使用 Azure 資源管理器範本）部署該應用程式。 有關如何通過 Azure 資源管理器部署 Service Fabric 應用程式的詳細資訊，請參閱[將應用程式和服務管理為 Azure 資源管理器資源](service-fabric-application-arm-resource.md)。

> [!NOTE] 
> 
> 未作為 Azure 資源部署的應用程式**不能**具有託管標識。 
>
> API 版本`"2019-06-01-preview"`支援使用託管標識的服務結構應用程式部署。 您還可以對應用程式類型、應用程式類型版本和服務資源使用相同的 API 版本。
>

## <a name="user-assigned-identity"></a>使用者分配標識

要啟用具有使用者分配標識的應用程式，請先將**標識**屬性添加到具有類型**使用者分配**和引用的使用者分配標識的應用程式資源。 然後在**應用程式**資源**的屬性**部分內添加**託管 Id 實體**部分，其中包含每個使用者分配標識的委託 Id 映射的易記名稱清單。 有關使用者分配標識的詳細資訊，請參閱[創建、列出或刪除使用者分配的託管標識](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell)。

### <a name="application-template"></a>應用程式範本

要啟用具有使用者分配標識的應用程式，請先將**標識**屬性添加到具有類型**使用者分配**和引用使用者分配標識的應用程式資源，然後在**屬性**部分中添加**託管 Id 實體**物件，該部分包含一個易記名稱清單到每個使用者分配的身份的 iid 映射。

    {
      "apiVersion": "2019-06-01-preview",
      "type": "Microsoft.ServiceFabric/clusters/applications",
      "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]",
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]"
      ],
      "identity": {
        "type" : "userAssigned",
        "userAssignedIdentities": {
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]": {}
        }
      },
      "properties": {
        "typeName": "[parameters('applicationTypeName')]",
        "typeVersion": "[parameters('applicationTypeVersion')]",
        "parameters": {
        },
        "managedIdentities": [
          {
            "name" : "[parameters('userAssignedIdentityName')]",
            "principalId" : "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName')), '2018-11-30').principalId]"
          }
        ]
      }
    }

在上面的示例中，使用者分配標識的資源名稱正用作應用程式的託管標識的易記名稱。 以下示例假定實際易記名稱為"AdminUser"。

### <a name="application-package"></a>應用程式套件

1. 對於 Azure 資源管理器範本`managedIdentities`中部分中定義的每個標識，在`<ManagedIdentity>`**"主體"** 部分下的應用程式清單中委任標記。 屬性`Name`需要匹配`name``managedIdentities`節中定義的屬性。

    **ApplicationManifest.xml**

    ```xml
      <Principals>
        <ManagedIdentities>
          <ManagedIdentity Name="AdminUser" />
        </ManagedIdentities>
      </Principals>
    ```

2. 在 **"服務清單導入"** 部分中，為使用託管標識的服務添加**標識繫結原則**。 此策略將`AdminUser`標識映射到以後需要添加到服務清單中的特定于服務的標識名稱。

    **ApplicationManifest.xml**

    ```xml
      <ServiceManifestImport>
        <Policies>
          <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="AdminUser" />
        </Policies>
      </ServiceManifestImport>
    ```

3. 更新服務清單以在 **"資源"** 部分內添加與應用程式清單`ServiceIdentityRef`中的名稱匹配`IdentityBindingPolicy`的**託管標識**：

    **ServiceManifest.xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```

## <a name="next-steps"></a>後續步驟

* [如何在服務結構應用程式代碼中使用託管標識](how-to-managed-identity-service-fabric-app-code.md)
* [如何授予服務結構應用程式對其他 Azure 資源的存取權限](how-to-grant-access-other-resources.md)

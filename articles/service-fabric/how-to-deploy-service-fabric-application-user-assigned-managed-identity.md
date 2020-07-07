---
title: 以使用者指派的受控識別部署應用程式
description: 本文說明如何使用使用者指派的受控識別來部署 Service Fabric 應用程式
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: 9aef81db7a455b72c83cf96898a0c228f1c382fd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81415633"
---
# <a name="deploy-service-fabric-application-with-a-user-assigned-managed-identity"></a>以使用者指派的受控識別部署 Service Fabric 應用程式

若要使用受控識別部署 Service Fabric 應用程式，必須透過 Azure Resource Manager 部署應用程式，通常會使用 Azure Resource Manager 範本。 如需有關如何透過 Azure Resource Manager 部署 Service Fabric 應用程式的詳細資訊，請參閱將[應用程式和服務當做 Azure Resource Manager 資源來管理](service-fabric-application-arm-resource.md)。

> [!NOTE] 
> 
> 未部署為 Azure 資源的應用程式**不能**有受控識別。 
>
> API 版本支援使用受控識別進行 Service Fabric 應用程式部署 `"2019-06-01-preview"` 。 您也可以針對應用程式類型、應用程式類型版本和服務資源使用相同的 API 版本。
>

## <a name="user-assigned-identity"></a>使用者指派的身分識別

若要使用使用者指派的身分識別來啟用應用程式，請先將**identity**屬性新增至**userAssigned**類型的應用程式資源，以及參考的使用者指派的身分識別。 然後在**應用程式**資源的 [**屬性**] 區段內新增**managedIdentities**區段，其中包含每個使用者指派身分識別的易記名稱清單，以 principalId 對應。 如需使用者指派識別的詳細資訊[，請參閱建立、列出或刪除使用者指派的受控識別](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell)。

### <a name="application-template"></a>應用程式範本

若要啟用具有使用者指派身分識別的應用程式，請先將**identity**屬性新增至類型為**userAssigned**的應用程式資源，以及參考的使用者指派的識別，然後在**properties**區段中新增**managedIdentities**物件，其中包含每個使用者指派身分識別的易記名稱清單，以 principalId 對應。

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

在上述範例中，使用者指派身分識別的資源名稱會用來作為應用程式受控識別的易記名稱。 下列範例假設實際的易記名稱是 "AdminUser"。

### <a name="application-package"></a>應用程式套件

1. 針對 Azure Resource Manager 範本的區段中定義的每個識別 `managedIdentities` ，在 `<ManagedIdentity>` [**主體**] 區段下的應用程式資訊清單中新增標記。 `Name`屬性必須符合 `name` 區段中定義的屬性 `managedIdentities` 。

    **ApplicationManifest.xml**

    ```xml
      <Principals>
        <ManagedIdentities>
          <ManagedIdentity Name="AdminUser" />
        </ManagedIdentities>
      </Principals>
    ```

2. 在 [ **ServiceManifestImport** ] 區段中，為使用受控識別的服務新增**IdentityBindingPolicy** 。 此原則會將身分 `AdminUser` 識別對應至稍後需要新增至服務資訊清單的服務特定身分識別名稱。

    **ApplicationManifest.xml**

    ```xml
      <ServiceManifestImport>
        <Policies>
          <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="AdminUser" />
        </Policies>
      </ServiceManifestImport>
    ```

3. 更新服務資訊清單，以在**資源**區段內新增**microsoft.managedidentity** ，其名稱符合 `ServiceIdentityRef` `IdentityBindingPolicy` 應用程式資訊清單中的：

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

* [如何在 Service Fabric 應用程式代碼中使用受控識別](how-to-managed-identity-service-fabric-app-code.md)
* [如何授與 Service Fabric 應用程式存取權給其他 Azure 資源](how-to-grant-access-other-resources.md)

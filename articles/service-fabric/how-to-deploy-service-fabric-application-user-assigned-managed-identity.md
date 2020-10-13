---
title: 使用使用者指派的受控識別部署應用程式
description: 本文說明如何使用使用者指派的受控識別來部署 Service Fabric 應用程式
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: 79d8654733b580be96d59e78f31105077929ac78
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86260089"
---
# <a name="deploy-service-fabric-application-with-a-user-assigned-managed-identity"></a>使用 User-Assigned 受控身分識別部署 Service Fabric 應用程式

若要部署具有受控識別的 Service Fabric 應用程式，必須透過 Azure Resource Manager 部署應用程式，通常是使用 Azure Resource Manager 範本。 如需有關如何透過 Azure Resource Manager 部署 Service Fabric 應用程式的詳細資訊，請參閱將 [應用程式和服務當作 Azure Resource Manager 資源來管理](service-fabric-application-arm-resource.md)。

> [!NOTE] 
> 
> 未部署為 Azure 資源的應用程式 **不能** 有受控識別。 
>
> API 版本支援具有受控識別的 Service Fabric 應用程式部署 `"2019-06-01-preview"` 。 您也可以針對應用程式類型、應用程式類型版本和服務資源使用相同的 API 版本。
>

## <a name="user-assigned-identity"></a>User-Assigned 身分識別

若要啟用具有 User-Assigned 身分識別的應用程式，請先將 identity 屬性新增至**userAssigned**類型的應用程式資源，以及所參考使用者指派的身分**識別**。 然後，在**應用程式**資源的 [**屬性**] 區段內新增**managedIdentities**區段，其中包含要針對每個使用者指派的身分識別 principalId 對應的易記名稱清單。 如需使用者指派的身分識別的詳細資訊，請參閱 [建立、列出或刪除使用者指派的受控識別](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)。

### <a name="application-template"></a>應用程式範本

若要啟用具有使用者指派身分識別的應用程式，請先將**標識**屬性新增至類型為**userAssigned**的應用程式資源和參考的使用者指派身分識別，然後在包含易記名稱清單的**屬性**區段中新增**managedIdentities**物件，以針對每個使用者指派的身分識別 principalId 對應。

```json
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
```

在上述範例中，使用者指派身分識別的資源名稱會用來做為應用程式之受控識別的易記名稱。 下列範例假設實際的易記名稱是 "AdminUser"。

### <a name="application-package"></a>應用程式套件

1. 針對 Azure Resource Manager 範本區段中所定義的每個身分識別 `managedIdentities` ， `<ManagedIdentity>` 在 [ **主體** ] 區段下的應用程式資訊清單中新增標記。 `Name`屬性必須符合 `name` 一節中定義的屬性 `managedIdentities` 。

    **ApplicationManifest.xml**

    ```xml
      <Principals>
        <ManagedIdentities>
          <ManagedIdentity Name="AdminUser" />
        </ManagedIdentities>
      </Principals>
    ```

2. 在 [ **ServiceManifestImport** ] 區段中，加入使用受控識別之服務的 **IdentityBindingPolicy** 。 此原則會將身分 `AdminUser` 識別對應至稍後需要新增至服務資訊清單中的服務特定身分識別名稱。

    **ApplicationManifest.xml**

    ```xml
      <ServiceManifestImport>
        <Policies>
          <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="AdminUser" />
        </Policies>
      </ServiceManifestImport>
    ```

3. 使用與**ManagedIdentity** **Resources** `ServiceIdentityRef` `IdentityBindingPolicy` 應用程式資訊清單中的名稱相符的名稱，更新服務資訊清單，以在 Resources 區段內新增 ManagedIdentity：

    **ServiceManifest.xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```

## <a name="next-steps"></a>接下來的步驟

* [如何在 Service Fabric 應用程式程式碼中使用受控識別](how-to-managed-identity-service-fabric-app-code.md)
* [如何將 Service Fabric 應用程式存取權授與其他 Azure 資源](how-to-grant-access-other-resources.md)

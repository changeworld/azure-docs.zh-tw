---
title: 部署具有系統指派 MI 的 Service Fabric 應用程式
description: 本文說明如何將系統指派的受控識別指派給 Azure Service Fabric 應用程式
ms.topic: article
ms.date: 07/25/2019
ms.openlocfilehash: c5c7a17c51eee18d9b7276f2c57289a5de5c8181
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "81415645"
---
# <a name="deploy-service-fabric-application-with-system-assigned-managed-identity"></a>使用系統指派的受控識別部署 Service Fabric 應用程式

為了存取 Azure Service Fabric 應用程式的受控識別功能，您必須先在叢集上啟用受控識別權杖服務。 這項服務會負責驗證使用其受控識別的 Service Fabric 應用程式，並代表他們取得存取權杖。 啟用服務之後，您可以在左窗格中的 [ **系統** ] 區段下的 Service Fabric Explorer 中看到它，並在 [其他系統服務] 旁的 [名稱網狀 **架構：/System/ManagedIdentityTokenService** ] 下執行。

> [!NOTE] 
> 從 API 版本開始，支援部署具有受控識別的 Service Fabric 應用程式 `"2019-06-01-preview"` 。 您也可以針對應用程式類型、應用程式類型版本和服務資源使用相同的 API 版本。 最小支援的 Service Fabric 執行時間為 6.5 CU2。 在 additoin 中，組建/套件環境也應該具有 CU2 或更高版本的 SF .Net SDK

## <a name="system-assigned-managed-identity"></a>系統指派的受控識別

### <a name="application-template"></a>應用程式範本

若要使用系統指派的受控識別來啟用應用程式，請將 **identity** 屬性新增至應用程式資源，其類型為 **systemAssigned** ，如下列範例所示：

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
此屬性會分別宣告 Azure Resource Manager 的 (，以及受控識別和 Service Fabric 資源提供者，此資源應具有隱含的 (`system assigned`) 受控身分識別。

### <a name="application-and-service-package"></a>應用程式和服務套件

1. 更新應用程式資訊清單，以在 [**主體**] 區段中加入**ManagedIdentity**元素，其中包含單一專案，如下所示：

    **ApplicationManifest.xml**

    ```xml
    <Principals>
      <ManagedIdentities>
        <ManagedIdentity Name="SystemAssigned" />
      </ManagedIdentities>
    </Principals>
    ```
    這會將指派給應用程式的身分識別對應至易記名稱，以進一步指派給組成應用程式的服務。 

2. 在對應至要指派受控識別之服務的 **ServiceManifestImport** 區段中，新增 **IdentityBindingPolicy** 元素，如下所示：

    **ApplicationManifest.xml**

      ```xml
        <ServiceManifestImport>
          <Policies>
            <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="SystemAssigned" />
          </Policies>
        </ServiceManifestImport>
      ```

    這個元素會將應用程式的身分識別指派給服務;如果沒有此指派，服務將無法存取應用程式的身分識別。 在上述程式碼片段中，身分 `SystemAssigned` 識別 (是保留關鍵字) 會對應至易記名稱下的服務定義 `WebAdmin` 。

3. 更新服務資訊清單，以在 [**資源**] 區段內新增**ManagedIdentity**專案，其名稱會與 `ServiceIdentityRef` `IdentityBindingPolicy` 應用程式資訊清單中的定義符合設定的值：

    **ServiceManifest.xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```
    這是身分識別與服務的對應對應（如上所述），但從服務定義的觀點來看。 此身分識別會根據在 `WebAdmin` 應用程式資訊清單中宣告的易記名稱 () 來參考。

## <a name="next-steps"></a>後續步驟
* 查看 Azure Service Fabric 中的[受控識別支援](./concepts-managed-identity.md)
* [部署新的](./configure-new-azure-service-fabric-enable-managed-identity.md) 具有受控識別支援的 Azure Service Fabric 叢集 
* 在現有的 Azure Service Fabric 叢集中[啟用受控識別](./configure-existing-cluster-enable-managed-identity-token-service.md)
* 從原始程式碼利用 Service Fabric 應用程式的 [受控識別](./how-to-managed-identity-service-fabric-app-code.md)
* [使用使用者指派的受控識別部署 Azure Service Fabric 應用程式](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [將 Azure Service Fabric 應用程式存取權授與其他 Azure 資源](./how-to-grant-access-other-resources.md)

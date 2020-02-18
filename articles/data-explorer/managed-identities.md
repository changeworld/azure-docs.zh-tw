---
title: 如何設定 Azure 資料總管叢集的受控識別
description: 瞭解如何設定 Azure 資料總管叢集的受控識別。
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: e76ae2e072bb780ac9788902e9157db871e4f09d
ms.sourcegitcommit: ef568f562fbb05b4bd023fe2454f9da931adf39a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/17/2020
ms.locfileid: "77373367"
---
# <a name="configure-managed-identities-for-your-azure-data-explorer-cluster"></a>設定 Azure 資料總管叢集的受控識別

[Azure Active Directory 的受控識別](/azure/active-directory/managed-identities-azure-resources/overview)可讓您的叢集輕鬆地存取其他受 AAD 保護的資源，例如 Azure Key Vault。 身分識別是由 Azure 平臺所管理，而且不需要布建或輪替任何秘密。 本文說明如何為 Azure 資料總管叢集建立受控識別。 受控識別設定目前僅支援[針對您的叢集啟用客戶管理的金鑰](/azure/data-explorer/security#customer-managed-keys-with-azure-key-vault)。

> [!Note]
> 如果您的應用程式在訂用帳戶或租使用者之間遷移，Azure 資料總管的受控識別不會如預期般運作。 應用程式必須取得新的身分識別，您可以使用 [[移除身分識別](#remove-an-identity)] 停用並重新啟用功能來完成此作業。 下游資源的存取原則也需要更新，才能使用新的身分識別。

## <a name="add-a-system-assigned-identity"></a>新增系統指派的身分識別

您的叢集可以指派系結至您叢集的**系統指派身分識別**，並在刪除叢集時刪除。 一個叢集只能有一個系統指派的身分識別。 使用系統指派的身分識別建立叢集時，需要在叢集上設定額外的屬性。

### <a name="add-a-system-assigned-identity-using-c"></a>使用新增系統指派的身分識別C#

若要使用 Azure 資料總管C#用戶端來設定受控識別，請執行下列動作：

* 安裝[Azure 資料總管（Kusto） NuGet 套件](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)。
* 安裝 Microsoft.identitymodel 的驗證[NuGet 套件](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)。
* 若要執行下列範例，請建立可存取資源的[Azure AD 應用程式](/azure/active-directory/develop/howto-create-service-principal-portal)和服務主體。 您可以在訂用帳戶範圍中新增角色指派，並取得所需的 `Directory (tenant) ID`、`Application ID`和 `Client Secret`。

#### <a name="create-or-update-your-cluster"></a>建立或更新您的叢集

1. 使用 `Identity` 屬性來建立或更新您的叢集：

    ```csharp
    var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
    var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
    var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
    var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
    var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
    var credential = new ClientCredential(clientId, clientSecret);
    var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);
    
    var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);
    
    var kustoManagementClient = new KustoManagementClient(credentials)
    {
        SubscriptionId = subscriptionId
    };
    
    var resourceGroupName = "testrg";
    var clusterName = "mykustocluster";
    var location = "Central US";
    var skuName = "Standard_D13_v2";
    var tier = "Standard";
    var capacity = 5;
    var sku = new AzureSku(skuName, tier, capacity);
    var identity = new Identity(IdentityType.SystemAssigned);
    var cluster = new Cluster(location, sku, identity: identity);
    await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
    ```
    
2. 執行下列命令，以檢查您的叢集是否已成功建立或更新為身分識別：

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    如果結果包含具有 `Succeeded` 值的 `ProvisioningState`，則會建立或更新叢集，而且應該具有下列屬性：
   
    ```csharp
    var principalId = cluster.Identity.PrincipalId;
    var tenantId = cluster.Identity.TenantId;
    ```

    `PrincipalId` 和 `TenantId` 會以 Guid 取代。 `TenantId` 屬性會識別身分識別所屬的 AAD 租使用者。 `PrincipalId` 是叢集新身分識別的唯一識別碼。 在 AAD 內，服務主體的名稱與您提供給 App Service 或 Azure Functions 執行個體的名稱相同。

### <a name="add-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本新增系統指派的身分識別

您可以使用 Azure Resource Manager 範本來將 Azure 資源的部署自動化。 若要深入瞭解如何部署至 Azure 資料總管，請參閱[使用 Azure Resource Manager 範本建立 azure 資料總管叢集和資料庫](create-cluster-database-resource-manager.md)。

新增系統指派的類型，會告訴 Azure 為您的叢集建立及管理身分識別。 對於所有 `Microsoft.Kusto/clusters` 型別的資源來說，您可以在資源定義中加入以下屬性，以建立採用身分識別的資源： 

```json
"identity": {
    "type": "SystemAssigned"
}    
```

例如：

```json
{
    "apiVersion": "2019-09-07",
    "type": "Microsoft.Kusto/clusters",
    "name": "[variables('clusterName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "SystemAssigned"
    },
    "properties": {
        "trustedExternalTenants": [],
        "virtualNetworkConfiguration": null,
        "optimizedAutoscale": null,
        "enableDiskEncryption": false,
        "enableStreamingIngest": false,
    }
}
```

建立叢集時，它具有下列其他屬性：

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

`<TENANTID>` 和 `<PRINCIPALID>` 會以 Guid 取代。 `TenantId` 屬性會識別身分識別所屬的 AAD 租使用者。 `PrincipalId` 是叢集新身分識別的唯一識別碼。 在 AAD 內，服務主體的名稱與您提供給 App Service 或 Azure Functions 執行個體的名稱相同。

## <a name="remove-an-identity"></a>移除身分識別

移除系統指派的身分識別，也會將它從 AAD 中刪除。 刪除叢集資源時，系統指派的身分識別也會自動從 AAD 移除。 藉由停用此功能，可以移除系統指派的身分識別：

```json
"identity": {
    "type": "None"
}
```

## <a name="next-steps"></a>後續步驟

* [在 Azure 中保護 Azure 資料總管叢集](security.md)
* 藉由啟用待用加密，[在 Azure 資料總管 Azure 入口網站中保護您](manage-cluster-security.md)的叢集。
 * [使用設定客戶管理的金鑰C#](customer-managed-keys-csharp.md)
 * [使用 Azure Resource Manager 範本設定客戶管理的金鑰](customer-managed-keys-resource-manager.md)

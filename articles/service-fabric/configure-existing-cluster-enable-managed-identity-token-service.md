---
title: 在現有的 Service Fabric 叢集中設定受控識別支援
description: 以下說明如何在現有的 Azure Service Fabric 叢集中啟用受控識別支援
ms.topic: article
ms.date: 03/11/2019
ms.custom: sfrev
ms.openlocfilehash: 722c507300cc5766d162f336f77f60293c5c90dc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86257615"
---
# <a name="configure-managed-identity-support-in-an-existing-service-fabric-cluster"></a>在現有的 Service Fabric 叢集中設定受控識別支援

若要在您的 Service Fabric 應用程式中使用 [適用于 Azure 資源的受控](../active-directory/managed-identities-azure-resources/overview.md) 識別，請先在叢集中啟用 *受控識別權杖服務* 。 這項服務會負責驗證使用其受控識別的 Service Fabric 應用程式，並代表他們取得存取權杖。 啟用服務之後，您就可以在左窗格的 [ **系統** ] 區段下的 Service Fabric Explorer 中看到它，並在名稱網狀 **架構：/System/ManagedIdentityTokenService 下執行**。

> [!NOTE]
> 需要 Service Fabric 執行階段版本6.5.658.9590 或更高版本，才能啟用 **受控識別權杖服務**。  
>
> 您可以藉由開啟叢集資源，並檢查 [ **Essentials** ] 區段中的 [ **Service Fabric 版本**] 屬性，從 Azure 入口網站找到叢集的 Service Fabric 版本。
>
> 如果叢集處於 **手動** 升級模式，您必須先將它升級至6.5.658.9590 或更新版本。

## <a name="enable-managed-identity-token-service-in-an-existing-cluster"></a>在現有的叢集中啟用*受控識別權杖服務*

若要在現有叢集中啟用受控識別權杖服務，您必須起始指定兩項變更的叢集升級： (1) 啟用受控識別權杖服務，以及 (2) 要求重新開機每個節點。 首先，將下列程式碼片段新增至您的叢集 Azure Resource Manager 範本：

```json
"fabricSettings": [
    {
        "name": "ManagedIdentityTokenService",
        "parameters": [
            {
                "name": "IsEnabled",
                "value": "true"
            }
        ]
    }
]
```

為了讓變更生效，您也必須變更升級原則，以便在升級透過叢集進行升級時，指定每個節點上的 Service Fabric 執行時間的強制重新開機。 此重新開機可確保新啟用的系統服務已在每個節點上啟動並執行。 在下列程式碼片段中， `forceRestart` 是啟用重新開機的基本設定。 針對其餘的參數，請使用下列所述的值，或使用已針對叢集資源指定的現有自訂值。 您可以從 Azure 入口網站選取 Service Fabric 資源或 resources.azure.com 上的 [網狀架構升級] 選項，以從 Azure 入口網站查看網狀架構升級原則的自訂設定 ( [upgradeDescription] ) 。 您無法從 powershell 或 resources.azure.com 查看升級原則 ( ' upgradeDescription ' ) 的預設選項。 如需其他資訊，請參閱 [ClusterUpgradePolicy](/dotnet/api/microsoft.azure.management.servicefabric.models.clusterupgradepolicy?view=azure-dotnet) 。  

```json
"upgradeDescription": {
    "forceRestart": true,
    "healthCheckRetryTimeout": "00:45:00",
    "healthCheckStableDuration": "00:05:00",
    "healthCheckWaitDuration": "00:05:00",
    "upgradeDomainTimeout": "02:00:00",
    "upgradeReplicaSetCheckTimeout": "1.00:00:00",
    "upgradeTimeout": "12:00:00"
}
```

> [!NOTE]
> 升級成功完成時，請記得復原 `forceRestart` 設定，以將後續升級的影響降到最低。 

## <a name="errors-and-troubleshooting"></a>錯誤和疑難排解

如果部署失敗，並出現下列訊息，表示叢集未在足夠的 Service Fabric 版本上執行：

```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="next-steps"></a>接下來的步驟
* [使用系統指派的受控識別部署 Azure Service Fabric 應用程式](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [使用使用者指派的受控識別部署 Azure Service Fabric 應用程式](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [從服務程式代碼利用 Service Fabric 應用程式的受控識別](./how-to-managed-identity-service-fabric-app-code.md)
* [將 Azure Service Fabric 應用程式存取權授與其他 Azure 資源](./how-to-grant-access-other-resources.md)

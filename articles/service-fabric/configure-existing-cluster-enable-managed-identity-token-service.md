---
title: 在現有的 Service Fabric 叢集中設定受控識別支援
description: 以下說明如何在現有的 Azure Service Fabric 叢集中啟用受控識別支援
ms.topic: article
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: cb6e4ab00afd80cba41881e46296f7046a905919
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2020
ms.locfileid: "76934955"
---
# <a name="configure-managed-identity-support-in-an-existing-service-fabric-cluster-preview"></a>在現有的 Service Fabric 叢集中設定受控識別支援（預覽）

若要在您的 Service Fabric 應用程式中使用[適用于 Azure 資源的受控](../active-directory/managed-identities-azure-resources/overview.md)識別，請先在叢集上啟用*受控識別權杖服務*。 此服務負責使用其受控識別來驗證 Service Fabric 應用程式，並代表其取得存取權杖。 啟用服務之後，您可以在左窗格中的 [**系統**] 區段底下的 [ManagedIdentityTokenService] Service Fabric Explorer 中看到它，並在 [ **Fabric：/system/** ] 名稱底下執行。

> [!NOTE]
> 若要啟用**受控識別權杖服務**，需要 Service Fabric 執行階段版本6.5.658.9590 或更高版本。  
>
> 您可以在 [**基本**功能] 區段中開啟叢集資源，並檢查 [ **Service Fabric 版本**] 屬性，以從 Azure 入口網站尋找叢集的 Service Fabric 版本。
>
> 如果叢集處於**手動**升級模式，您必須先將它升級為6.5.658.9590 或更新版本。

## <a name="enable-managed-identity-token-service-in-an-existing-cluster"></a>在現有的叢集中啟用*受控識別權杖服務*

若要在現有的叢集中啟用受控識別權杖服務，您將需要起始叢集升級，並指定兩個變更：（1）啟用受控識別權杖服務，以及（2）要求重新開機每個節點。 首先，在您的叢集 Azure Resource Manager 範本中新增下列程式碼片段：

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

為了讓變更生效，您也需要變更升級原則，以便在升級完成叢集時，在每個節點上指定強制重新開機 Service Fabric 執行時間。 此重新開機可確保新啟用的系統服務已在每個節點上啟動並執行。 在下列程式碼片段中，`forceRestart` 是必要的設定。將您現有的值用於其餘的設定。  

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
> 升級成功完成後，別忘了復原 `forceRestart` 設定，以將後續升級的影響降至最低。 

## <a name="errors-and-troubleshooting"></a>錯誤和疑難排解

如果部署失敗並出現下列訊息，表示叢集未在足夠的 Service Fabric 版本上執行：

```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="next-steps"></a>後續步驟
* [使用系統指派的受控識別來部署 Azure Service Fabric 應用程式](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [使用使用者指派的受控識別來部署 Azure Service Fabric 應用程式](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [從服務程式代碼運用 Service Fabric 應用程式的受控識別](./how-to-managed-identity-service-fabric-app-code.md)
* [將其他 Azure 資源的存取權授與 Azure Service Fabric 應用程式](./how-to-grant-access-other-resources.md)
---
title: 在現有服務結構叢集配置託管識別支援
description: 下面瞭解如何在現有 Azure 服務結構群集中啟用託管標識支援
ms.topic: article
ms.date: 03/11/2019
ms.custom: sfrev
ms.openlocfilehash: 73c890e960f26b8e0e3fa924d9ff6b7a4cd4a4dc
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415678"
---
# <a name="configure-managed-identity-support-in-an-existing-service-fabric-cluster"></a>在現有服務結構叢集配置託管識別支援

要在 Service Fabric 應用程式中[對 Azure 資源使用託管識別](../active-directory/managed-identities-azure-resources/overview.md),請首先在群集上啟用*託管標識權杖服務*。 此服務負責使用其託管標識對 Service Fabric 應用程式進行身份驗證,並代表它們獲取訪問權杖。 啟用服務後,您可以在左側窗格中的 **「系統**」部分下的「服務結構資源管理器」中看到它,該功能在名稱**結構:/系統/託管身份權杖服務**下運行。

> [!NOTE]
> 啟用**託管標識權杖服務**需要服務結構運行時版本 6.5.658.9590 或更高版本。  
>
> 通過打開群集資源並檢查 **「基本」** 部分中的**服務結構版本**屬性,可以從 Azure 門戶找到群集的服務結構版本。
>
> 如果群集處於**手動**升級模式,則需要首先將其升級到 6.5.658.9590 或更高版本。

## <a name="enable-managed-identity-token-service-in-an-existing-cluster"></a>在現有群組集中啟用*託管識別碼服務*

在現有群集中啟用託管標識權杖服務,您需要啟動群集升級,指定兩個更改:(1) 啟用託管標識權杖服務,以及 (2) 請求重新啟動每個節點。 首先,添加叢集 Azure 資源管理員樣本的以下代碼段:

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

為了使更改生效,您還需要更改升級策略,以便在升級通過群集時為每個節點上強制重新啟動 Service Fabric 運行時。 此重新啟動可確保在每個節點上啟動新啟用的系統服務並運行。 在下面的代碼段中`forceRestart`,是啟用重新啟動的基本設置。 對於其餘參數,請使用下面描述的值或使用為群集資源指定的現有自定義值。 通過在服務交換矩陣資源或resources.azure.com上選擇「交換矩陣升級」選項,可以從 Azure 門戶查看結構升級策略的自定義設置("升級說明")。 升級策略("升級描述")的預設選項無法從電源殼或resources.azure.com查看。 有關詳細資訊,請參閱[群集升級策略](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.servicefabric.models.clusterupgradepolicy?view=azure-dotnet)。  

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
> 成功完成升級后,不要忘記回滾`forceRestart`設置,以盡量減少後續升級的影響。 

## <a name="errors-and-troubleshooting"></a>錯誤和疑難排解

如果部署失敗,以下消息,這意味著群集未在足夠高的服務結構版本上運行:

```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="next-steps"></a>後續步驟
* [使用系統配置的託管識別部署 Azure 服務結構應用程式](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [使用使用者配置的託管識別部署 Azure 服務結構應用程式](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [利用服務結構應用程式的託管識別(從服務代碼中使用)](./how-to-managed-identity-service-fabric-app-code.md)
* [授予 Azure 服務結構應用程式對其他 Azure 資源的存取](./how-to-grant-access-other-resources.md)

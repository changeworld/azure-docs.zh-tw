---
title: 為新的服務結構叢集設定託管識別支援
description: 下面瞭解如何在新的 Azure 服務結構群組集中啟用託管識別支援
ms.topic: article
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: dd0cbd3251185a7831852ead47ca0b120126cf55
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415675"
---
# <a name="configure-managed-identity-support-for-a-new-service-fabric-cluster"></a>為新的服務結構叢集設定託管識別支援

要在 Service Fabric 應用程式中[對 Azure 資源使用託管識別](../active-directory/managed-identities-azure-resources/overview.md),請首先在群集上啟用*託管標識權杖服務*。 此服務負責使用其託管標識對 Service Fabric 應用程式進行身份驗證,並代表它們獲取訪問權杖。 啟用服務後,您可以在左側窗格中的 **「系統**」部分下的「服務結構資源管理器」中看到它,該功能在其他系統服務旁邊的名稱**結構:/系統/託管身份令牌服務**下運行。

> [!NOTE]
> 啟用**託管標識權杖服務**需要服務結構運行時版本 6.5.658.9590 或更高版本。  

## <a name="enable-the-managed-identity-token-service"></a>開啟託管識別碼服務

要在叢集建立時啟用管理員識別碼服務,請向叢集 Azure 資源管理員樣本加入以下代碼段:

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

## <a name="errors"></a>Errors

如果此消息的部署失敗,則表示群集不在所需的 Service Fabric 版本上(支援的最小運行時為 6.5 CU2):


```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="related-articles"></a>相關文章

* 檢視 Azure 服務結構中的[託管識別支援](./concepts-managed-identity.md)

* [在現有 Azure 服務結構群集中啟用託管識別支援](./configure-existing-cluster-enable-managed-identity-token-service.md)

## <a name="next-steps"></a>後續步驟

* [使用系統配置的託管識別部署 Azure 服務結構應用程式](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [使用使用者配置的託管識別部署 Azure 服務結構應用程式](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [利用服務結構應用程式的託管識別(從服務代碼中使用)](./how-to-managed-identity-service-fabric-app-code.md)
* [授予 Azure 服務結構應用程式對其他 Azure 資源的存取](./how-to-grant-access-other-resources.md)
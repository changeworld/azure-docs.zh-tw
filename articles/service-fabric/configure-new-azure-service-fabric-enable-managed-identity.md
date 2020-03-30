---
title: 為新的服務結構群集配置託管標識支援
description: 下面瞭解如何在新的 Azure 服務結構群集中啟用託管標識支援
ms.topic: article
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: 0e35d2192fdcdb294b349105f3f0158564cec86b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76930467"
---
# <a name="configure-managed-identity-support-for-a-new-service-fabric-cluster-preview"></a>為新的服務交換矩陣群集配置託管標識支援（預覽）

要在 Service Fabric 應用程式中[對 Azure 資源使用託管標識](../active-directory/managed-identities-azure-resources/overview.md)，請首先在群集上啟用*託管標識權杖服務*。 此服務負責使用其託管標識對 Service Fabric 應用程式進行身份驗證，並代表它們獲取訪問權杖。 啟用服務後，您可以在左側窗格中的 **"系統**"部分下的"服務結構資源管理器"中看到它，該功能在其他系統服務旁邊的名稱**結構：/系統/託管身份權杖服務**下運行。

> [!NOTE]
> 啟用**託管標識權杖服務**需要服務結構執行階段版本 6.5.658.9590 或更高版本。  

## <a name="enable-the-managed-identity-token-service"></a>啟用託管標識權杖服務

要在群集創建時啟用託管標識權杖服務，請向群集 Azure 資源管理器範本添加以下程式碼片段：

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

如果此消息的部署失敗，則表示群集不在所需的 Service Fabric 版本上（支援的最小運行時為 6.5 CU2）：


```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="related-articles"></a>相關文章

* 查看 Azure 服務結構中的[託管標識支援](./concepts-managed-identity.md)

* [在現有 Azure 服務結構群集中啟用託管標識支援](./configure-existing-cluster-enable-managed-identity-token-service.md)

## <a name="next-steps"></a>後續步驟

* [使用系統分配的託管標識部署 Azure 服務結構應用程式](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [使用使用者分配的託管標識部署 Azure 服務結構應用程式](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [利用服務結構應用程式的託管標識（從服務代碼中使用）](./how-to-managed-identity-service-fabric-app-code.md)
* [授予 Azure 服務結構應用程式對其他 Azure 資源的訪問](./how-to-grant-access-other-resources.md)
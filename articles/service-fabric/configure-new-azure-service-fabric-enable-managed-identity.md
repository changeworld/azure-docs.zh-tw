---
title: 設定新 Service Fabric 叢集的受控識別支援
description: 以下說明如何在新的 Azure Service Fabric 叢集中啟用受控識別支援
ms.topic: article
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: dd0cbd3251185a7831852ead47ca0b120126cf55
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81415675"
---
# <a name="configure-managed-identity-support-for-a-new-service-fabric-cluster"></a>設定新 Service Fabric 叢集的受控識別支援

若要在您的 Service Fabric 應用程式中使用[適用于 Azure 資源的受控](../active-directory/managed-identities-azure-resources/overview.md)識別，請先在叢集上啟用*受控識別權杖服務*。 此服務負責使用其受控識別來驗證 Service Fabric 應用程式，並代表其取得存取權杖。 啟用服務之後，您可以 Service Fabric Explorer 在左窗格的 [**系統**] 區段底下的 [ManagedIdentityTokenService] 中看到它，並在 [其他系統服務] 旁的 [ **Fabric：/system/** ] 底下執行。

> [!NOTE]
> 若要啟用**受控識別權杖服務**，需要 Service Fabric 執行階段版本6.5.658.9590 或更高版本。  

## <a name="enable-the-managed-identity-token-service"></a>啟用受控識別權杖服務

若要在建立叢集時啟用受控識別權杖服務，請將下列程式碼片段新增至您的叢集 Azure Resource Manager 範本：

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

## <a name="errors"></a>錯誤

如果部署因此訊息而失敗，則表示叢集不在所需的 Service Fabric 版本上（支援的最低執行時間為 6.5 CU2）：


```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="related-articles"></a>相關文章

* 審查 Azure Service Fabric 中的[受控識別支援](./concepts-managed-identity.md)

* [在現有的 Azure Service Fabric 叢集中啟用受控識別支援](./configure-existing-cluster-enable-managed-identity-token-service.md)

## <a name="next-steps"></a>後續步驟

* [使用系統指派的受控識別來部署 Azure Service Fabric 應用程式](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [使用使用者指派的受控識別來部署 Azure Service Fabric 應用程式](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [從服務程式代碼運用 Service Fabric 應用程式的受控識別](./how-to-managed-identity-service-fabric-app-code.md)
* [將其他 Azure 資源的存取權授與 Azure Service Fabric 應用程式](./how-to-grant-access-other-resources.md)
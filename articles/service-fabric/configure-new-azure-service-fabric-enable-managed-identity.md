---
title: 針對新的 Service Fabric 叢集設定受控識別支援
description: 以下說明如何在新的 Azure Service Fabric 叢集中啟用受控識別支援
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: ae4fec452a2342a68843d874ba955b594014c46d
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96574661"
---
# <a name="configure-managed-identity-support-for-a-new-service-fabric-cluster"></a>針對新的 Service Fabric 叢集設定受控識別支援

若要在您的 Service Fabric 應用程式中使用 [適用于 Azure 資源的受控](../active-directory/managed-identities-azure-resources/overview.md) 識別，請先在叢集中啟用 *受控識別權杖服務* 。 這項服務會負責驗證使用其受控識別的 Service Fabric 應用程式，並代表他們取得存取權杖。 啟用服務之後，您可以在左窗格中的 [ **系統** ] 區段下的 Service Fabric Explorer 中看到它，並在 [其他系統服務] 旁的 [名稱網狀 **架構：/System/ManagedIdentityTokenService** ] 下執行。

> [!NOTE]
> 需要 Service Fabric 執行階段版本6.5.658.9590 或更高版本，才能啟用 **受控識別權杖服務**。  

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

## <a name="errors"></a>Errors

如果部署失敗並出現這則訊息，表示叢集不是所需的 Service Fabric 版本 (支援的最低執行時間是 6.5 CU2) ：


```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="related-articles"></a>相關文章

* 查看 Azure Service Fabric 中的[受控識別支援](./concepts-managed-identity.md)

* [在現有的 Azure Service Fabric 叢集中啟用受控識別支援](./configure-existing-cluster-enable-managed-identity-token-service.md)

## <a name="next-steps"></a>後續步驟

* [使用系統指派的受控識別部署 Azure Service Fabric 應用程式](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [使用使用者指派的受控識別部署 Azure Service Fabric 應用程式](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [從服務程式代碼利用 Service Fabric 應用程式的受控識別](./how-to-managed-identity-service-fabric-app-code.md)
* [將 Azure Service Fabric 應用程式存取權授與其他 Azure 資源](./how-to-grant-access-other-resources.md)
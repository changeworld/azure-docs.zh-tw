---
title: 適用于 Azure 的受控識別
description: 瞭解如何使用適用于 Azure 的受控識別搭配 Service Fabric。
ms.topic: conceptual
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: a5c82f49195fc6d790ca4308d78b70106b6cc042
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84710298"
---
# <a name="using-managed-identities-for-azure-with-service-fabric"></a>使用適用于 Azure 的受控識別搭配 Service Fabric

建立雲端應用程式時，常見的挑戰是如何安全地管理程式碼中的認證，以便驗證各種服務，而不需要在開發人員工作站或原始檔控制中將它們儲存在本機。 *適用于 Azure 的受控*識別可在 Azure Active Directory （Azure AD）中為您的所有資源解決此問題，方法是在 Azure AD 內提供自動管理的身分識別。 您可以使用服務的身分識別來向任何支援 Azure AD 驗證的服務進行驗證，包括 Key Vault，而不需要將任何認證儲存在您的程式碼中。

適用于 azure*資源的受控*識別可免費使用 azure 訂用帳戶的 Azure AD。 沒有任何額外成本。

> [!NOTE]
> *適用于 Azure 的受控*識別是先前稱為受控服務識別（MSI）之服務的新名稱。

## <a name="concepts"></a>概念

適用于 Azure 的受控識別是以幾個重要概念為基礎：

- **用戶端識別碼**-在其初始布建期間系結至應用程式和服務主體的 Azure AD 所產生的唯一識別碼（也請參閱[應用程式識別碼](/azure/active-directory/develop/developer-glossary#application-id-client-id)）。

- **主體識別碼**-受控識別之服務主體物件的物件識別碼，用來授與 Azure 資源的角色型存取權。

- **服務主體**-Azure Active Directory 物件，代表指定租使用者中 AAD 應用程式的投影（也請參閱[服務主體](../active-directory/develop/developer-glossary.md#service-principal-object)）。

受控身分識別有兩種：

- **系統指派的受控識別**會直接在 Azure 服務實例上啟用。  系統指派之身分識別的生命週期對其啟用所在的 Azure 服務實例而言是唯一的。
- **使用者指派的受控識別**會以獨立 Azure 資源的形式建立。 身分識別可指派給一或多個 Azure 服務實例，並與這些實例的生命週期分開管理。

若要進一步瞭解受控識別類型之間的差異，請參閱[適用于 Azure 資源的受控識別如何執行？](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)。

## <a name="supported-scenarios-for-service-fabric-applications"></a>Service Fabric 應用程式的支援案例

只有 Azure 部署的 Service Fabric 叢集才支援 Service Fabric 的受控識別，而且僅適用于部署為 Azure 資源的應用程式;未部署為 Azure 資源的應用程式無法指派身分識別。 概念上說，支援 Azure Service Fabric 叢集中的受控識別是由兩個階段所組成：

1. 將一個或多個受控識別指派給應用程式資源;應用程式可能會分別指派單一系統指派的身分識別，以及（或）高達32的使用者指派身分識別。

2. 在應用程式的定義中，將指派給應用程式的其中一個身分識別對應至組成應用程式的任何個別服務。

應用程式的系統指派身分識別對該應用程式而言是唯一的;使用者指派的身分識別是一種獨立的資源，可以指派給多個應用程式。 在應用程式內，單一身分識別（不論是系統指派或使用者指派）可以指派給應用程式的多個服務，但每個個別服務只能指派一個身分識別。 最後，必須明確地將身分識別指派給服務，才能存取這項功能。 實際上，應用程式的身分識別對應到其構成服務允許應用程式內隔離，服務只能使用對應到它的身分識別。  

目前，這項功能支援下列案例：

- 部署新的應用程式，其中包含一或多個服務，以及一或多個指派的身分識別

- 將一個或多個受控識別指派給現有的（Azure 部署）應用程式，以存取 Azure 資源

下列案例不受支援或不建議使用;請注意，這些動作可能不會被封鎖，但可能會導致您的應用程式中斷：

- 移除或變更指派給應用程式的身分識別;如果您必須進行變更，請先提交個別的部署，以加入新的身分識別指派，然後再移除先前指派的身分。 從現有的應用程式移除身分識別可能會產生不想要的效果，包括讓應用程式處於無法升級的狀態。 如果需要移除身分識別，則可以放心地刪除應用程式;請注意，這會刪除與應用程式相關聯的系統指派身分識別（如果有定義的話），並會移除指派給應用程式之使用者指派身分識別的任何關聯。

- 受控識別的 Service Fabric 支援目前不會整合到[azureservicetokenprovider 會](../key-vault/general/service-to-service-authentication.md)中。

## <a name="next-steps"></a>後續步驟

- [使用受控識別支援來部署新的 Azure Service Fabric 叢集](./configure-new-azure-service-fabric-enable-managed-identity.md)
- [在現有的 Azure Service Fabric 叢集中啟用受控識別支援](./configure-existing-cluster-enable-managed-identity-token-service.md)
- [使用系統指派的受控識別來部署 Azure Service Fabric 應用程式](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
- [使用使用者指派的受控識別來部署 Azure Service Fabric 應用程式](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
- [從服務程式代碼運用 Service Fabric 應用程式的受控識別](./how-to-managed-identity-service-fabric-app-code.md)
- [將其他 Azure 資源的存取權授與 Azure Service Fabric 應用程式](./how-to-grant-access-other-resources.md)
- [宣告和使用應用程式秘密作為 KeyVaultReferences](./service-fabric-keyvault-references.md)

---
title: 適用于 Azure 的受控識別
description: 瞭解如何使用適用于 Azure 的受控識別搭配 Service Fabric。
ms.topic: conceptual
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: 786d9b4b577f4a686367a103542ae4f8fa5453da
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86257626"
---
# <a name="using-managed-identities-for-azure-with-service-fabric"></a>使用適用于 Azure 的受控識別搭配 Service Fabric

建立雲端應用程式時，常見的挑戰是如何安全地管理程式碼中的認證，以驗證各種服務，而不需要將它們儲存在開發人員工作站或原始檔控制中。 *適用于 Azure 的受控* 識別可針對 Azure Active Directory (Azure AD) 中的所有資源解決此問題，方法是在 Azure AD 中提供自動管理的身分識別。 您可以使用服務的身分識別，向任何支援 Azure AD 驗證的服務進行驗證，包括 Key Vault，而不會在您的程式碼中儲存任何認證。

適用于 azure*資源的受控*識別可透過 azure 訂用帳戶的 Azure AD 免費使用。 沒有任何額外成本。

> [!NOTE]
> *適用于 Azure 的受控* 識別是先前稱為「受控服務識別」 (MSI) 之服務的新名稱。

## <a name="concepts"></a>概念

適用于 Azure 的受控識別會以數個重要概念為基礎：

- **用戶端識別碼** -在其初始布建期間系結至應用程式和服務主體 Azure AD 所產生的唯一識別碼 (也請參閱 [應用程式識別碼](../active-directory/develop/developer-glossary.md#application-id-client-id)。 ) 

- **主體識別碼** -用來授與角色型存取權給 Azure 資源的受控識別之服務主體物件的物件識別碼。

- **服務主體** -Azure Active Directory 物件，代表指定租使用者中 AAD 應用程式的投射 (也請參閱 [服務主體](../active-directory/develop/developer-glossary.md#service-principal-object)。 ) 

受控身分識別有兩種：

- **系統指派的受控識別**會直接在 Azure 服務實例上啟用。  系統指派的身分識別生命週期對其啟用的 Azure 服務實例而言是唯一的。
- **使用者指派的受控識別**會以獨立 Azure 資源的形式建立。 身分識別可以指派給一或多個 Azure 服務實例，並與這些實例的生命週期分開管理。

若要進一步瞭解受控識別類型之間的差異，請參閱適用 [于 Azure 資源的受控識別如何運作？](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)。

## <a name="supported-scenarios-for-service-fabric-applications"></a>Service Fabric 應用程式的支援案例

只有 Azure 部署的 Service Fabric 叢集支援 Service Fabric 的受控識別，且僅適用于部署為 Azure 資源的應用程式;未部署為 Azure 資源的應用程式無法指派身分識別。 在概念上，Azure Service Fabric 叢集中的受控識別支援是由兩個階段所組成：

1. 將一或多個受控識別指派給應用程式資源;應用程式可能會分別指派給一個系統指派的身分識別，以及/或最多32個使用者指派的身分識別。

2. 在應用程式的定義中，將指派給應用程式的其中一個身分識別對應至組成應用程式的任何個別服務。

應用程式的系統指派身分識別對該應用程式而言是唯一的;使用者指派的身分識別是可指派給多個應用程式的獨立資源。 在應用程式中，單一身分識別 (是否可以將系統指派或使用者指派的) 指派給應用程式的多個服務，但每個個別服務只能指派一個身分識別。 最後，必須明確地為服務指派身分識別，才能存取這項功能。 實際上，將應用程式的身分識別對應至其組成服務，可允許應用程式內隔離，而服務可能只會使用與其對應的身分識別。  

目前，這項功能支援下列案例：

- 使用一或多個服務和一或多個指派的身分識別來部署新的應用程式

- 將一或多個受控識別指派給現有 (Azure 部署的) 應用程式，以便存取 Azure 資源

下列案例不受支援或不建議使用：請注意，這些動作可能不會被封鎖，但可能會導致您的應用程式中斷：

- 移除或變更指派給應用程式的身分識別;如果您必須進行變更，請提交個別的部署，以先新增身分識別指派，然後再移除先前指派的身分識別。 從現有的應用程式移除身分識別可能會產生非預期的結果，包括讓您的應用程式處於無法升級的狀態。 如果需要移除身分識別，則可以安全地刪除應用程式;請注意，這將會刪除系統指派的身分識別 (如果有定義) 與應用程式相關聯，而且將會移除任何與指派給應用程式的使用者指派身分識別相關聯的關聯。

- 目前未將受控識別的 Service Fabric 支援整合到 [azureservicetokenprovider 會](../key-vault/general/service-to-service-authentication.md)中。

## <a name="next-steps"></a>接下來的步驟

- [使用受控識別支援部署新的 Azure Service Fabric 叢集](./configure-new-azure-service-fabric-enable-managed-identity.md)
- [在現有的 Azure Service Fabric 叢集中啟用受控識別支援](./configure-existing-cluster-enable-managed-identity-token-service.md)
- [使用系統指派的受控識別部署 Azure Service Fabric 應用程式](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
- [使用使用者指派的受控識別部署 Azure Service Fabric 應用程式](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
- [從服務程式代碼利用 Service Fabric 應用程式的受控識別](./how-to-managed-identity-service-fabric-app-code.md)
- [將 Azure Service Fabric 應用程式存取權授與其他 Azure 資源](./how-to-grant-access-other-resources.md)
- [宣告和使用應用程式秘密作為 KeyVaultReferences](./service-fabric-keyvault-references.md)

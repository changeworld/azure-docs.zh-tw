---
title: Azure 的託管標識
description: 瞭解如何將託管標識用於 Azure 與服務結構。
ms.topic: conceptual
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: 06ebcfdf3d6a3815908752153acb09437d745d15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76986745"
---
# <a name="using-managed-identities-for-azure-with-service-fabric-preview"></a>使用具有服務結構的 Azure 的託管標識（預覽）

構建雲應用程式時面臨的一個常見挑戰是，如何安全地管理代碼中的憑據，以便對各種服務進行身份驗證，而無需將它們保存在開發人員工作站或原始程式碼管理中。 *Azure 的託管標識*通過在 Azure AD 中自動託管標識為 Azure 活動目錄 （Azure AD） 中的所有資源提供此問題。 可以使用服務標識對支援 Azure AD 身份驗證（包括金鑰保存庫）的任何服務進行身份驗證，而無需在代碼中存儲任何憑據。

*Azure 資源的託管標識*對於 Azure 訂閱的 Azure AD 是免費的。 沒有任何額外成本。

> [!NOTE]
> *Azure 的託管標識*是以前稱為託管服務標識 （MSI） 的服務的新名稱。

## <a name="concepts"></a>概念

Azure 的託管標識基於幾個關鍵概念：

- **用戶端 ID** - Azure AD 生成的唯一識別碼，在初始預配期間綁定到應用程式和服務主體（另請參閱[應用程式 ID](/azure/active-directory/develop/developer-glossary#application-id-client-id)。

- **主體 ID** - 託管標識的服務主體物件的物件識別碼，用於授予對 Azure 資源的基於角色的存取權限。

- **服務主體**- Azure 活動目錄物件，表示給定租戶中 AAD 應用程式的投影（另請參閱[服務主體](../active-directory/develop/developer-glossary.md#service-principal-object).

受控身分識別有兩種：

- 直接在 Azure 服務實例上啟用**系統分配的託管標識**。  系統分配的標識的生命週期對於啟用它的 Azure 服務實例是唯一的。
- **使用者分配的託管標識**將創建為獨立的 Azure 資源。 標識可以分配給一個或多個 Azure 服務實例，並獨立于這些實例的生命週期進行管理。

為了進一步瞭解託管標識類型之間的區別，請參閱[Azure 資源的託管標識如何工作？](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work)

## <a name="supported-scenarios-for-service-fabric-applications"></a>支援服務結構應用程式的方案

服務結構的託管標識僅在 Azure 部署的服務結構群集中受支援，並且僅適用于作為 Azure 資源部署的應用程式;無法為未部署為 Azure 資源的應用程式分配標識。 從概念上講，對 Azure 服務結構群集中的託管標識的支援包括兩個階段：

1. 將一個或多個託管標識分配給應用程式資源;可以分別為應用程式分配一個系統分配的標識和/或最多 32 個使用者分配的身份。

2. 在應用程式的定義中，將分配給應用程式的任何單個服務（包括應用程式）的標識之一映射。

應用程式的系統分配標識對該應用程式是唯一的;使用者分配的標識是一個獨立的資源，可以分配給多個應用程式。 在應用程式中，可以將單個標識（無論是系統分配還是使用者分配）分配給應用程式的多個服務，但每個單獨的服務只能分配一個標識。 最後，必須顯式為服務分配標識才能訪問此功能。 實際上，將應用程式的身份映射到其組成服務允許應用程式內隔離 - 服務只能使用映射到它的標識。  

目前，此預覽功能支援以下方案：

- 使用一個或多個服務和一個或多個分配標識部署新應用程式

- 將一個或多個託管標識分配給現有（Azure 部署）應用程式，以便訪問 Azure 資源

不支援或不建議使用以下方案;請注意，這些操作可能不會被阻止，但可能會導致應用程式中的中斷：

- 刪除或更改分配給應用程式的身份;如果必須進行更改，請提交單獨的部署以先添加新標識分配，然後刪除以前分配的標識分配。 從現有應用程式中刪除標識可能會產生不良影響，包括使應用程式處於不可升級的狀態。 如果需要刪除標識，可以安全地完全刪除應用程式;請注意，這將刪除與應用程式關聯的系統分配標識（如果已定義），並將刪除與分配給應用程式的使用者分配標識的任何關聯。

- 此時未將託管標識的服務交換矩陣支援集成到[AzureServiceToken 提供程式](../key-vault/service-to-service-authentication.md)中;集成將在託管標識功能的預覽期結束時完成。

>
> [!NOTE]
>
> 這項功能處於預覽狀態。 它可能經常更改，不適合生產部署。

## <a name="next-steps"></a>後續步驟

- [部署具有託管標識支援的新 Azure 服務結構群集](./configure-new-azure-service-fabric-enable-managed-identity.md)
- [在現有 Azure 服務結構群集中啟用託管標識支援](./configure-existing-cluster-enable-managed-identity-token-service.md)
- [使用系統分配的託管標識部署 Azure 服務結構應用程式](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
- [使用使用者分配的託管標識部署 Azure 服務結構應用程式](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
- [利用服務結構應用程式的託管標識（從服務代碼中使用）](./how-to-managed-identity-service-fabric-app-code.md)
- [授予 Azure 服務結構應用程式對其他 Azure 資源的訪問](./how-to-grant-access-other-resources.md)
- [聲明和使用應用程式機密作為金鑰庫引用](./service-fabric-keyvault-references.md)

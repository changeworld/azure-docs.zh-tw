---
title: 使用 Azure 存儲資來源提供者訪問管理資源
description: Azure 存儲資來源提供者是一種服務，它提供對 Azure 存儲的管理資源的訪問。 可以使用 Azure 存儲資來源提供者創建、更新、管理和刪除資源，如存儲帳戶、專用終結點和帳戶訪問金鑰。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f5d42a6a0567d3949bc4b0fb1947450a9c957f18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972354"
---
# <a name="use-the-azure-storage-resource-provider-to-access-management-resources"></a>使用 Azure 存儲資來源提供者訪問管理資源

Azure Resource Manager 是 Azure 的部署和管理服務。 Azure 存儲資來源提供者是基於 Azure 資源管理器的服務，它提供對 Azure 存儲的管理資源的訪問。 可以使用 Azure 存儲資來源提供者創建、更新、管理和刪除資源，如存儲帳戶、專用終結點和帳戶訪問金鑰。 有關 Azure 資源管理器的詳細資訊，請參閱[Azure 資源管理器概述](/azure/azure-resource-manager/resource-group-overview)。

可以使用 Azure 存儲資來源提供者執行創建或刪除存儲帳戶或獲取訂閱中的存儲帳戶清單等操作。 要授權針對 Azure 存儲資來源提供者的請求，請使用 Azure 活動目錄 （Azure AD）。 本文介紹如何為管理資源配置許可權，並指向演示如何針對 Azure 存儲資來源提供者發出請求的示例。

## <a name="management-resources-versus-data-resources"></a>管理資源與資料資源

Microsoft 提供了兩個用於使用 Azure 存儲資源的 REST API。 這些 API 構成了針對 Azure 存儲可以執行的所有操作的基礎。 Azure 存儲 REST API 使您能夠處理存儲帳戶中的資料，包括 Blob、佇列、檔和表資料。 Azure 存儲資來源提供者 REST API 使您能夠使用存儲帳戶和相關資源。

讀取或寫入 Blob 資料的請求需要與執行管理操作的請求不同的許可權。 RBAC 提供對兩種資源的許可權的細細微性控制。 將 RBAC 角色指派給安全主體時，請確保您瞭解該主體將被授予哪些許可權。 有關描述哪些操作與每個內置 RBAC 角色關聯的詳細參考，請參閱 Azure[資源的內置角色](../../role-based-access-control/built-in-roles.md)。

Azure 存儲支援使用 Azure AD 授權針對 Blob 和佇列存儲的請求。 有關 Blob 和佇列資料操作的 RBAC 角色的資訊，請參閱[使用 Active Directory 授權對 Blob 和佇列的訪問](storage-auth-aad.md)。

## <a name="assign-management-permissions-with-role-based-access-control-rbac"></a>使用基於角色的存取控制 （RBAC） 分配管理許可權

每個 Azure 訂閱都有一個關聯的 Azure 活動目錄，用於管理使用者、組和應用程式。 在[Microsoft 標識平臺](/azure/active-directory/develop/)的上下文中，使用者、組或應用程式也稱為安全主體。 您可以使用基於角色的存取控制 （RBAC） 將訂閱中的資源授予在 Active Directory 中定義的安全主體的存取權限。

將 RBAC 角色指派給安全主體時，還會指示角色授予的許可權生效的範圍。 對於管理操作，您可以在訂閱、資源組或存儲帳戶級別分配角色。 可以使用[Azure 門戶、Azure](https://portal.azure.com/) [CLI 工具](../../cli-install-nodejs.md)[、PowerShell](/powershell/azureps-cmdlets-docs)或[Azure 存儲資來源提供者 REST API](/rest/api/storagerp)將 RBAC 角色指派給安全主體。

有關 RBAC 的詳細資訊，請參閱[什麼是 Azure 資源的基於角色的存取控制 （RBAC）？](../../role-based-access-control/overview.md) [和經典訂閱管理員角色、Azure RBAC 角色和 Azure AD 管理員角色](../../role-based-access-control/rbac-and-directory-admin-roles.md)。

### <a name="built-in-roles-for-management-operations"></a>管理操作的內置角色

Azure 提供授予調用管理操作的許可權的內置角色。 Azure 存儲還提供專為 Azure 存儲資來源提供者使用的內置角色。

授予調用存儲管理操作的許可權的內置角色包括下表中描述的角色：

|    RBAC 角色    |    描述    |    包括存取帳戶金鑰？    |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|
| **擁有者** | 可以管理所有存儲資源和對資源的訪問。  | 是，提供查看和重新生成存儲帳戶金鑰的許可權。 |
| **參與者**  | 可以管理所有存儲資源，但不能管理分配給資源。 | 是，提供查看和重新生成存儲帳戶金鑰的許可權。 |
| **閱讀程式** | 可以查看有關存儲帳戶的資訊，但不能查看帳戶金鑰。 | 否。 |
| **儲存體帳戶參與者** | 可以管理存儲帳戶，獲取有關訂閱的資源組和資源的資訊，以及創建和管理訂閱資源組部署。 | 是，提供查看和重新生成存儲帳戶金鑰的許可權。 |
| **使用者存取系統管理員** | 可以管理對存儲帳戶的訪問。   | 是，允許安全主體為自己和其他人分配任何許可權。 |
| **虛擬機器參與者** | 可以管理虛擬機器，但不能管理它們所連接的存儲帳戶。   | 是，提供查看和重新生成存儲帳戶金鑰的許可權。 |

表中的第三列指示內置角色是否支援**Microsoft.存儲/存儲帳戶/清單鍵/操作**。 此操作授予讀取和重新生成存儲帳戶金鑰的許可權。 訪問 Azure 存儲管理資源的許可權不包括訪問資料的許可權。 但是，如果使用者有權存取帳戶金鑰，則他們可以使用帳戶金鑰通過共用金鑰授權訪問 Azure 存儲資料。

### <a name="custom-roles-for-management-operations"></a>管理操作的自訂角色

Azure 還支援定義自訂 RBAC 角色以訪問管理資源。 有關自訂角色的詳細資訊，請參閱[Azure 資源的自訂角色](../../role-based-access-control/custom-roles.md)。

## <a name="code-samples"></a>程式碼範例

有關演示如何從 Azure 存儲管理庫授權和調用管理操作的代碼示例，請參閱以下示例：

- [.NET](https://github.com/Azure-Samples/storage-dotnet-resource-provider-getting-started)
- [JAVA](https://github.com/Azure-Samples/storage-java-manage-storage-accounts)
- [Node.js](https://github.com/Azure-Samples/storage-node-resource-provider-getting-started)
- [Python](https://github.com/Azure-Samples/storage-python-manage)

## <a name="azure-resource-manager-versus-classic-deployments"></a>Azure 資源管理器與經典部署

Resource Manager 和傳統部署模型代表部署和管理 Azure 解決方案的兩個不同方式。 Microsoft 建議在創建新存儲帳戶時使用 Azure 資源管理器部署模型。 如果可能，Microsoft 還建議您使用資源管理器模型重新創建現有的經典存儲帳戶。 儘管您可以使用經典部署模型創建存儲帳戶，但經典模型的靈活性較低，最終將被棄用。

有關 Azure 部署模型的詳細資訊，請參閱[資源管理器和經典部署](../../azure-resource-manager/management/deployment-models.md)。

## <a name="next-steps"></a>後續步驟

- [Azure 資源管理器概述](/azure/azure-resource-manager/resource-group-overview)
- [什麼是 Azure 資源的角色型存取控制 (RBAC)？](../../role-based-access-control/overview.md)
- [Azure 存儲資來源提供者的可伸縮性目標](scalability-targets-resource-provider.md)

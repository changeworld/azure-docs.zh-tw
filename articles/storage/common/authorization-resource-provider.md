---
title: 使用 Azure 儲存體資源提供者存取管理資源
description: Azure 儲存體資源提供者是一項服務，可讓您存取 Azure 儲存體的管理資源。 您可以使用 Azure 儲存體資源提供者來建立、更新、管理及刪除資源，例如儲存體帳戶、私人端點和帳戶存取金鑰。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: fcf3e9228c8e651efb8f97067f7ba9eead5959db
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92789670"
---
# <a name="use-the-azure-storage-resource-provider-to-access-management-resources"></a>使用 Azure 儲存體資源提供者存取管理資源

Azure Resource Manager 是 Azure 的部署和管理服務。 Azure 儲存體資源提供者是以 Azure Resource Manager 為基礎的服務，可讓您存取 Azure 儲存體的管理資源。 您可以使用 Azure 儲存體資源提供者來建立、更新、管理及刪除資源，例如儲存體帳戶、私人端點和帳戶存取金鑰。 如需 Azure Resource Manager 的詳細資訊，請參閱 [Azure Resource Manager 總覽](../../azure-resource-manager/management/overview.md)。

您可以使用 Azure 儲存體資源提供者來執行動作，例如建立或刪除儲存體帳戶，或取得訂用帳戶中的儲存體帳戶清單。 若要對 Azure 儲存體資源提供者授權要求，請使用 Azure Active Directory (Azure AD) 。 本文說明如何將許可權指派給管理資源，並指向示範如何對 Azure 儲存體資源提供者提出要求的範例。

## <a name="management-resources-versus-data-resources"></a>管理資源與資料資源

Microsoft 提供兩個 REST Api 來處理 Azure 儲存體資源。 這些 Api 構成您可以對 Azure 儲存體執行的所有動作的基礎。 Azure 儲存體 REST API 可讓您使用儲存體帳戶中的資料，包括 blob、佇列、檔案和資料表資料。 Azure 儲存體資源提供者 REST API 可讓您使用儲存體帳戶和相關資源。

讀取或寫入 blob 資料的要求所需的許可權，與執行管理作業的要求不同。 Azure RBAC 可讓您更精細地控制這兩種資源類型的許可權。 當您將 Azure 角色指派給安全性主體時，請確定您瞭解將授與主體的許可權。 如需說明哪些動作與每個 Azure 內建角色相關聯的詳細參考，請參閱 [Azure 內建角色](../../role-based-access-control/built-in-roles.md)。

Azure 儲存體支援使用 Azure AD 對 Blob 和佇列儲存體授權要求。 如需有關適用于 blob 和佇列資料作業之 Azure 角色的詳細資訊，請參閱 [使用 Active Directory 授權存取 blob 和佇列](storage-auth-aad.md)。

## <a name="assign-management-permissions-with-azure-role-based-access-control-azure-rbac"></a>使用 Azure 角色型存取控制指派管理許可權 (Azure RBAC) 

每個 Azure 訂用帳戶都有一個相關聯的 Azure Active Directory，可管理使用者、群組和應用程式。 在 [Microsoft 身分識別平臺](../../active-directory/develop/index.yml)的環境中，使用者、群組或應用程式也稱為「安全性主體」（security principal）。 您可以使用 Azure 角色型存取控制 (Azure RBAC) ，將訂用帳戶中所定義之資源的存取權授與 Active Directory 中定義的安全性主體。

當您將 Azure 角色指派給安全性主體時，您也會指出角色所授與的許可權範圍。 針對管理作業，您可以在訂用帳戶、資源群組或儲存體帳戶層級指派角色。 您可以使用 [Azure 入口網站](https://portal.azure.com/)、 [Azure CLI 工具](/cli/azure/install-classic-cli)、 [PowerShell](/powershell/azure/)或 [Azure 儲存體資源提供者 REST API](/rest/api/storagerp)，將 Azure 角色指派給安全性主體。

如需詳細資訊，請參閱 [什麼是 AZURE RBAC)  (azure 角色型存取控制？](../../role-based-access-control/overview.md) 和 [傳統訂用帳戶管理員角色、Azure 角色和 Azure AD 系統管理員角色](../../role-based-access-control/rbac-and-directory-admin-roles.md)。

### <a name="built-in-roles-for-management-operations"></a>管理作業的內建角色

Azure 提供內建角色，可授與呼叫管理作業的許可權。 Azure 儲存體也提供專門用來與 Azure 儲存體資源提供者搭配使用的內建角色。

授與許可權以呼叫存放裝置管理作業的內建角色，包含下表所述的角色：

|    Azure 角色    |    描述    |    包含帳戶金鑰的存取權嗎？    |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|
| **擁有者** | 可以管理所有儲存體資源和資源的存取權。  | 是，提供查看和重新產生儲存體帳戶金鑰的許可權。 |
| **參與者**  | 可以管理所有儲存體資源，但無法管理對資源的存取。 | 是，提供查看和重新產生儲存體帳戶金鑰的許可權。 |
| **讀取者** | 可以查看儲存體帳戶的相關資訊，但無法查看帳戶金鑰。 | 否。 |
| **儲存體帳戶參與者** | 可以管理儲存體帳戶、取得訂用帳戶資源群組和資源的相關資訊，以及建立和管理訂用帳戶資源群組部署。 | 是，提供查看和重新產生儲存體帳戶金鑰的許可權。 |
| **使用者存取系統管理員** | 可以管理對儲存體帳戶的存取。   | 是，允許安全性主體將任何許可權指派給自己和其他人。 |
| **虛擬機器參與者** | 可以管理虛擬機器，但無法管理其連線的儲存體帳戶。   | 是，提供查看和重新產生儲存體帳戶金鑰的許可權。 |

資料表中的第三個數據行表示內建角色是否支援 **Microsoft. Storage/storageAccounts/listkeys/action** 。 此動作會授與讀取和重新產生儲存體帳戶金鑰的許可權。 存取 Azure 儲存體管理資源的許可權也不包含存取資料的許可權。 不過，如果使用者具有帳戶金鑰的存取權，他們就可以使用帳戶金鑰來透過共用金鑰授權存取 Azure 儲存體資料。

### <a name="custom-roles-for-management-operations"></a>管理作業的自訂角色

Azure 也支援定義 Azure 自訂角色，以存取管理資源。 如需自訂角色的詳細資訊，請參閱 [Azure 自訂角色](../../role-based-access-control/custom-roles.md)。

## <a name="code-samples"></a>程式碼範例

如需示範如何從 Azure 儲存體管理程式庫授權和呼叫管理作業的程式碼範例，請參閱下列範例：

- [.NET](https://github.com/Azure-Samples/storage-dotnet-resource-provider-getting-started)
- [Java](https://github.com/Azure-Samples/storage-java-manage-storage-accounts)
- [Node.js](https://github.com/Azure-Samples/storage-node-resource-provider-getting-started)
- [Python](https://github.com/Azure-Samples/storage-python-manage)

## <a name="azure-resource-manager-versus-classic-deployments"></a>Azure Resource Manager 與傳統部署

Resource Manager 和傳統部署模型代表部署和管理 Azure 解決方案的兩個不同方式。 當您建立新的儲存體帳戶時，Microsoft 建議使用 Azure Resource Manager 部署模型。 可能的話，Microsoft 也會建議您使用 Resource Manager 模型重新建立現有的傳統儲存體帳戶。 雖然您可以使用傳統部署模型來建立儲存體帳戶，但傳統模型的彈性較低，而且最終將會被取代。

如需有關 Azure 部署模型的詳細資訊，請參閱 [Resource Manager 和傳統部署](../../azure-resource-manager/management/deployment-models.md)。

## <a name="next-steps"></a>後續步驟

- [Azure Resource Manager 概觀](../../azure-resource-manager/management/overview.md)
- [什麼是 Azure 角色型存取控制 (Azure RBAC)？](../../role-based-access-control/overview.md)
- [Azure 儲存體資源提供者的擴充性目標](scalability-targets-resource-provider.md)
---
title: 使用 Active Directory 授權存取 blob 和佇列
titleSuffix: Azure Storage
description: 使用 Azure Active Directory 授權存取 Azure blob 和佇列。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 2/23/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 5a5417d20229356eb2a0574a35b8a5a884e50193
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84807625"
---
# <a name="authorize-access-to-blobs-and-queues-using-azure-active-directory"></a>使用 Azure Active Directory 授權存取 blob 和佇列

Azure 儲存體支援使用 Azure Active Directory （Azure AD）來授權對 Blob 和佇列儲存體的要求。 使用 Azure AD，您可以使用角色型存取控制（RBAC），將許可權授與安全性主體，這可能是使用者、群組或應用程式服務主體。 安全性主體會由 Azure AD 進行驗證，以傳回 OAuth 2.0 權杖。 然後，您可以使用此權杖來授權對 Blob 或佇列儲存體的要求。

使用 Azure AD 對 Azure 儲存體授權要求，可透過共用金鑰授權提供更優異的安全性和易用性。 Microsoft 建議您盡可能在 blob 和佇列應用程式中使用 Azure AD 授權，以將共用金鑰中固有的潛在安全性弱點降至最低。

具有 Azure AD 的授權適用于所有公用區域和國家雲端中的所有一般用途和 Blob 儲存體帳戶。 只有使用 Azure Resource Manager 部署模型所建立的儲存體帳戶才支援 Azure AD 授權。

Blob 儲存體額外支援建立以 Azure AD 認證簽署的共用存取簽章（SAS）。 如需詳細資訊，請參閱[使用共用存取簽章授與資料的有限存取權](storage-sas-overview.md)。

Azure 檔案儲存體針對已加入網域的 Vm，透過 SMB 支援 AD （預覽）或 Azure AD DS （GA）的授權。 若要瞭解如何使用 AD （預覽）或透過 SMB 的 Azure AD DS （GA）來 Azure 檔案儲存體，請參閱[Azure 檔案儲存體以身分識別為基礎的驗證支援 smb 存取](../files/storage-files-active-directory-overview.md)。

Azure 資料表儲存體不支援具有 Azure AD 的授權。 使用共用金鑰來授權對資料表儲存體的要求。

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>Blob 和佇列的 Azure AD 總覽

當安全性主體（使用者、群組或應用程式）嘗試存取 blob 或佇列資源時，必須授權要求，除非它是可供匿名存取的 blob。 使用 Azure AD，對資源的存取是兩個步驟的程序。 首先，安全性主體的身分識別已通過驗證，並傳回 OAuth 2.0 權杖。 接下來，權杖會當做要求的一部分傳遞至 Blob 或佇列服務，並由服務用來授權存取指定的資源。

驗證步驟要求應用程式在執行時間要求 OAuth 2.0 存取權杖。 如果應用程式是從 azure 實體（例如 Azure VM、虛擬機器擴展集或 Azure Functions 應用程式）中執行，它可以使用[受控識別](../../active-directory/managed-identities-azure-resources/overview.md)來存取 blob 或佇列。 若要瞭解如何授權受控識別對 Azure Blob 或佇列服務提出的要求，請參閱[使用適用于 Azure 資源的 Azure Active Directory 和受控識別來授權對 blob 和佇列的存取](storage-auth-aad-msi.md)。

授權步驟需要將一或多個 RBAC 角色指派給安全性主體。 Azure 儲存體提供的 RBAC 角色包含 blob 和佇列資料的通用許可權集。 指派給安全性主體的角色會決定主體將擁有的許可權。 若要深入瞭解如何指派 Azure 儲存體的 RBAC 角色，請參閱[使用 Rbac 管理儲存體資料的存取權限](storage-auth-aad-rbac.md)。

向 Azure Blob 或佇列服務提出要求的原生應用程式和 web 應用程式，也可以使用 Azure AD 授權存取。 若要瞭解如何要求存取權杖，並使用它來授權 blob 或佇列資料的要求，請參閱[使用 Azure 儲存體應用程式的 Azure AD 授權存取 Azure 儲存體](storage-auth-aad-app.md)。

## <a name="assign-rbac-roles-for-access-rights"></a>指派存取權限的 RBAC 角色

Azure Active Directory (Azure AD) 會透過[角色型存取控制 (RBAC)](../../role-based-access-control/overview.md)，來授與存取受保護資源的權限。 Azure 儲存體會定義一組內建的 RBAC 角色，其中包含用來存取 blob 和佇列資料的常用許可權集。 您也可以定義自訂角色以存取 blob 和佇列資料。

當 RBAC 角色指派給 Azure AD 安全性主體時，Azure 會為該安全性主體授與這些資源的存取權。 存取權的範圍可以包括訂用帳戶、資源群組、儲存體帳戶或個別的容器或佇列層級。 Azure AD 的安全性主體可以是使用者、群組、應用程式服務主體，或[適用于 Azure 資源的受控識別](../../active-directory/managed-identities-azure-resources/overview.md)。

### <a name="built-in-rbac-roles-for-blobs-and-queues"></a>適用于 blob 和佇列的內建 RBAC 角色

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

若要瞭解如何將內建的 RBAC 角色指派給安全性主體，請參閱下列其中一篇文章：

- [在 Azure 入口網站中使用 RBAC 授與 Azure Blob 和佇列資料的存取權](storage-auth-aad-rbac-portal.md)
- [透過 Azure CLI 使用 RBAC 授與 Azure Blob 和佇列資料的存取權](storage-auth-aad-rbac-cli.md)
- [透過 PowerShell 使用 RBAC 授與 Azure Blob 和佇列資料的存取權](storage-auth-aad-rbac-powershell.md)

如需有關如何為 Azure 儲存體定義內建角色的詳細資訊，請參閱[了解角色定義](../../role-based-access-control/role-definitions.md#management-and-data-operations)。 如需建立自訂 RBAC 角色的詳細資訊，請參閱[建立 Azure 角色型存取控制的自訂角色](../../role-based-access-control/custom-roles.md)。

### <a name="access-permissions-for-data-operations"></a>資料作業的存取權限

如需呼叫特定 Blob 或佇列服務作業所需許可權的詳細資訊，請參閱[呼叫 Blob 和佇列資料作業的許可權](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)。

## <a name="resource-scope"></a>資源範圍

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="access-data-with-an-azure-ad-account"></a>使用 Azure AD 帳戶存取資料

透過 Azure 入口網站、PowerShell 或 Azure CLI 存取 blob 或佇列資料，可以使用使用者的 Azure AD 帳戶，或使用帳戶存取金鑰（共用金鑰授權）來授權。

### <a name="data-access-from-the-azure-portal"></a>從 Azure 入口網站存取資料

Azure 入口網站可以使用您的 Azure AD 帳戶或帳戶存取金鑰來存取 Azure 儲存體帳戶中的 blob 和佇列資料。 Azure 入口網站使用的授權配置取決於指派給您的 RBAC 角色。

當您嘗試存取 blob 或佇列資料時，Azure 入口網站會先檢查您是否已獲指派 RBAC 角色的**Microsoft. Storage/storageAccounts/listkeys/action**。 如果您已使用此動作指派角色，則 Azure 入口網站會使用帳戶金鑰，透過共用金鑰授權來存取 blob 和佇列資料。 如果您尚未使用此動作指派角色，則 Azure 入口網站會嘗試使用您的 Azure AD 帳戶來存取資料。

若要使用您的 Azure AD 帳戶從 Azure 入口網站存取 blob 或佇列資料，您需要存取 blob 和佇列資料的許可權，而且您也需要有許可權才能流覽 Azure 入口網站中的儲存體帳戶資源。 Azure 儲存體提供的內建角色會授與對 blob 和佇列資源的存取權，但不會授與許可權給儲存體帳戶資源。 基於這個理由，入口網站的存取權也需要指派 Azure Resource Manager 角色，例如「[讀取](../../role-based-access-control/built-in-roles.md#reader)者」角色，範圍限定在儲存體帳戶層級或更高等級。 「**讀取**者」角色會授與最受限制的許可權，但也可接受另一個授與存取儲存體帳戶管理資源的 Azure Resource Manager 角色。 若要深入瞭解如何使用 Azure AD 帳戶將許可權指派給使用者以在 Azure 入口網站中進行資料存取，請參閱[在 Azure 入口網站中使用 RBAC 授與 Azure blob 和佇列資料的存取權](storage-auth-aad-rbac-portal.md)。

Azure 入口網站指出當您流覽至容器或佇列時，所使用的授權配置。 如需入口網站中資料存取的詳細資訊，請參閱[使用 Azure 入口網站來存取 blob 或佇列資料](storage-access-blobs-queues-portal.md)。

### <a name="data-access-from-powershell-or-azure-cli"></a>從 PowerShell 或 Azure CLI 進行資料存取

Azure CLI 和 PowerShell 支援使用 Azure AD 認證進行登入。 登入之後，您的會話會在這些認證下執行。 若要深入瞭解，請參閱[使用 Azure AD 認證來執行 Azure CLI 或 PowerShell 命令，以存取 blob 或佇列資料](authorize-active-directory-powershell.md)。

## <a name="next-steps"></a>後續步驟

- [使用適用于 Azure 資源的 Azure Active Directory 和受控識別，授權對 blob 和佇列的存取](storage-auth-aad-msi.md)
- [使用應用程式中的 Azure Active Directory 進行授權，以存取 blob 和佇列](storage-auth-aad-app.md)
- [Azure Active Directory 型存取控制的 Azure 儲存體支援已正式運作](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)

---
title: 使用活動目錄授權訪問 Blob 和佇列
titleSuffix: Azure Storage
description: 使用 Azure 活動目錄授權訪問 Azure Blob 和佇列。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 2/23/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: b8a42723a9b56665160e660c0ea1451253c3d185
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255362"
---
# <a name="authorize-access-to-blobs-and-queues-using-azure-active-directory"></a>使用 Azure 活動目錄授權對 Blob 和佇列的訪問

Azure 存儲支援使用 Azure 活動目錄 （Azure AD） 授權對 Blob 和佇列存儲的請求。 使用 Azure AD，可以使用基於角色的存取控制 （RBAC） 向安全主體授予許可權，安全主體可以是使用者、組或應用程式服務主體。 安全主體由 Azure AD 進行身份驗證，以返回 OAuth 2.0 權杖。 然後，該權杖可用於授權針對 Blob 或佇列存儲的請求。

通過 Azure AD 授權針對 Azure 存儲的請求，與共享金鑰授權一起提供更高的安全性和易用性。 Microsoft 建議盡可能將 Azure AD 授權與 blob 和佇列應用程式一起使用，以儘量減少共用金鑰中固有的潛在安全性漏洞。

使用 Azure AD 授權可用於所有公共區域和國家雲中的所有通用和 Blob 存儲帳戶。 只有使用 Azure 資源管理器部署模型創建的存儲帳戶支援 Azure AD 授權。

Blob 存儲還支援創建使用 Azure AD 憑據簽名的共用訪問簽名 （SAS）。 有關詳細資訊，請參閱[授予對具有共用訪問簽名的資料的有限存取權限](storage-sas-overview.md)。

Azure 檔支援僅通過 SMB 授權 AD（預覽）或 Azure AD DS （GA） 以支援加入域的 VM。 要瞭解如何在 SMB 上使用 AD（預覽）或 Azure AD DS （GA） 用於 Azure 檔，請參閱基於[SMB 訪問的基於 Azure 檔的身份驗證支援的概述](../files/storage-files-active-directory-overview.md)。

Azure 表存儲不支援使用 Azure AD 授權。 使用共用金鑰授權對表存儲的請求。

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>Blob 和佇列的 Azure AD 概述

當安全主體（使用者、組或應用程式）嘗試訪問 Blob 或佇列資源時，必須授權該請求，除非它是可供匿名存取的 Blob。 使用 Azure AD，訪問資源是一個兩步過程。 首先，安全主體的標識經過身份驗證，並返回 OAuth 2.0 權杖。 接下來，權杖作為請求的一部分傳遞給 Blob 或佇列服務，並由服務用於授權對指定資源的訪問。

身份驗證步驟要求應用程式在運行時請求 OAuth 2.0 訪問權杖。 如果應用程式在 Azure 實體（如 Azure VM、虛擬機器縮放集或 Azure 函數應用）內運行，則可以使用[託管標識](../../active-directory/managed-identities-azure-resources/overview.md)訪問 Blob 或佇列。 要瞭解如何將託管標識發出的請求授權給 Azure Blob 或佇列服務，請參閱[授權訪問具有 Azure 活動目錄和 Azure 資源託管標識的 Blob 和佇列](storage-auth-aad-msi.md)。

授權步驟要求將一個或多個 RBAC 角色指派給安全主體。 Azure 存儲提供 RBAC 角色，這些角色包含 Blob 和佇列資料的常見許可權集。 分配給安全主體的角色確定主體將具有的許可權。 要瞭解有關為 Azure 存儲分配 RBAC 角色的更多詳細資訊，請參閱[使用 RBAC 管理存儲資料的存取權限](storage-auth-aad-rbac.md)。

向 Azure Blob 或佇列服務發出請求的本機應用程式和 Web 應用程式也可以授權使用 Azure AD 進行訪問。 要瞭解如何請求訪問權杖並使用它授權對 Blob 或佇列資料的請求，請參閱[授權從 Azure 存儲應用程式使用 Azure AD 訪問 Azure 存儲](storage-auth-aad-app.md)。

## <a name="assign-rbac-roles-for-access-rights"></a>為存取權限分配 RBAC 角色

Azure Active Directory (Azure AD) 會透過[角色型存取控制 (RBAC)](../../role-based-access-control/overview.md)，來授與存取受保護資源的權限。 Azure 存儲定義一組內置 RBAC 角色，這些角色包含用於訪問 Blob 和佇列資料的常見許可權集。 您還可以定義自訂角色以訪問 Blob 和佇列資料。

將 RBAC 角色指派給 Azure AD 安全主體時，Azure 會授予對該安全主體的這些資源的存取權限。 存取權的範圍可以包括訂用帳戶、資源群組、儲存體帳戶或個別的容器或佇列層級。 Azure AD 安全主體可以是 Azure 資源的使用者、組、應用程式服務主體或[託管標識](../../active-directory/managed-identities-azure-resources/overview.md)。

### <a name="built-in-rbac-roles-for-blobs-and-queues"></a>用於 blob 和佇列的內置 RBAC 角色

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

要瞭解如何將內置 RBAC 角色指派給安全主體，請參閱以下文章之一：

- [在 Azure 入口網站中使用 RBAC 授與 Azure Blob 和佇列資料的存取權](storage-auth-aad-rbac-portal.md)
- [透過 Azure CLI 使用 RBAC 授與 Azure Blob 和佇列資料的存取權](storage-auth-aad-rbac-cli.md)
- [透過 PowerShell 使用 RBAC 授與 Azure Blob 和佇列資料的存取權](storage-auth-aad-rbac-powershell.md)

如需有關如何為 Azure 儲存體定義內建角色的詳細資訊，請參閱[了解角色定義](../../role-based-access-control/role-definitions.md#management-and-data-operations)。 有關創建自訂 RBAC 角色的資訊，請參閱[為 Azure 基於角色的存取控制創建自訂角色](../../role-based-access-control/custom-roles.md)。

### <a name="access-permissions-for-data-operations"></a>資料操作的存取權限

有關調用特定 Blob 或佇列服務操作所需的許可權的詳細資訊，請參閱[調用 Blob 和佇列資料操作的許可權](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)。

## <a name="resource-scope"></a>資源範圍

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="access-data-with-an-azure-ad-account"></a>使用 Azure AD 帳戶訪問資料

可通過 Azure 門戶、PowerShell 或 Azure CLI 訪問 Blob 或佇列資料，可以通過使用者的 Azure AD 帳戶或使用帳戶訪問金鑰（共用金鑰授權）進行授權。

### <a name="data-access-from-the-azure-portal"></a>從 Azure 門戶進行資料訪問

Azure 門戶可以使用 Azure AD 帳戶或帳戶訪問金鑰訪問 Azure 存儲帳戶中的 Blob 和佇列資料。 Azure 門戶使用哪個授權方案取決於分配給你的 RBAC 角色。

當您嘗試訪問 Blob 或佇列資料時，Azure 門戶首先檢查您是否在**Microsoft**中分配了 RBAC 角色。 如果已為此操作分配了角色，則 Azure 門戶將使用帳戶金鑰通過共用金鑰授權訪問 Blob 和佇列資料。 如果未為此操作分配角色，則 Azure 門戶將嘗試使用 Azure AD 帳戶訪問資料。

要使用 Azure AD 帳戶訪問 Azure 門戶中的 Blob 或佇列資料，需要訪問 Blob 和佇列資料的許可權，並且還需要許可權才能在 Azure 門戶中流覽存儲帳戶資源。 Azure 存儲提供的內置角色授予對 Blob 和佇列資源的許可權，但它們不授予存儲帳戶資源的許可權。 因此，訪問門戶還需要分配 Azure 資源管理器角色，如[Reader](../../role-based-access-control/built-in-roles.md#reader)角色，範圍可限定為存儲帳戶級別或更高級別。 **Reader**角色授予受限制的許可權最多，但授予對存儲帳戶管理資源存取權限的另一個 Azure 資源管理器角色也是可以接受的。 要詳細瞭解如何使用 Azure AD 帳戶在 Azure 門戶中為數據訪問使用者分配許可權，請參閱[在 Azure 門戶中使用 RBAC 授予對 Azure Blob 和佇列資料的存取權限](storage-auth-aad-rbac-portal.md)。

Azure 門戶指示導航到容器或佇列時正在使用的授權方案。 有關門戶中的資料訪問的詳細資訊，請參閱[使用 Azure 門戶訪問 Blob 或佇列資料](storage-access-blobs-queues-portal.md)。

### <a name="data-access-from-powershell-or-azure-cli"></a>從 PowerShell 或 Azure CLI 進行的資料訪問

Azure CLI 和 PowerShell 支援使用 Azure AD 憑據登錄。 登錄後，會話將在這些憑據下運行。 要瞭解更多資訊，請參閱[使用 Azure AD 憑據運行 Azure CLI 或 PowerShell 命令以訪問 Blob 或佇列資料](authorize-active-directory-powershell.md)。

## <a name="next-steps"></a>後續步驟

- [使用 Azure 活動目錄和 Azure 資源的託管標識授權對 Blob 和佇列的訪問](storage-auth-aad-msi.md)
- [使用應用程式中的 Azure 活動目錄授權訪問 Blob 和佇列](storage-auth-aad-app.md)
- [Azure 存儲支援基於 Azure 活動目錄的訪問控制項通常可用](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)

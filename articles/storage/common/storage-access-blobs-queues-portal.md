---
title: 使用 Azure 門戶訪問 Blob 或佇列資料
titleSuffix: Azure Storage
description: 使用 Azure 門戶訪問 Blob 或佇列資料時，門戶會在封面下對 Azure 存儲發出請求。 可以使用 Azure AD 帳戶或存儲帳戶訪問金鑰組這些對 Azure 存儲的請求進行身份驗證和授權。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/10/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 602be49ef0c60274f1cd016c4f8e870cf033ec7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75866902"
---
# <a name="use-the-azure-portal-to-access-blob-or-queue-data"></a>使用 Azure 門戶訪問 Blob 或佇列資料

使用[Azure 門戶](https://portal.azure.com)訪問 Blob 或佇列資料時，門戶在封面下向 Azure 存儲發出請求。 可以使用 Azure AD 帳戶或存儲帳戶訪問金鑰授權對 Azure 存儲的請求。 門戶指示您正在使用的方法，並使您能夠在兩者之間切換（如果您具有適當的許可權）。  

## <a name="permissions-needed-to-access-blob-or-queue-data"></a>訪問 Blob 或佇列資料所需的許可權

根據授權訪問 Azure 門戶中的 Blob 或佇列資料的方式，需要特定許可權。 在大多數情況下，這些許可權是通過基於角色的存取控制 （RBAC） 提供的。 有關 RBAC 的詳細資訊，請參閱[什麼是基於角色的存取控制 （RBAC）？](../../role-based-access-control/overview.md)

### <a name="account-access-key"></a>帳戶訪問金鑰

要使用帳戶訪問金鑰訪問 Blob 和佇列資料，您必須為您分配 RBAC 角色，其中包括 RBAC 操作**Microsoft.存儲/存儲帳戶/清單鍵/操作**。 此 RBAC 角色可以是內置角色或自訂角色。 支援**Microsoft.存儲/存儲帳戶/清單鍵/操作**的內置角色包括：

- Azure 資源管理器[擁有者](../../role-based-access-control/built-in-roles.md#owner)角色
- Azure 資源管理器[參與者](../../role-based-access-control/built-in-roles.md#contributor)角色
- [存儲帳戶參與者](../../role-based-access-control/built-in-roles.md#storage-account-contributor)角色

當您嘗試訪問 Azure 門戶中的 Blob 或佇列資料時，門戶首先檢查您是否在**Microsoft**中分配了角色。 如果已為此操作分配了角色，則門戶將使用帳戶金鑰訪問 Blob 和佇列資料。 如果未為此操作分配角色，則門戶將嘗試使用 Azure AD 帳戶訪問資料。

> [!NOTE]
> 經典訂閱管理員角色服務管理員和共同管理員包括等效于 Azure 資源管理器[擁有者](../../role-based-access-control/built-in-roles.md#owner)角色。 **擁有者**角色包括所有操作，包括**Microsoft.存儲/存儲帳戶/清單鍵/操作**，因此具有這些管理角色之一的使用者也可以使用帳戶金鑰訪問 Blob 和佇列資料。 有關詳細資訊，請參閱[經典訂閱管理員角色](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)。

### <a name="azure-ad-account"></a>Azure AD 帳戶

要使用 Azure AD 帳戶訪問 Azure 門戶中的 Blob 或佇列資料，以下兩個語句都必須為 true：

- 已分配 Azure 資源管理器[讀取器](../../role-based-access-control/built-in-roles.md#reader)角色，至少限定為存儲帳戶級別或更高級別。 **Reader**角色授予受限制的許可權最多，但授予對存儲帳戶管理資源存取權限的另一個 Azure 資源管理器角色也是可以接受的。
- 已為您分配了提供對 Blob 或佇列資料訪問的內置或自訂角色。

讀取**器**角色指派或其他 Azure 資源管理器角色指派是必需的，以便使用者可以在 Azure 門戶中查看和導航存儲帳戶管理資源。 授予對 Blob 或佇列資料存取權限的 RBAC 角色不授予對存儲帳戶管理資源的訪問。 要訪問門戶中的 Blob 或佇列資料，使用者需要許可權來導航存儲帳戶資源。 有關此要求的詳細資訊，請參閱[為門戶訪問分配讀取器角色](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access)。

支援訪問 Blob 或佇列資料的內置角色包括：

- [存儲 Blob 資料擁有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)：用於 Azure 資料湖存儲第 2 代的 POSIX 存取控制。
- [存儲 Blob 資料參與者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)：Blob 的讀取/寫入/刪除許可權。
- [存儲 Blob 資料讀取器](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader)：Blob 的唯讀許可權。
- [存儲佇列資料參與者](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor)：佇列的讀取/寫入/刪除許可權。
- [存儲佇列資料讀取器](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader)：佇列的唯讀許可權。
    
自訂角色可以支援內置角色提供的相同許可權的不同組合。 有關創建自訂 RBAC 角色的詳細資訊，請參閱[Azure 資源的自訂角色](../../role-based-access-control/custom-roles.md)[，並瞭解 Azure 資源的角色定義](../../role-based-access-control/role-definitions.md)。

不支援列出具有經典訂閱管理員角色的佇列。 要列出佇列，使用者必須為其分配 Azure 資源管理器**讀取器**角色、**存儲佇列資料讀取器**角色或**存儲佇列資料參與者**角色。

> [!IMPORTANT]
> Azure 門戶中的存儲資源管理器預覽版本不支援使用 Azure AD 憑據查看和修改 Blob 或佇列資料。 Azure 門戶中的存儲資源管理器始終使用帳戶金鑰訪問資料。 要在 Azure 門戶中使用存儲資源管理器，必須為角色指派包括**Microsoft.存儲/存儲帳戶/清單鍵/操作**。

## <a name="navigate-to-blobs-or-queues-in-the-portal"></a>導航到門戶中的 Blob 或佇列

要查看門戶中的 Blob 或佇列資料，請導航到存儲帳戶的 **"概述"，** 然後按一下**Blob**或**佇列**的連結。 或者，您也可以導航到功能表中的**Blob 服務和****佇列服務**部分。 

![導航到 Azure 門戶中的 Blob 或佇列資料](media/storage-access-blobs-queues-portal/blob-queue-access.png)

## <a name="determine-the-current-authentication-method"></a>確定當前身份驗證方法

導航到容器或佇列時，Azure 門戶指示您當前是使用帳戶訪問金鑰還是使用 Azure AD 帳戶進行身份驗證。

本節中的示例顯示訪問容器及其 Blob，但在訪問佇列及其消息或列出佇列時，門戶將顯示相同的消息。

### <a name="account-access-key"></a>帳戶訪問金鑰

如果使用帳戶訪問金鑰進行身份驗證，您將看到門戶中指定為身份驗證方法**的訪問金鑰**：

![當前使用帳戶金鑰訪問容器資料](media/storage-access-blobs-queues-portal/auth-method-access-key.png)

要切換到使用 Azure AD 帳戶，請按一下圖像中突出顯示的連結。 如果您通過分配給你的 RBAC 角色具有適當的許可權，您將能夠繼續。 但是，如果您缺少正確的許可權，您將看到一條錯誤訊息，如下所示：

![如果 Azure AD 帳戶不支援訪問，則顯示錯誤](media/storage-access-blobs-queues-portal/auth-error-azure-ad.png)

請注意，如果 Azure AD 帳戶沒有查看這些 Blob 的許可權，則清單中不會顯示任何 Blob。 按一下 **"切換"以訪問金鑰**連結，再次使用訪問金鑰進行身份驗證。

### <a name="azure-ad-account"></a>Azure AD 帳戶

如果使用 Azure AD 帳戶進行身份驗證，您將看到門戶中指定為身份驗證方法的**Azure AD 使用者帳戶**：

![當前使用 Azure AD 帳戶訪問容器資料](media/storage-access-blobs-queues-portal/auth-method-azure-ad.png)

要切換到使用帳戶訪問金鑰，請按一下圖像中突出顯示的連結。 如果您有權存取帳戶金鑰，則可以繼續。 但是，如果您無法存取帳戶金鑰，您將看到一條錯誤訊息，如下所示：

![如果您無法存取帳戶金鑰，則顯示錯誤](media/storage-access-blobs-queues-portal/auth-error-access-key.png)

請注意，如果您無法存取帳戶金鑰，則清單中不會顯示任何 Blob。 按一下"**切換到 Azure AD 使用者帳戶"** 連結，再次使用 Azure AD 帳戶進行身份驗證。

## <a name="next-steps"></a>後續步驟

- [使用 Azure 活動目錄對 Azure Blob 和佇列的訪問進行身份驗證](storage-auth-aad.md)
- [在 Azure 門戶中使用 RBAC 授予對 Azure 容器和佇列的存取權限](storage-auth-aad-rbac-portal.md)
- [透過 Azure CLI 使用 RBAC 授與 Azure Blob 和佇列資料的存取權](storage-auth-aad-rbac-cli.md)
- [透過 PowerShell 使用 RBAC 授與 Azure Blob 和佇列資料的存取權](storage-auth-aad-rbac-powershell.md)

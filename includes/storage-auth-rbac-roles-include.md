---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/17/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: d73bab76860abf3e9fa442dad44e1ddb66d147a3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80519551"
---
Azure 提供下列內建 RBAC 角色，以使用 Azure AD 和 OAuth 來授權 blob 和佇列資料的存取：

- [儲存體 Blob 資料擁有](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner)者：用來設定 Azure Data Lake Storage Gen2 的擁有權和管理 POSIX 存取控制。 如需詳細資訊，請參閱 [Azure Data Lake Storage Gen2 中的存取控制](../articles/storage/blobs/data-lake-storage-access-control.md)。
- [儲存體 Blob 資料參與者](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor)：用來授與 Blob 儲存體資源的讀取/寫入/刪除許可權。
- [儲存體 Blob 資料讀取器](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader)：用來授與 Blob 儲存體資源的唯讀許可權。
- [儲存體佇列資料參與者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor)：用來授與 Azure 佇列的讀取/寫入/刪除許可權。
- [儲存體佇列資料讀取器](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader)：用來授與 Azure 佇列的唯讀許可權。
- [儲存體佇列資料訊息處理器](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor)：用來授與 Azure 儲存體佇列中之訊息的查看、取得和刪除許可權。
- [儲存體佇列資料訊息寄件者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender)：用來將新增許可權授與 Azure 儲存體佇列中的訊息。

如需適用于資料服務和管理服務之 Azure 儲存體內建 RBAC 角色的詳細資訊，請參閱 azure [RBAC 的 azure 內建角色](../articles/role-based-access-control/built-in-roles.md#storage)中的**儲存體**一節。 此外，如需在 Azure 中提供許可權之不同角色類型的詳細資訊，請參閱[傳統訂用帳戶管理員角色、AZURE RBAC 角色和 Azure AD 角色](../articles/role-based-access-control/rbac-and-directory-admin-roles.md)。

> [!NOTE]
> RBAC 角色指派最多可能需要五分鐘的時間來傳播。
>
> 只有明確定義來進行資料存取的角色，才會允許安全性主體存取 blob 或佇列資料。 「**擁有**者」、「**參與者**」和「**儲存體帳戶參與者**」等角色允許安全性主體來管理儲存體帳戶，但不提供存取權給該帳戶內的 blob 或佇列資料。
>
> 您可以使用您的 Azure AD 帳戶或儲存體帳戶存取金鑰，來授權存取 Azure 入口網站中的 blob 或佇列資料。 如需詳細資訊，請參閱[使用 Azure 入口網站來存取 blob 或佇列資料](../articles/storage/common/storage-access-blobs-queues-portal.md)。

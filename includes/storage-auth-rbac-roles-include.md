---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/06/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: bc5d4e5a8a9607f74eb8733855a216b83520ff11
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87503698"
---
Azure 提供下列 Azure 內建角色，以使用 Azure AD 和 OAuth 來授權 Blob 和佇列資料的存取權：

- [儲存體 Blob 資料擁有者](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner)：用來設定擁有權和管理 Azure Data Lake Storage Gen2 的 POSIX 存取控制。 如需詳細資訊，請參閱 [Azure Data Lake Storage Gen2 中的存取控制](../articles/storage/blobs/data-lake-storage-access-control.md)。
- [儲存體 Blob 資料參與者](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor)：用來授與 Blob 儲存體資源的讀取/寫入/刪除權限。
- [儲存體 Blob 資料讀者](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader)：用來授與 Blob 儲存體資源的唯讀權限。
- [儲存體 Blob 委派者](../articles/role-based-access-control/built-in-roles.md#storage-blob-delegator)：取得使用者委派金鑰，以針對使用 Azure AD 認證所簽署的容器或 Blob，建立共用存取簽章。
- [儲存體佇列資料參與者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor)：用來授與 Azure 佇列的讀取/寫入/刪除權限。
- [儲存體佇列資料讀者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader)：用來授與 Azure 佇列的唯讀權限。
- [儲存體佇列資料訊息處理者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor)：用來授與 Azure 儲存體佇列中訊息的瞄核、取出和刪除權限。
- [儲存體佇列資料訊息傳送者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender)：用來授與 Azure 儲存體佇列中訊息的新增權限。

只有針對資料存取明確定義的角色，才能允許安全性主體存取 Blob 或佇列資料。 內建角色 (例如 **擁有者**、**參與者**和**儲存體帳戶參與者**) 允許安全性主體管理儲存體帳戶，但是不會透過 Azure AD 提供該帳戶內 Blob 或佇列資料的存取權。 不過，如果角色包含 **Microsoft.Storage/storageAccounts/listKeys/action**，則獲指派該角色的使用者可以透過具有帳戶存取金鑰的共用金鑰授權來存取儲存體帳戶中的資料。 如需詳細資訊，請參閱[使用 Azure 入口網站來存取 Blob 或佇列資料](../articles/storage/common/storage-access-blobs-queues-portal.md)。

如需有關適用於資料服務和管理服務的 Azure 儲存體 Azure 內建角色的詳細資訊，請參閱 [Azure RBAC 的 Azure 內建角色](../articles/role-based-access-control/built-in-roles.md#storage)中的**儲存體**一節。 此外，如需在 Azure 中提供權限的不同類型角色詳細資訊，請參閱[傳統訂用帳戶管理員角色、Azure 角色和 Azure AD 角色](../articles/role-based-access-control/rbac-and-directory-admin-roles.md)。

> [!IMPORTANT]
> Azure 角色指派可能需要五分鐘的時間傳播。

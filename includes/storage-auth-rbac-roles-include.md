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
ms.openlocfilehash: 4b10955a1d3b85acbcae109836bebc03ec04c72c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "76279603"
---
Azure 提供了以下內置 RBAC 角色，用於使用 Azure AD 和 Auth 授權訪問 Blob 和佇列資料：

- [存儲 Blob 資料擁有者](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner)：用於設置 Azure 資料湖存儲 Gen2 的擁有權和管理 POSIX 存取控制。 如需詳細資訊，請參閱 [Azure Data Lake Storage Gen2 中的存取控制](../articles/storage/blobs/data-lake-storage-access-control.md)。
- [存儲 Blob 資料參與者](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor)：用於向 Blob 存儲資源授予讀取/寫入/刪除許可權。
- [存儲 Blob 資料讀取器](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader)：用於向 Blob 存儲資源授予唯讀許可權。
- [存儲佇列資料參與者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor)：用於向 Azure 佇列授予讀取/寫入/刪除許可權。
- [存儲佇列資料讀取器](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader)：用於向 Azure 佇列授予唯讀許可權。
- [存儲佇列資料消息處理器](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor)：用於向 Azure 存儲佇列中的消息授予查看、檢索和刪除許可權。
- [存儲佇列資料消息寄件者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender)：用於向 Azure 存儲佇列中的郵件授予添加許可權。

> [!NOTE]
> RBAC 角色指派可能需要長達五分鐘才能傳播。
>
> 只有明確定義用於資料訪問的角色才允許安全主體訪問 Blob 或佇列資料。 **擁有者**、**參與者**和**存儲帳戶參與者**等角色允許安全主體管理存儲帳戶，但不提供對該帳戶中的 Blob 或佇列資料的訪問。
>
> 可以使用 Azure AD 帳戶或存儲帳戶訪問金鑰授權訪問 Azure 門戶中的 Blob 或佇列資料。 有關詳細資訊，請參閱使用[Azure 門戶訪問 Blob 或佇列資料](../articles/storage/common/storage-access-blobs-queues-portal.md)。

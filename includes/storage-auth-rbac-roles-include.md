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
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519551"
---
Azure 提供以下內建 RBAC 角色,用於使用 Azure AD 和 Auth 授權存取 Blob 和佇列資料:

- [儲存 Blob 資料擁有者](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner):用於設定 Azure 資料湖儲存 Gen2 的擁有權和管理 POSIX 存取控制。 如需詳細資訊，請參閱 [Azure Data Lake Storage Gen2 中的存取控制](../articles/storage/blobs/data-lake-storage-access-control.md)。
- [儲存 Blob 資料參與者](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor):用於向 Blob 儲存資源授予讀取/寫入/刪除許可權。
- [儲存 Blob 資料讀取器](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader):用於向 Blob 儲存資源授予唯讀許可權。
- [儲存佇列資料參與者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor):用於向 Azure 佇列授予讀取/寫入/刪除許可權。
- [儲存佇列資料讀取器](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader):用於向 Azure 佇列授予唯讀權限。
- [儲存佇列資料訊息處理器](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor):用於向 Azure 儲存佇列中的消息授予查看、檢索和刪除許可權。
- [儲存佇列資料訊息發件者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender):用於向 Azure 儲存佇列中的郵件授予添加許可權。

有關用於資料服務和管理服務的 Azure 儲存的內建 RBAC 角色的詳細資訊,請參閱[Azure RBAC 內建角色](../articles/role-based-access-control/built-in-roles.md#storage)中的**儲存**部分。 此外,有關在 Azure 中提供權限的不同類型的角色的資訊,請參閱[經典訂閱管理員角色、Azure RBAC 角色和 Azure AD 角色](../articles/role-based-access-control/rbac-and-directory-admin-roles.md)。

> [!NOTE]
> RBAC 角色分配可能需要長達五分鐘才能傳播。
>
> 只有明確定義用於數據訪問的角色才允許安全主體訪問 Blob 或佇列數據。 **擁有者**、**參與者**和**儲存帳戶參與者**等角色允許安全主體管理存儲帳戶,但不提供對該帳戶中的 Blob 或佇列數據的訪問。
>
> 可以使用 Azure AD 帳戶或儲存帳戶訪問金鑰授權訪問 Azure 門戶中的 Blob 或佇列數據。 有關詳細資訊,請參閱使用[Azure 門戶存取 Blob 或佇列資料](../articles/storage/common/storage-access-blobs-queues-portal.md)。

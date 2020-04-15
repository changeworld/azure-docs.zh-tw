---
title: 使用 Azure 門戶存取 Blob 或佇列資料
titleSuffix: Azure Storage
description: 使用 Azure 門戶訪問 Blob 或佇列數據時,門戶會在封面下對 Azure 儲存發出請求。 可以使用 Azure AD 帳戶或儲存帳戶訪問金鑰對這些對 Azure 儲存的請求進行身份驗證和授權。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/14/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: e556e21238db5de7dddce13ea912dae30723fe8c
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383656"
---
# <a name="use-the-azure-portal-to-access-blob-or-queue-data"></a>使用 Azure 門戶存取 Blob 或佇列資料

使用[Azure 門戶](https://portal.azure.com)訪問 Blob 或佇列數據時,門戶在封面下向 Azure 儲存發出請求。 可以使用 Azure AD 帳戶或儲存帳戶訪問金鑰授權對 Azure 儲存的請求。 門戶指示您正在使用的方法,並使您能夠在兩者之間切換(如果您具有適當的許可權)。  

還可以指定如何在 Azure 門戶中授權單個 Blob 上傳操作。 默認情況下,門戶使用已使用的方法來授權 Blob 上載操作,但您可以選擇在上載 Blob 時更改此設置。

## <a name="permissions-needed-to-access-blob-or-queue-data"></a>存取 Blob 或佇列資料所需的權限

根據授權訪問 Azure 門戶中的 Blob 或佇列數據的方式,需要特定許可權。 在大多數情況下,這些許可權是通過基於角色的訪問控制 (RBAC) 提供的。 有關 RBAC 的詳細資訊,請參閱[什麼是基於角色的訪問控制 (RBAC)?](../../role-based-access-control/overview.md)

### <a name="use-the-account-access-key"></a>使用帳號存取金鑰

要使用帳號存取金鑰存取 Blob 和佇列資料,您必須為您分配 RBAC 角色,其中包括 RBAC 操作**Microsoft.儲存/儲存帳戶/清單鍵/操作**。 此 RBAC 角色可以是內建角色或自定義角色。 支援**Microsoft.儲存/儲存帳戶/清單鍵/操作**的內建角色包括:

- Azure 資源管理[者](../../role-based-access-control/built-in-roles.md#owner)角色
- Azure 資源管理員[參與者](../../role-based-access-control/built-in-roles.md#contributor)角色
- [儲存帳戶參與者](../../role-based-access-control/built-in-roles.md#storage-account-contributor)角色

當您嘗試訪問 Azure 門戶中的 Blob 或佇列數據時,門戶首先檢查您是否在**Microsoft**中分配了角色。 如果已為此操作分配了角色,則門戶將使用帳戶密鑰訪問 Blob 和佇列數據。 如果未為此操作分配角色,則門戶將嘗試使用 Azure AD 帳戶訪問數據。

> [!NOTE]
> 經典訂閱管理員角色服務管理員和共同管理員包括等效於 Azure 資源管理器[擁有者](../../role-based-access-control/built-in-roles.md#owner)角色。 **擁有者**角色包括所有操作,包括**Microsoft.儲存/儲存帳戶/清單鍵/操作**,因此具有這些管理角色之一的使用者也可以使用帳戶金鑰訪問 Blob 和佇列數據。 如需詳細資訊，請參閱[傳統訂用帳戶管理員角色、Azure RBAC 角色和 Azure AD 管理員角色](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)。

### <a name="use-your-azure-ad-account"></a>使用 Azure AD 帳號

要使用 Azure AD 帳號存取 Azure 門戶中的 Blob 或佇列資料,以下兩個語句都必須為 true:

- 已分配 Azure 資源管理器[讀取器](../../role-based-access-control/built-in-roles.md#reader)角色,至少限定為儲存帳戶級別或更高級別。 **Reader**角色授予受限制的許可權最多,但授予對存儲帳戶管理資源訪問許可權的另一個 Azure 資源管理器角色也是可以接受的。
- 已為您分配了提供對 Blob 或佇列數據訪問的內置或自定義角色。

讀取**器**角色分配或其他 Azure 資源管理員角色分配是必需的,以便用戶可以在 Azure 門戶中查看和導航存儲帳戶管理資源。 授予對 Blob 或佇列數據訪問許可權的 RBAC 角色不授予對儲存帳戶管理資源的訪問。 要造訪門戶中的 Blob 或佇列數據,使用者需要許可權來導航存儲帳戶資源。 有關此要求的詳細資訊,請參閱[為門戶存取分配讀取器角色](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access)。

支援存取 Blob 或佇列資料的內建角色包括:

- [儲存 Blob 資料擁有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner):用於 Azure 資料湖儲存第 2 代的 POSIX 存取控制。
- [儲存 Blob 資料參與者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor):Blob 的讀取/寫入/刪除許可權。
- [儲存 Blob 資料讀取器](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader):Blob 的唯讀權限。
- [儲存佇列資料參與者](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor):佇列的讀取/寫入/刪除許可權。
- [儲存佇列資料讀取器](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader):佇列的唯讀許可權。

自定義角色可以支援內建角色提供的相同許可權的不同組合。 有關建立自訂 RBAC 角色的詳細資訊,請參閱[Azure 資源的自訂角色](../../role-based-access-control/custom-roles.md)[,並瞭解 Azure 資源的角色定義](../../role-based-access-control/role-definitions.md)。

不支援列出具有經典訂閱管理員角色的佇列。 要列出佇列,用戶必須為其分配 Azure 資源管理器**讀取器**角色、**儲存佇列數據讀取器**角色或**儲存佇列數據參與者**角色。

> [!IMPORTANT]
> Azure 門戶中的儲存資源管理員預覽版本不支援使用 Azure AD 認證和修改 Blob 或佇列資料。 Azure 門戶中的儲存資源管理器始終使用帳戶金鑰訪問數據。 要在 Azure 門戶使用儲存資源管理員,必須為角色分配包括**Microsoft.儲存/儲存帳戶/清單鍵/操作**。

## <a name="navigate-to-blobs-or-queues-in-the-portal"></a>瀏覽到門戶中的 Blob 或佇列

要檢視門戶中的 Blob 或佇列資料,請瀏覽到儲存帳戶的 **「概述」,** 然後單擊**Blob**或**佇列**的連結。 或者,您也可以導航到功能表中的**Blob 服務和****佇列服務**部分。 

![導覽到 Azure 門戶中的 Blob 或佇列資料](media/storage-access-blobs-queues-portal/blob-queue-access.png)

## <a name="determine-the-current-authentication-method"></a>確定目前身份驗證方法

導航到容器或佇列時,Azure 門戶指示您當前是使用帳戶訪問密鑰還是使用 Azure AD 帳戶進行身份驗證。

本節中的示例顯示訪問容器及其 Blob,但在訪問佇列及其消息或列出佇列時,門戶將顯示相同的消息。

### <a name="authenticate-with-the-account-access-key"></a>使用帳號存取金鑰進行認證

如果使用帳號存取金鑰進行身份驗證,您將看到門戶中指定為身份認證方法**的存取金鑰**:

![目前使用帳戶金鑰存取容器資料](media/storage-access-blobs-queues-portal/auth-method-access-key.png)

要切換到使用 Azure AD 帳戶,請按一下影像中突出顯示的連結。 如果您通過分配給你的 RBAC 角色具有適當的許可權,您將能夠繼續。 但是,如果您缺少正確的許可權,您將看到一條錯誤消息,如下所示:

![如果 Azure AD 帳號不支援存取,則顯示錯誤](media/storage-access-blobs-queues-portal/auth-error-azure-ad.png)

請注意,如果 Azure AD 帳戶沒有查看這些 Blob 的許可權,則列表中不會顯示任何 Blob。 按下 **「切換」 以造訪金鑰**連結,再次使用存取金鑰進行身份驗證。

### <a name="authenticate-with-your-azure-ad-account"></a>使用 Azure AD 帳號進行認證

如果使用 Azure AD 帳號進行身份驗證,您將看到門戶中指定為身份驗證方法的**Azure AD 使用者帳號**:

![目前使用 Azure AD 帳號存取容器資料](media/storage-access-blobs-queues-portal/auth-method-azure-ad.png)

要切換到使用帳戶訪問密鑰,請按一下圖像中突出顯示的連結。 如果您有權訪問帳戶密鑰,則可以繼續。 但是,如果您無法訪問帳戶金鑰,您將看到一條錯誤消息,如下所示:

![如果您無法存取帳號金鑰,則顯示錯誤](media/storage-access-blobs-queues-portal/auth-error-access-key.png)

請注意,如果您無法訪問帳戶密鑰,則列表中不會顯示任何 Blob。 按下「**切換到 Azure AD 使用者帳戶」** 連結,再次使用 Azure AD 帳戶進行身份驗證。

## <a name="specify-how-to-authorize-a-blob-upload-operation"></a>指定如何授權 Blob 上載操作

從 Azure 門戶上傳 Blob 時,可以指定是使用帳戶存取金鑰還是使用 Azure AD 認證對此操作進行身分驗證和授權。 默認情況下,門戶使用當前身份驗證方法,如[確定當前身份驗證方法](#determine-the-current-authentication-method)所示。

要指定如何授權 Blob 上載操作,請按照以下步驟操作:

1. 在 Azure 門戶中,導航到要上傳 Blob 的容器。
1. 選取 [上傳]**** 按鈕。
1. 展開 **「進階」** 部分以顯示 blob 的進階屬性。
1. 在 **「身份驗證類型」** 欄位中,指示是使用 Azure AD 帳戶還是使用帳戶存取金鑰授權上載操作,如下圖所示:

    :::image type="content" source="media/storage-access-blobs-queues-portal/auth-blob-upload.png" alt-text="顯示如何在 Blob 上載時改變授權方法的螢幕擷圖":::

## <a name="next-steps"></a>後續步驟

- [使用 Azure 活動目錄對 Azure Blob 和佇列的存取進行身份驗證](storage-auth-aad.md)
- [在 Azure 門戶中使用 RBAC 授予對 Azure 容器和佇列的存取權限](storage-auth-aad-rbac-portal.md)
- [透過 Azure CLI 使用 RBAC 授與 Azure Blob 和佇列資料的存取權](storage-auth-aad-rbac-cli.md)
- [透過 PowerShell 使用 RBAC 授與 Azure Blob 和佇列資料的存取權](storage-auth-aad-rbac-powershell.md)

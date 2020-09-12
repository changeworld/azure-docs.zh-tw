---
title: 選擇如何授權存取 Azure 入口網站中的佇列資料
titleSuffix: Azure Storage
description: 當您使用 Azure 入口網站來存取佇列資料時，入口網站會在幕後要求 Azure 儲存體。 Azure 儲存體的這些要求可使用您的 Azure AD 帳戶或儲存體帳戶存取金鑰來進行驗證和授權。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/08/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: contperfq1
ms.openlocfilehash: 82e92b672248e2a63f8b2bbf1fc651f617a7027f
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2020
ms.locfileid: "90020307"
---
# <a name="choose-how-to-authorize-access-to-queue-data-in-the-azure-portal"></a>選擇如何授權存取 Azure 入口網站中的佇列資料

當您使用 [Azure 入口網站](https://portal.azure.com)來存取佇列資料時，入口網站會在幕後要求 Azure 儲存體。 您可以使用您的 Azure AD 帳戶或儲存體帳戶存取金鑰來授權 Azure 儲存體要求。 入口網站會指出您要使用的方法，如果您有適當的許可權，可讓您在兩者之間切換。  

## <a name="permissions-needed-to-access-queue-data"></a>存取佇列資料所需的許可權

根據您要如何授權存取 Azure 入口網站中的佇列資料，您將需要特定的許可權。 在大部分情況下，這些許可權是透過角色型存取控制來提供 (RBAC) 。 如需 RBAC 的詳細資訊，請參閱 [什麼是 AZURE rbac) 的 azure 角色型存取控制 (？](../../role-based-access-control/overview.md)。

### <a name="use-the-account-access-key"></a>使用帳戶存取金鑰

若要使用帳戶存取金鑰來存取佇列資料，您必須擁有指派給您的 Azure 角色，其中包含 RBAC 動作 **Microsoft. Storage/storageAccounts/listkeys/action**。 此 Azure 角色可能是內建或自訂角色。 支援 **Microsoft Storage/storageAccounts/listkeys/action** 的內建角色包括：

- Azure Resource Manager [擁有](../../role-based-access-control/built-in-roles.md#owner) 者角色
- Azure Resource Manager [參與者](../../role-based-access-control/built-in-roles.md#contributor) 角色
- [儲存體帳戶參與者](../../role-based-access-control/built-in-roles.md#storage-account-contributor)角色

當您嘗試存取 Azure 入口網站中的佇列資料時，入口網站會先檢查您是否已使用 **Microsoft. Storage/storageAccounts/listkeys/action**指派角色。 如果您已獲指派具有此動作的角色，則入口網站會使用帳戶金鑰來存取佇列資料。 如果您未獲指派具有此動作的角色，則入口網站會嘗試使用您的 Azure AD 帳戶來存取資料。

> [!NOTE]
> 傳統訂用帳戶管理員角色服務系統管理員和共同管理員，包含對等的 Azure Resource Manager [擁有](../../role-based-access-control/built-in-roles.md#owner) 者角色。 **擁有**者角色包含所有動作（包括**Microsoft. Storage/storageAccounts/listkeys/action**），因此具有其中一個系統管理角色的使用者也可以使用帳戶金鑰來存取佇列資料。 如需詳細資訊，請參閱[傳統訂用帳戶管理員角色、Azure 角色和 Azure AD 管理員角色](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)。

### <a name="use-your-azure-ad-account"></a>使用您的 Azure AD 帳戶

若要使用您的 Azure AD 帳戶從 Azure 入口網站存取佇列資料，下列兩個語句都必須為 true：

- 您已獲指派「Azure Resource Manager [讀者](../../role-based-access-control/built-in-roles.md#reader) 」角色的最小值範圍是儲存體帳戶或更高的層級。 **讀取**者角色會授與最受限制的許可權，但也可以接受另一個授與儲存體帳戶管理資源存取權的 Azure Resource Manager 角色。
- 您已獲指派可存取佇列資料的內建或自訂角色。

**讀者**角色指派或其他 Azure Resource Manager 角色指派是必要的，讓使用者可以在 Azure 入口網站中查看和流覽儲存體帳戶管理資源。 授與佇列資料存取權的 Azure 角色並不會授與儲存體帳戶管理資源的存取權。 若要在入口網站中存取佇列資料，使用者需要流覽儲存體帳戶資源的許可權。 如需這項需求的詳細資訊，請參閱 [指派入口網站存取的讀取者角色](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access)。

支援存取佇列資料的內建角色包括：

- [儲存體佇列資料參與者](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor)：佇列的讀取/寫入/刪除許可權。
- [儲存體佇列資料讀取器](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader)：佇列的唯讀許可權。

自訂角色可以支援內建角色所提供之相同許可權的不同組合。 如需有關建立 Azure 自訂角色的詳細資訊，請參閱 [azure 自訂角色](../../role-based-access-control/custom-roles.md) 及 [瞭解 azure 資源的角色定義](../../role-based-access-control/role-definitions.md)。

不支援列出具有傳統訂用帳戶管理員角色的佇列。 若要列出佇列，使用者必須已指派 Azure Resource Manager **讀取** 者角色、 **儲存體佇列資料讀取** 者角色或 **儲存體佇列資料參與者** 角色。

> [!IMPORTANT]
> Azure 入口網站中的儲存體總管預覽版本不支援使用 Azure AD 認證來查看和修改佇列資料。 Azure 入口網站中的儲存體總管一律會使用帳戶金鑰來存取資料。 若要在 Azure 入口網站中使用儲存體總管，您必須被指派包含 **Microsoft storageAccounts/listkeys/action**的角色。

## <a name="navigate-to-queues-in-the-azure-portal"></a>流覽至 Azure 入口網站中的佇列

若要在入口網站中查看佇列資料，請流覽至您儲存體帳戶的 **總覽** ，然後按一下 **佇列**的連結。 或者，您也可以流覽至功能表中的 **佇列服務** 區段。

:::image type="content" source="media/authorize-queue-access-portal/queue-access-portal.png" alt-text="顯示如何流覽至 Azure 入口網站中佇列資料的螢幕擷取畫面":::

## <a name="determine-the-current-authentication-method"></a>判斷目前的驗證方法

當您流覽至佇列時，Azure 入口網站會指出您目前是否正在使用帳戶存取金鑰或您的 Azure AD 帳戶進行驗證。

### <a name="authenticate-with-the-account-access-key"></a>使用帳戶存取金鑰進行驗證

如果您要使用帳戶存取金鑰進行驗證，您會在入口網站中看到指定為驗證方法的 **存取金鑰** ：

:::image type="content" source="media/authorize-queue-access-portal/auth-method-access-key.png" alt-text="顯示使用者目前使用帳戶金鑰存取佇列的螢幕擷取畫面":::

若要切換到使用 Azure AD 帳戶，請按一下影像中反白顯示的連結。 如果您有適當的許可權可透過指派給您的 Azure 角色，您將能夠繼續進行。 但是，如果您沒有正確的許可權，您會看到如下所示的錯誤訊息：

:::image type="content" source="media/authorize-queue-access-portal/auth-error-azure-ad.png" alt-text="如果 Azure AD 帳戶不支援存取，就會顯示錯誤":::

請注意，如果您的 Azure AD 帳戶沒有許可權可加以查看，則清單中不會出現任何佇列。 按一下 [ **切換至存取金鑰** ] 連結，以再次使用存取金鑰進行驗證。

### <a name="authenticate-with-your-azure-ad-account"></a>使用您的 Azure AD 帳戶進行驗證

如果您要使用您的 Azure AD 帳戶進行驗證，您會在入口網站中看到指定為驗證方法 **Azure AD 使用者帳戶** ：

:::image type="content" source="media/authorize-queue-access-portal/auth-method-azure-ad.png" alt-text="顯示使用者目前使用 Azure AD 帳戶存取佇列的螢幕擷取畫面":::

若要切換到使用帳戶存取金鑰，請按一下影像中反白顯示的連結。 如果您有帳戶金鑰的存取權，就可以繼續進行。 但是，如果您沒有帳戶金鑰的存取權，Azure 入口網站會顯示錯誤訊息。

如果您沒有帳戶金鑰的存取權，則佇列不會列在入口網站中。 按一下 [ **切換至 Azure AD 使用者帳戶** ] 連結，以再次使用您的 Azure AD 帳戶進行驗證。

## <a name="next-steps"></a>接下來的步驟

- [使用 Azure Active Directory 來驗證 Azure blob 和佇列的存取權](../common/storage-auth-aad.md)
- [在 Azure 入口網站中使用 RBAC 授與 Azure 容器和佇列的存取權](../common/storage-auth-aad-rbac-portal.md)
- [透過 Azure CLI 使用 RBAC 授與 Azure Blob 和佇列資料的存取權](../common/storage-auth-aad-rbac-cli.md)
- [透過 PowerShell 使用 RBAC 授與 Azure Blob 和佇列資料的存取權](../common/storage-auth-aad-rbac-powershell.md)

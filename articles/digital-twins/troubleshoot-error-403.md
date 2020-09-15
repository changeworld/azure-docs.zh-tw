---
title: 'Azure 數位 Twins 要求失敗，狀態： 403 (禁止) '
description: 「服務要求失敗的原因和解決方式。 狀態： Azure 數位 Twins 上的 403 (禁止的) 」。
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 7/20/2020
ms.openlocfilehash: bc4fbbc265bef00be27c890c3f090a49591dc415
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90562735"
---
# <a name="service-request-failed-status-403-forbidden"></a>服務要求失敗。 狀態： 403 (禁止的) 

本文說明從服務要求至 Azure 數位 Twins 收到403錯誤的原因和解決步驟。 

## <a name="symptoms"></a>徵兆

這項錯誤可能會發生在許多需要驗證的服務要求類型上。 效果是 API 要求失敗，並傳回錯誤狀態 `403 (Forbidden)` 。

## <a name="causes"></a>原因

### <a name="cause-1"></a>原因 #1

最常見的情況是，此錯誤表示您的角色型存取控制 (服務的 RBAC) 許可權未正確設定。 Azure 數位 Twins 實例的許多動作都需要您在**嘗試管理的實例上**擁有*Azure 數位 Twins 擁有者 (預覽版) *角色。 

### <a name="cause-2"></a>原因 #2

如果您使用用戶端應用程式來與 Azure 數位 Twins 通訊，就會發生此錯誤，因為您的 [Azure Active Directory (Azure AD) ](../active-directory/fundamentals/active-directory-whatis.md) 應用程式註冊沒有針對 Azure 數位 Twins 服務設定的許可權。

需要註冊應用程式才能擁有針對 Azure 數位 Twins Api 設定的存取權限。 然後，當您的用戶端應用程式向應用程式註冊進行驗證時，就會被授與應用程式註冊所設定的許可權。

## <a name="solutions"></a>方案

### <a name="solution-1"></a>方案 #1

第一個解決方案是確認您的 Azure 使用者在您嘗試管理的實例上具有 _**Azure 數位 Twins 擁有者 (預覽版) **_ 角色。 如果您沒有此角色，請進行設定。

請注意，此角色不同于 .。。
* 整個 Azure 訂用帳戶的 *擁有* 者角色。 *Azure 數位 Twins 擁有者 (預覽版) * 是 Azure 數位 Twins 內的角色，而且範圍限定為此個別 Azure 數位 Twins 實例。
* Azure 數位 Twins 中的 *擁有* 者角色。 這些是兩個不同的 Azure 數位 Twins 管理角色，而 *Azure 數位 Twins 擁有者 (預覽版) * 是在預覽期間應用於管理的角色。

#### <a name="check-current-setup"></a>檢查目前的設定

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

#### <a name="fix-issues"></a>修正問題 

如果您沒有此角色指派，則您的**azure 訂**用帳戶中具有「擁有者」角色的使用者應該執行下列命令，為您的 azure 使用者授與 Azure 數位 Twins 擁有者在**azure 數位 Twins 實例**上的* (預覽版) *角色。 

如果您是訂用帳戶的擁有者，您可以自行執行此命令。 如果不是，請洽詢擁有者，以代表您執行此命令。

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-Azure-AD-email>" --role "Azure Digital Twins Owner (Preview)"
```

如需有關此角色需求和指派程式的詳細資訊，請參閱 how *to：設定實例和驗證 (CLI 或入口網站) *的「 [*設定使用者的存取權限*」一節](how-to-set-up-instance-CLI.md#set-up-user-access-permissions)。

如果您已經有此角色指派，且仍遇到403問題，請繼續進行下一個方案。

### <a name="solution-2"></a>方案 #2

第二個解決方案是確認 Azure AD 的應用程式註冊有針對 Azure 數位 Twins 服務設定的許可權。 如果未設定，請進行設定。

#### <a name="check-current-setup"></a>檢查目前的設定

[!INCLUDE [digital-twins-setup-verify-app-registration-1.md](../../includes/digital-twins-setup-verify-app-registration-1.md)]

首先，請確認已在註冊時正確設定 Azure 數位 Twins 許可權設定。 若要這樣做，請從功能表列選取 [ *資訊清單* ]，以查看應用程式註冊的資訊清單程式碼。 滾動至程式碼視窗底部，然後在底下尋找這些欄位 `requiredResourceAccess` 。 這些值應該符合以下螢幕擷取畫面中的值：

[!INCLUDE [digital-twins-setup-verify-app-registration-2.md](../../includes/digital-twins-setup-verify-app-registration-2.md)]

#### <a name="fix-issues"></a>修正問題

如果其中任何一項出現的方式不同于所述，請依照 how *to：設定實例和驗證 (CLI 或入口網站) *中的如何在[*設定用戶端應用程式的存取權限*一節](how-to-set-up-instance-cli.md#set-up-access-permissions-for-client-applications)中的指示來設定應用程式註冊。

## <a name="next-steps"></a>後續步驟

請參閱建立和驗證新的 Azure 數位 Twins 實例的設定步驟：
* [*如何： (CLI 設定實例和驗證) *](how-to-set-up-instance-cli.md)

深入瞭解 Azure 數位 Twins 的安全性和許可權：
* [*概念： Azure 數位 Twins 解決方案的安全性*](concepts-security.md)
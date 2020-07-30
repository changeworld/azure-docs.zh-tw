---
title: Azure 數位 Twins 要求失敗，狀態為：403（禁止）
description: 「服務要求失敗的原因和解決方案。 狀態： Azure 數位 Twins 上的403（禁止） '。
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 7/20/2020
ms.openlocfilehash: a971291dd423894e4d04158abe873a7222f9802c
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87405558"
---
# <a name="service-request-failed-status-403-forbidden"></a>服務要求失敗。 狀態：403（禁止）

本文說明從服務要求傳送至 Azure 數位 Twins 的403錯誤的原因和解決步驟。 

## <a name="symptoms"></a>徵狀

這項錯誤可能會發生在需要驗證的許多服務要求類型上。 其效果是 API 要求失敗，並傳回錯誤狀態 `403 (Forbidden)` 。

## <a name="causes"></a>原因

### <a name="cause-1"></a>原因 #1

最常見的情況是，此錯誤表示未正確設定服務的角色型存取控制（RBAC）許可權。 Azure 數位 Twins 實例的許多動作都需要您**在嘗試管理的實例上**擁有*Azure 數位 Twins 擁有者（預覽）* 角色。 

### <a name="cause-2"></a>原因 #2

如果您使用用戶端應用程式來與 Azure 數位 Twins 通訊，就會發生此錯誤，因為您的[Azure Active Directory （Azure AD）](../active-directory/fundamentals/active-directory-whatis.md)應用程式註冊並未設定 Azure 數位 Twins 服務的許可權。

應用程式註冊必須要有設定 Azure 數位 Twins Api 的存取權限。 然後，當您的用戶端應用程式向應用程式註冊進行驗證時，就會被授與應用程式註冊所設定的許可權。

## <a name="solutions"></a>方案

### <a name="solution-1"></a>方案 #1

第一個解決方案是確認您的 Azure 使用者在您嘗試管理的實例上具有_**Azure 數位 Twins 擁有者（預覽）**_ 角色。 如果您沒有此角色，請加以設定。

請注意，此角色不同于 .。。
* 整個 Azure 訂用帳戶的*擁有*者角色。 *Azure 數位 Twins 擁有者（預覽）* 是 Azure 數位 Twins 內的角色，其範圍限於此個別的 Azure 數位 Twins 實例。
* Azure 數位 Twins 中的*擁有*者角色。 這些是兩個不同的 Azure 數位 Twins 管理角色，而*Azure 數位 Twins 擁有者（預覽）* 是在預覽期間應用來進行管理的角色。

#### <a name="check-current-setup"></a>檢查目前的設定

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

#### <a name="fix-issues"></a>修正問題 

如果您沒有此角色指派，則在您的**azure 訂**用帳戶中具有擁有者角色的人員應該執行下列命令，將 Azure**數位 Twins 實例**上的 azure*數位 Twins 擁有者（預覽）* 角色授與您的 azure 使用者。 

如果您是訂用帳戶的擁有者，您可以自行執行此命令。 如果不是，請洽詢擁有者，以代表您執行此命令。

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-Azure-AD-email>" --role "Azure Digital Twins Owner (Preview)"
```

如需有關此角色需求和指派程式的詳細資訊，請參閱*如何：設定實例和驗證（CLI 或入口網站）* 中的[*設定您的使用者存取權限*一節](how-to-set-up-instance-CLI.md#set-up-user-access-permissions)。

如果您已有此角色指派，但仍遇到403問題，請繼續進行下一個解決方案。

### <a name="solution-2"></a>方案 #2

第二個解決方案是確認 Azure AD 應用程式註冊具有為 Azure 數位 Twins 服務設定的許可權。 如果未設定，請加以設定。

#### <a name="check-current-setup"></a>檢查目前的設定

[!INCLUDE [digital-twins-setup-verify-app-registration-1.md](../../includes/digital-twins-setup-verify-app-registration-1.md)]

首先，請確認已正確設定註冊上的 Azure 數位 Twins 許可權設定。 若要這麼做，請從功能表列選取 [*資訊清單*]，以查看應用程式註冊的資訊清單代碼。 在程式碼視窗的底部，尋找中的這些欄位 `requiredResourceAccess` 。 值應符合下列螢幕擷取畫面中的值：

[!INCLUDE [digital-twins-setup-verify-app-registration-2.md](../../includes/digital-twins-setup-verify-app-registration-2.md)]

#### <a name="fix-issues"></a>修正問題

如果這其中任何一項的顯示方式與說明不同，請遵循*如何：設定實例和驗證（CLI 或入口網站）* 的[*設定用戶端應用程式的存取權限*一節](how-to-set-up-instance-cli.md#set-up-access-permissions-for-client-applications)中有關如何設定應用程式註冊的指示。

## <a name="next-steps"></a>後續步驟

閱讀建立和驗證新的 Azure 數位 Twins 實例的設定步驟：
* [*如何：設定實例和驗證（CLI）*](how-to-set-up-instance-cli.md)

深入瞭解 Azure 數位 Twins 的安全性和許可權：
* [*概念： Azure 數位 Twins 解決方案的安全性*](concepts-security.md)
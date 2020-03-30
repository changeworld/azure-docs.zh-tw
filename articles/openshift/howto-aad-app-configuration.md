---
title: Azure 紅帽開放移位的 Azure 活動目錄集成
description: 瞭解如何創建 Azure AD 安全性群組和使用者以在 Microsoft Azure 紅帽 OpenShift 群集上測試應用。
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: a2eade6c5a9c826d28d435a09861ba58463ae8c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280530"
---
# <a name="azure-active-directory-integration-for-azure-red-hat-openshift"></a>Azure 紅帽開放移位的 Azure 活動目錄集成

如果尚未創建 Azure 活動目錄 （Azure AD） 租戶，請按照[Azure 紅帽 OpenShift 創建 Azure AD 租戶](howto-create-tenant.md)的說明操作，然後再繼續這些說明。

Microsoft Azure 紅帽 OpenShift 需要代表群集執行任務的許可權。 如果您的組織還沒有 Azure AD 使用者、Azure AD 安全性群組或 Azure AD 應用註冊用作服務主體，請按照這些說明創建它們。

## <a name="create-a-new-azure-active-directory-user"></a>建立新的 Azure Active Directory 使用者

在[Azure 門戶](https://portal.azure.com)中，確保租戶顯示在門戶右上角的使用者名下：

![右上角列出租戶的門戶螢幕截圖](./media/howto-create-tenant/tenant-callout.png)如果顯示了錯誤的租戶，請按一下右上角的使用者名，然後按一下 **"切換目錄**"，然後從 **"所有目錄"** 清單中選擇正確的租戶。

創建新的 Azure 活動目錄全域管理員使用者以登錄到 Azure 紅帽 OpenShift 群集。

1. 轉到["使用者-所有使用者"](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers)邊欄選項卡。
2. 按一下 **"新建使用者**"以打開 **"使用者"** 窗格。
3. 輸入此使用者**的名稱**。
4. 基於您創建的租戶的名稱創建**使用者名**，並在末尾`.onmicrosoft.com`追加。 例如： `yourUserName@yourTenantName.onmicrosoft.com` 。 記下此使用者名。 您需要它才能登錄到群集。
5. 按一下 **"目錄"角色**以打開目錄角色窗格，然後選擇**全域管理員**，然後按一下窗格底部的 **"確定**"。
6. 在 **"使用者"** 窗格中，按一下"**顯示密碼"** 並記錄臨時密碼。 首次登錄後，系統將提示您重置它。
7. 在窗格的底部，按一下"**創建**"以創建使用者。

## <a name="create-an-azure-ad-security-group"></a>創建 Azure AD 安全性群組

要授予群集管理員存取權限，Azure AD 安全性群組中的成員身份將同步到 OpenShift 組"osa-客戶管理員"。 如果未指定，將不會授予群集管理員存取權限。

1. 打開[Azure 活動目錄組邊](https://portal.azure.com/#blade/Microsoft_AAD_IAM/GroupsManagementMenuBlade/AllGroups)欄選項卡。
2. 按一下 **"新群組**"。
3. 提供組名稱和說明。
4. 將**組類型**設置為 **"安全**"。
5. 將**成員資格類型**設置為**已分配**。

    將在前面的步驟中創建的 Azure AD 使用者添加到此安全性群組。

6. 按一下 **"成員**"以打開 **"選擇成員**"窗格。
7. 在成員清單中，選擇上面創建的 Azure AD 使用者。
8. 在門戶底部，按一下"**選擇"** 然後**創建**以創建安全性群組。

    記下組 ID 值。

9. 創建組時，您將在所有組清單中看到它。 按一下新組。
10. 在顯示的頁面上，向下複製物件**ID**。 我們將引用此值，如`GROUPID`創建 Azure[紅帽 OpenShift 群集](tutorial-create-cluster.md)教程。

> [!IMPORTANT]
> 要將此組與 osa-客戶管理員 OpenShift 組同步，請使用 Azure CLI 創建群集。 Azure 門戶當前缺少一個欄位來設置此組。

## <a name="create-an-azure-ad-app-registration"></a>創建 Azure AD 應用註冊

通過將標誌省略到命令，`--aad-client-app-id`可以自動創建 Azure 活動目錄 （Azure AD） 應用註冊用戶端，作為創建群集`az openshift create`的一部分。 本教程演示如何創建 Azure AD 應用註冊以獲得完整性。

如果您的組織尚未註冊 Azure 活動目錄 （Azure AD） 應用註冊用作服務主體，請按照這些說明創建一個。

1. 打開[應用註冊邊欄選項卡](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview)，然後按一下 **"新建註冊**"。
2. 在 **"註冊應用程式**"窗格中，輸入應用程式註冊的名稱。
3. 確保根據 **"支援"科目類型**，**僅選中此組織目錄中的帳戶**。 這是最安全的選擇。
4. 稍後，我們將在知道群集 URI 後添加重定向 URI。 按一下 **"註冊"** 按鈕可創建 Azure AD 應用程式註冊。
5. 在顯示的頁面上，向下複製**應用程式（用戶端）ID**。 我們將引用此值，如`APPID`創建 Azure[紅帽 OpenShift 群集](tutorial-create-cluster.md)教程。

![應用物件頁面的螢幕截圖](./media/howto-create-tenant/get-app-id.png)

### <a name="create-a-client-secret"></a>建立用戶端密碼

生成用戶端金鑰，用於將應用驗證為 Azure 活動目錄。

1. 在應用註冊頁的 **"管理**"部分中，按一下 **"證書&機密**"。
2. 在 **"證書&機密**"窗格中，按一下 **"新用戶端機密**"。  將顯示 **"添加用戶端機密**"窗格。
3. 提供**說明**。
4. 設置為**您**喜歡的持續時間，例如 **，在 2 年內**。
5. 按一下"**添加**"，鍵值將顯示在頁面的 **"用戶端機密**"部分中。
6. 向下複製鍵值。 我們將引用此值，如`SECRET`創建 Azure[紅帽 OpenShift 群集](tutorial-create-cluster.md)教程。

![證書和機密窗格的螢幕截圖](./media/howto-create-tenant/create-key.png)

有關 Azure 應用程式物件的詳細資訊，請參閱[Azure 活動目錄中的應用程式和服務主體物件](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)。

有關創建新 Azure AD 應用程式的詳細資訊，請參閱[使用 Azure 活動目錄 v1.0 終結點註冊應用](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)。

## <a name="add-api-permissions"></a>新增 API 權限

[//]: # (不要更改為微軟圖形。它不適用於微軟圖形。)
1. 在 **"管理**"部分中按一下**API 許可權**
2. 按一下 **"添加許可權**"並選擇**Azure 活動目錄圖**，然後**委派許可權**。
> [!NOTE]
> 請確保選擇了"Azure 活動目錄圖"，而不是"Microsoft 圖形"磁貼。

3. 展開下面的清單中**的使用者**並啟用**使用者.閱讀**許可權。 如果預設情況下啟用**了 User.Read，** 請確保它是 Azure**活動目錄圖形**許可權 **"使用者"。**
4. 向上滾動並選擇**應用程式許可權**。
5. 展開下面清單中的**目錄**並啟用**目錄。閱讀全部**。
6. 按一下"**添加許可權**"以接受更改。
7. API 許可權面板現在應同時顯示*User.Read*和*目錄。ReadAll*。 請注意*目錄*旁邊的 **"管理員同意"** 列中的警告。
8. 如果您是 Azure*訂閱管理員*，請按一下下面的 **"授予管理員同意*訂閱名稱***"。 如果您不是 Azure*訂閱管理員*，請要求管理員的同意。

![API 許可權面板的螢幕截圖。 使用者.閱讀和目錄.閱讀添加的擁有權限，管理員同意目錄.閱讀所有](./media/howto-aad-app-configuration/permissions-required.png)

> [!IMPORTANT]
> 群集管理員組的同步僅在獲得同意後才起作用。 在 *"管理員同意"要求*列中，您將看到一個綠色圓圈，其中帶有核取記號和消息"訂閱*名稱*授予"。

有關管理管理員和其他角色的詳細資訊，請參閱[添加或更改 Azure 訂閱管理員](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator)。

## <a name="resources"></a>資源

* [Azure 活動目錄中的應用程式和服務主體物件](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)
* [快速入門：向 Azure Active Directory v1.0 端點註冊應用程式](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)

## <a name="next-steps"></a>後續步驟

如果您滿足所有[Azure 紅帽 OpenShift 先決條件](howto-setup-environment.md)，則可以創建第一個群集！

請嘗試本教程：
> [!div class="nextstepaction"]
> [建立 Azure Red Hat OpenShift 叢集](tutorial-create-cluster.md)

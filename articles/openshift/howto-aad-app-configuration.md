---
title: Azure 紅帽開放移位的 Azure 活動目錄整合
description: 瞭解如何創建 Azure AD 安全組和使用者以在 Microsoft Azure 紅帽 OpenShift 群集上測試應用。
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: f6c4fb5caf746650f95872d50afe31e5693422be
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382923"
---
# <a name="azure-active-directory-integration-for-azure-red-hat-openshift"></a>Azure 紅帽開放移位的 Azure 活動目錄整合

如果尚未創建 Azure 活動目錄 (Azure AD) 租戶,請按照[Azure 紅帽 OpenShift 創建 Azure AD 租戶](howto-create-tenant.md)的說明操作,然後再繼續這些說明。

Microsoft Azure 紅帽 OpenShift 需要代表群集執行任務的許可權。 如果您的組織還沒有 Azure AD 使用者、Azure AD 安全組或 Azure AD 應用註冊用作服務主體,請按照這些說明創建它們。

## <a name="create-a-new-azure-active-directory-user"></a>建立新的 Azure Active Directory 使用者

在[Azure 門戶](https://portal.azure.com)中,確保租戶顯示在門戶右上角的使用者名下:

![右上角列出租戶的門戶螢幕截圖](./media/howto-create-tenant/tenant-callout.png)如果顯示了錯誤的租戶,請單擊右上角的使用者名,然後按下 **「切換目錄**」,然後從 **「所有目錄」** 清單中選擇正確的租戶。

創建新的 Azure 活動目錄「所有者」使用者以登錄到 Azure 紅帽 OpenShift 群集。

1. 跳到[「使用者-所有使用者」](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers)邊欄選項卡。
2. 按下 **「新增使用者**」 以開啟 **「使用者」** 窗格。
3. 輸入此使用者**的名稱**。
4. 基於您創建的租戶的名稱創建**使用者名**,並在末`.onmicrosoft.com`尾追加。 例如： `yourUserName@yourTenantName.onmicrosoft.com` 。 記下此使用者名。 您需要它才能登錄到群集。
5. 按下 **「目錄」角色**以打開目錄角色窗格,然後選擇 **「所有者」,** 然後按下窗格底部的 **「確定**」。
6. 在 **「使用者」** 窗格中,按下「**顯示密碼」** 並記錄臨時密碼。 首次登錄後,系統將提示您重置它。
7. 在窗格的底部,按下「**創建**」 以創建使用者。

## <a name="create-an-azure-ad-security-group"></a>建立 Azure AD 安全性群組

要授予群集管理員訪問許可權,Azure AD 安全組中的成員身份將同步到 OpenShift 組"osa-客戶管理員"。 如果未指定,將不會授予群集管理員訪問許可權。

1. 打開[Azure 活動目錄組邊](https://portal.azure.com/#blade/Microsoft_AAD_IAM/GroupsManagementMenuBlade/AllGroups)欄選項卡。
2. 按下 **「新建組**」。
3. 提供組名稱和說明。
4. 將**組類型**設置為 **「安全**」。
5. 將**成員資格類型**設定為**已分配**。

    將在前面的步驟中創建的 Azure AD 使用者添加到此安全組。

6. 按下 **「成員**」以開啟 **「選擇成員**」窗格。
7. 在成員清單中,選擇上面創建的 Azure AD 使用者。
8. 在門戶底部,按下「**選擇」** 然後**創建**以創建安全組。

    記下組 ID 值。

9. 建立群組時,您將在所有組清單中看到它。 單擊新組。
10. 在顯示的頁面上,向下複製物件**代碼**。 我們將引用此值,如`GROUPID`創建 Azure[紅帽 OpenShift 叢集](tutorial-create-cluster.md)教程。

> [!IMPORTANT]
> 要將此組與 osa-客戶管理員 OpenShift 組同步,請使用 Azure CLI 創建群集。 Azure 門戶當前缺少一個字段來設置此組。

## <a name="create-an-azure-ad-app-registration"></a>建立 Azure AD 應用程式註冊

通過將標誌省略到命令,`--aad-client-app-id`可以自動建立 Azure 活動目錄 (Azure AD) 應用註冊`az openshift create`用戶端,作為創建群集 的一部分。 本教程演示如何創建 Azure AD 應用註冊以獲得完整性。

如果您的組織尚未註冊 Azure 活動目錄 (Azure AD) 應用註冊用作服務主體,請按照這些說明創建一個。

1. 打開[應用註冊邊欄選項卡](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview),然後按**下 「新建註冊**」。。
2. 在 **「註冊應用程式**」窗格中,輸入應用程式註冊的名稱。
3. 確保**此組織型態**,**只會選擇此組織目錄中的帳號**。 這是最安全的選擇。
4. 稍後,我們將在知道群集 URI 後添加重定向 URI。 按下 **「註冊」** 按鈕可建立 Azure AD 應用程式註冊。
5. 在顯示的頁面上,向下複製**應用程式(用戶端)ID**。 我們將引用此值,如`APPID`創建 Azure[紅帽 OpenShift 叢集](tutorial-create-cluster.md)教程。

![套用物件頁面的螢幕擷取](./media/howto-create-tenant/get-app-id.png)

### <a name="create-a-client-secret"></a>建立用戶端密碼

生成用戶端密鑰,用於將應用驗證為 Azure 活動目錄。

1. 在應用註冊頁的 **「管理**」部分中,按**下 「證書&機密**」。
2. 在 **"證書&機密**"窗格中,按下 **"新用戶端機密**"。  將顯示 **「添加用戶端機密**」窗格。
3. 提供**說明**。
4. 設定為**您**喜歡的持續時間,例如 **,在 2 年內**。
5. 按下「**新增**」,鍵值將顯示在頁面的 **「用戶端機密**」部分中。
6. 向下複製鍵值。 我們將引用此值,如`SECRET`創建 Azure[紅帽 OpenShift 叢集](tutorial-create-cluster.md)教程。

![憑證與機密窗格的螢幕截圖](./media/howto-create-tenant/create-key.png)

有關 Azure 應用程式物件的詳細資訊,請參閱[Azure 活動目錄中的應用程式和服務主體物件](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)。

有關建立新 Azure AD 應用程式的詳細資訊,請參閱[使用 Azure 的目錄 v1.0 的訊息中註冊應用](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)。

## <a name="add-api-permissions"></a>新增 API 權限

[//]: # (不要更改為微軟圖形。它不適用於微軟圖形。)
1. 在 **'管理**' 部分按下**API 權限**
2. 點選 **「新增權限**」 並選擇**Azure 的目錄圖**,然後**委派權限**。
> [!NOTE]
> 請確保選擇了「Azure 活動目錄圖」,而不是「Microsoft 圖形」磁貼。

3. 展開下面的清單中**的使用者**並啟用**使用者.閱讀**許可權。 如果預設情況下啟用**了 User.Read,** 請確保它是 Azure**活動目錄圖形**許可權 **"使用者"**
4. 往上捲動並選擇**應用程式權限**。
5. 展開下面清單中的**目錄**並開啟**目錄。 讀取 。**
6. 按下「**添加權限**」以接受更改。
7. API 權限面板現在應同時顯示*User.Read*和*目錄。ReadAll*。 請注意*目錄*旁邊的 **「管理員同意」** 列中的警告。
8. 如果您是 Azure*訂閱管理員*,請按下下面的 **「授予管理員同意*訂閱名稱***」。 如果您不是 Azure*訂閱管理員*,請請求管理員的同意。

![API許可權面板的螢幕截圖。 使用者.閱讀和目錄.閱讀添加的所有許可權,管理員同意目錄.閱讀所有](./media/howto-aad-app-configuration/permissions-required.png)

> [!IMPORTANT]
> 群集管理員組的同步僅在獲得同意后才起作用。 在 *「管理員同意」要求*列中,您將看到一個綠色圓圈,其中帶有複選標記和消息"訂閱*名稱*授予"。

有關管理管理員和其他角色的詳細資訊,請參閱[新增或更改 Azure 訂閱管理員](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator)。

## <a name="resources"></a>資源

* [Azure 活動目錄中的應用程式和服務主體物件](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)
* [快速入門：向 Azure Active Directory v1.0 端點註冊應用程式](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)

## <a name="next-steps"></a>後續步驟

如果您滿足所有[Azure 紅帽 OpenShift 先決條件](howto-setup-environment.md),則可以創建第一個群集!

請嘗試本教程:
> [!div class="nextstepaction"]
> [建立 Azure Red Hat OpenShift 叢集](tutorial-create-cluster.md)

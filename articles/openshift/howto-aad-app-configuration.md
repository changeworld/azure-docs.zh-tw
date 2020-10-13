---
title: 適用于 Azure Red Hat OpenShift 的 Azure Active Directory 整合
description: 瞭解如何建立 Azure AD 安全性群組和使用者，以在您 Microsoft Azure Red Hat OpenShift 叢集上測試應用程式。
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 8d3326587d9c03e0a3960016b8ba3668c825ee6d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89470080"
---
# <a name="azure-active-directory-integration-for-azure-red-hat-openshift"></a>適用于 Azure Red Hat OpenShift 的 Azure Active Directory 整合

如果您尚未建立 Azure Active Directory (Azure AD) 租使用者，請遵循 [建立 Azure Red Hat OpenShift 的 Azure AD 租](howto-create-tenant.md) 使用者中的指示，再繼續進行這些指示。

Microsoft Azure Red Hat OpenShift 需要代表您的叢集中執行工作的許可權。 如果您的組織還沒有 Azure AD 使用者、Azure AD 安全性群組或 Azure AD 應用程式註冊來作為服務主體使用，請遵循下列指示來建立它們。

## <a name="create-a-new-azure-active-directory-user"></a>建立新的 Azure Active Directory 使用者

在 [Azure 入口網站](https://portal.azure.com)中，確定您的租使用者出現在入口網站右上角的使用者名稱底下：

![如果顯示錯誤的租使用者，請在右上方列出具有租使用者的入口網站螢幕擷取畫面 ](./media/howto-create-tenant/tenant-callout.png) ，在右上方按一下您的使用者名稱，然後按一下 [ **切換目錄**]，然後從 [ **所有目錄** ] 清單中選取正確的租使用者。

建立新的 Azure Active Directory 「擁有者」使用者，以登入您的 Azure Red Hat OpenShift 叢集。

1. 移至 [ [使用者-所有使用者](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) ] 分頁。
2. 按一下 [ **+ 新增使用者** ] 以開啟 [ **使用者** ] 窗格。
3. 輸入此使用者的 **名稱** 。
4. 根據您所建立的租使用者名稱建立 **使用者名稱** ，並  `.onmicrosoft.com` 在結尾附加。 例如： `yourUserName@yourTenantName.onmicrosoft.com` 。 寫下此使用者名稱。 您將需要它來登入您的叢集。
5. 按一下 [ **目錄角色** ] 以開啟 [目錄角色] 窗格，並選取 [ **擁有** 者]，然後按一下窗格底部的 **[確定]** 。
6. 在 [ **使用者** ] 窗格中，按一下 [ **顯示密碼** ] 並記錄暫存密碼。 當您第一次登入時，系統會提示您重設它。
7. 在窗格的底部，按一下 [ **建立** ] 以建立使用者。

## <a name="create-an-azure-ad-security-group"></a>建立 Azure AD 安全性群組

若要授與叢集系統管理員存取權，Azure AD 安全性群組中的成員資格會同步至 OpenShift 群組「osa-客戶-系統管理員」。 如果未指定，將不會授與任何叢集系統管理員存取權。

1. 開啟 [Azure Active Directory 群組](https://portal.azure.com/#blade/Microsoft_AAD_IAM/GroupsManagementMenuBlade/AllGroups) ] 分頁。
2. 按一下 [ **+ 新增群組**]。
3. 提供組名和描述。
4. 將 **群組類型** 設定為 **安全性**。
5. 將 **成員資格類型** 設定為 [ **已指派**]。

    將您在先前步驟中建立的 Azure AD 使用者新增到此安全性群組。

6. 按一下 [ **成員** ] 以開啟 [ **選取成員** ] 窗格。
7. 在 [成員] 清單中，選取您先前建立的 Azure AD 使用者。
8. 按一下入口網站底部的 [ **選取** ]，然後按一下 [ **建立** ] 以建立安全性群組。

    寫下 [群組識別碼] 值。

9. 建立群組時，您會在所有群組的清單中看到它。 按一下 [新增] 群組。
10. 在出現的頁面上，複製 **物件識別碼**。 我們將 `GROUPID` 在 [建立 Azure Red Hat OpenShift](tutorial-create-cluster.md) 叢集教學課程中參考此值。

> [!IMPORTANT]
> 若要將此群組與 osa-customer-admins OpenShift 群組同步，請使用 Azure CLI 來建立叢集。 Azure 入口網站目前缺少可設定此群組的欄位。

## <a name="create-an-azure-ad-app-registration"></a>建立 Azure AD 應用程式註冊

您可以在建立叢集時自動建立 Azure Active Directory (Azure AD) 應用程式註冊用戶端，方法是省略 `--aad-client-app-id` 命令的旗標 `az openshift create` 。 本教學課程說明如何建立 Azure AD 應用程式註冊，以取得完整性。

如果您的組織還沒有 Azure Active Directory (Azure AD) 應用程式註冊作為服務主體使用，請依照下列指示建立一個。

1. 開啟 [應用程式註冊](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) 分頁，然後按一下 [ **+ 新增註冊**]。
2. 在 [ **註冊應用程式** ] 窗格中，輸入應用程式註冊的名稱。
3. 確定已選取 [ **受支援的帳戶類型** ] 下的 [ **僅限此組織目錄中的帳戶** ]。 這是最安全的選擇。
4. 我們會在我們知道叢集的 URI 之後，稍後再新增重新導向 URI。 按一下 [ **註冊** ] 按鈕，建立 Azure AD 應用程式註冊。
5. 在出現的頁面上，將 **應用程式複製 (用戶端) 識別碼**。 我們將 `APPID` 在 [建立 Azure Red Hat OpenShift](tutorial-create-cluster.md) 叢集教學課程中參考此值。

![應用程式物件頁面的螢幕擷取畫面](./media/howto-create-tenant/get-app-id.png)

### <a name="create-a-client-secret"></a>建立用戶端密碼

產生用來驗證您的應用程式以 Azure Active Directory 的用戶端密碼。

1. 在 [應用程式註冊] 頁面的 [ **管理** ] 區段中，按一下 [ **憑證 & 秘密**]。
2. 在 [ **憑證 & 秘密** ] 窗格中，按一下 [ **+ 新增用戶端密碼**]。  [ **新增用戶端密碼** ] 窗格隨即出現。
3. 提供 **描述**。
4. 將 **到期** 時間設定為您偏好的持續時間，例如 **2 年**。
5. 按一下 [ **新增** ]，金鑰值就會出現在頁面的 [ **用戶端密碼** ] 區段中。
6. 複製金鑰值。 我們將 `SECRET` 在 [建立 Azure Red Hat OpenShift](tutorial-create-cluster.md) 叢集教學課程中參考此值。

![[憑證和密碼] 窗格的螢幕擷取畫面](./media/howto-create-tenant/create-key.png)

如需 Azure 應用程式物件的詳細資訊，請參閱 [Azure Active Directory 中的應用程式和服務主體物件](../active-directory/develop/app-objects-and-service-principals.md)。

如需建立新 Azure AD 應用程式的詳細資訊，請參閱 [使用 Azure Active Directory v1.0 端點註冊應用](../active-directory/develop/quickstart-register-app.md)程式。

## <a name="add-api-permissions"></a>新增 API 權限

[//]: # (請勿變更為 Microsoft Graph。不適用於 Microsoft Graph。)
1. 在 [**管理**] 區段中，按一下 [ **API 許可權**]
2. 按一下 [ **新增許可權** ]，然後選取 **Azure Active Directory Graph** 然後 **委派許可權**。
> [!NOTE]
> 請確定您選取的是「Azure Active Directory 圖」，而不是「Microsoft Graph」磚。

3. 展開下方清單中的 [ **使用者** ]，並啟用 [ **讀取** ] 許可權。 如果預設啟用 [ **使用者** ]，請確定它是 **Azure Active Directory Graph** 許可權 **使用者。請參閱**。
4. 向上和向下選取 **應用程式許可權**。
5. 展開下列清單上的 **目錄** ，並啟用 **ReadAll**。
6. 按一下 [ **新增許可權** ] 以接受變更。
7. [API 許可權] 面板現在應該會顯示 ReadAll 的 [ *讀取* ] 和 [ *目錄*]。 請注意*ReadAll*旁的 [**需要管理員同意**] 欄中的警告。
8. 如果您是*Azure 訂用帳戶管理員*，請按一下下方的訂用帳戶***名稱*的 [授與系統管理員同意**]。 如果您不是 *Azure 訂用帳戶管理員*，請向您的系統管理員要求同意。

![[API 許可權] 面板的螢幕擷取畫面。 已新增 ReadAll 許可權的使用者，目錄. ReadAll 需要系統管理員同意。](./media/howto-aad-app-configuration/permissions-required.png)

> [!IMPORTANT]
> 只有在授與同意之後，叢集系統管理員群組的同步處理才會運作。 您會在 [*需要管理員同意*] 資料行中看到有核取記號和「授與訂用帳戶*名稱*」訊息的綠色圓圈。

如需管理系統管理員和其他角色的詳細資訊，請參閱 [新增或變更 Azure 訂](../cost-management-billing/manage/add-change-subscription-administrator.md)用帳戶管理員。

## <a name="resources"></a>資源

* [Azure Active Directory 中的應用程式和服務主體物件](../active-directory/develop/app-objects-and-service-principals.md)
* [快速入門：向 Azure Active Directory v1.0 端點註冊應用程式](../active-directory/develop/quickstart-register-app.md)

## <a name="next-steps"></a>後續步驟

如果您已符合所有 [Azure Red Hat OpenShift 必要條件](howto-setup-environment.md)，您就可以開始建立您的第一個叢集！

試用教學課程：
> [!div class="nextstepaction"]
> [建立 Azure Red Hat OpenShift 叢集](tutorial-create-cluster.md)
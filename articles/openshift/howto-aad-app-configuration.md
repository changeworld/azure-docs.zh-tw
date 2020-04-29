---
title: Azure Red Hat OpenShift 的 Azure Active Directory 整合
description: 瞭解如何建立 Azure AD 安全性群組和使用者，以在您的 Microsoft Azure Red Hat OpenShift 叢集上測試應用程式。
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: f6c4fb5caf746650f95872d50afe31e5693422be
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81382923"
---
# <a name="azure-active-directory-integration-for-azure-red-hat-openshift"></a>Azure Red Hat OpenShift 的 Azure Active Directory 整合

如果您尚未建立 Azure Active Directory （Azure AD）租使用者，請遵循[建立 Azure Red Hat OpenShift 的 Azure AD 租](howto-create-tenant.md)使用者中的指示，再繼續進行這些指示。

Microsoft Azure Red Hat OpenShift 需要代表您的叢集執行工作的許可權。 如果您的組織還沒有 Azure AD 使用者、Azure AD 安全性群組或 Azure AD 應用程式註冊，以做為服務主體，請遵循這些指示來建立它們。

## <a name="create-a-new-azure-active-directory-user"></a>建立新的 Azure Active Directory 使用者

在 [ [Azure 入口網站](https://portal.azure.com)中，確定您的租使用者出現在入口網站右上方的 [使用者名稱] 底下：

![如果顯示錯誤的租使用者，請在](./media/howto-create-tenant/tenant-callout.png)右上方列出入口網站的螢幕擷取畫面，按一下右上方的使用者名稱，再按一下 [**切換目錄**]，然後從 [**所有目錄**] 清單中選取正確的租使用者。

建立新的 Azure Active Directory 「擁有者」使用者以登入您的 Azure Red Hat OpenShift 叢集。

1. 移至 [[使用者-所有使用者](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers)] 分頁。
2. 按一下 [ **+ 新增使用者**] 以開啟 [**使用者**] 窗格。
3. 輸入此使用者的 [**名稱**]。
4. 根據您所建立的租使用者名稱，並在結尾附加，以`.onmicrosoft.com`建立**使用者名稱**。 例如： `yourUserName@yourTenantName.onmicrosoft.com` 。 記下此使用者名稱。 您需要它才能登入您的叢集。
5. 按一下 [**目錄角色**] 以開啟 [目錄角色] 窗格，選取 [**擁有**者]，然後按一下窗格底部的 **[確定]** 。
6. 在 [**使用者**] 窗格中，按一下 [**顯示密碼**] 並記錄暫時密碼。 第一次登入之後，系統會提示您重設它。
7. 在窗格底部，按一下 [**建立**] 以建立使用者。

## <a name="create-an-azure-ad-security-group"></a>建立 Azure AD 安全性群組

若要授與叢集系統管理員存取權，Azure AD 安全性群組中的成員資格會同步至 OpenShift 群組「osa-客戶-管理員」。 如果未指定，則不會授與任何叢集系統管理員存取權。

1. 開啟 [ [Azure Active Directory 群組](https://portal.azure.com/#blade/Microsoft_AAD_IAM/GroupsManagementMenuBlade/AllGroups)] 分頁。
2. 按一下 [ **+ 新增群組**]。
3. 提供 [組名] 和 [描述]。
4. 將 [**群組類型**] 設定為 [**安全性**]。
5. 將**成員資格類型**設定為 [**已指派**]。

    將您在先前步驟中建立的 Azure AD 使用者新增到此安全性群組。

6. 按一下 [**成員**] 以開啟 [**選取成員**] 窗格。
7. 在 [成員] 清單中，選取您在上面建立的 Azure AD 使用者。
8. 在入口網站底部，依序按一下 [**選取**] 和 [**建立**] 以建立安全性群組。

    記下 [群組識別碼] 值。

9. 建立群組時，您會在 [所有群組] 清單中看到它。 按一下 [新增] 群組。
10. 在出現的頁面上，複製 [**物件識別碼**]。 我們將`GROUPID`在[建立 Azure Red Hat OpenShift](tutorial-create-cluster.md)叢集教學課程中參考此值。

> [!IMPORTANT]
> 若要將此群組與 osa-customer-admins OpenShift 群組同步，請使用 Azure CLI 來建立叢集。 Azure 入口網站目前缺少設定此群組所需的欄位。

## <a name="create-an-azure-ad-app-registration"></a>建立 Azure AD 應用程式註冊

藉由省略`--aad-client-app-id` `az openshift create`命令的旗標，您可以自動建立 Azure Active Directory （Azure AD）應用程式註冊用戶端，作為建立叢集的一部分。 本教學課程會示範如何建立 Azure AD 應用程式註冊以進行完整性。

如果您的組織還沒有 Azure Active Directory （Azure AD）應用程式註冊以作為服務主體，請遵循這些指示來建立一個。

1. 開啟 [[應用程式註冊](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview)] 分頁，然後按一下 [ **+ 新增註冊**]。
2. 在 [**註冊應用程式**] 窗格中，輸入應用程式註冊的名稱。
3. 請確定在 [**支援的帳戶類型**] 下，只選取 [**此組織目錄中的帳戶**]。 這是最安全的選擇。
4. 我們會在我們知道叢集的 URI 之後，稍後再新增重新導向 URI。 按一下 [**註冊**] 按鈕，以建立 Azure AD 應用程式註冊。
5. 在出現的頁面上，複製**應用程式（用戶端）識別碼**。 我們將`APPID`在[建立 Azure Red Hat OpenShift](tutorial-create-cluster.md)叢集教學課程中參考此值。

![[應用程式物件] 頁面的螢幕擷取畫面](./media/howto-create-tenant/get-app-id.png)

### <a name="create-a-client-secret"></a>建立用戶端密碼

產生用戶端密碼，以驗證您的應用程式以 Azure Active Directory。

1. 在 [應用程式註冊] 頁面的 [**管理**] 區段中，按一下 [**憑證 & 密碼**]。
2. 在 [**憑證 & 秘密**] 窗格中，按一下 [ **+ 新增用戶端密碼**]。  [**新增用戶端密碼**] 窗格隨即出現。
3. 提供**描述**。
4. 將 [**到期**] 設為您偏好的持續時間，例如**2 年**。
5. 按一下 [**新增**]，[金鑰] 值就會出現在頁面的 [**用戶端密碼**] 區段中。
6. 複製機碼值。 我們將`SECRET`在[建立 Azure Red Hat OpenShift](tutorial-create-cluster.md)叢集教學課程中參考此值。

![[憑證和密碼] 窗格的螢幕擷取畫面](./media/howto-create-tenant/create-key.png)

如需 Azure 應用程式物件的詳細資訊，請參閱[Azure Active Directory 中的應用程式和服務主體物件](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)。

如需建立新 Azure AD 應用程式的詳細資訊，請參閱[使用 Azure Active Directory v1.0 端點註冊應用](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)程式。

## <a name="add-api-permissions"></a>新增 API 權限

[//]: # (請勿變更為 Microsoft Graph。它無法與 Microsoft Graph 搭配使用。)
1. 在 [**管理**] 區段中，按一下 [ **API 許可權**]
2. 按一下 [**新增許可權**]，然後依序選取 [ **Azure Active Directory 圖形]** 和 [**委派許可權**]
> [!NOTE]
> 請確定您已選取 [Azure Active Directory 圖形]，而不是 [Microsoft Graph] 磚。

3. 在下列清單中展開 [**使用者**]，並啟用 [**讀取**] 許可權。 如果依預設會啟用 [**讀取**]，請確定它是**Azure Active Directory Graph**許可權**使用者。請參閱**。
4. 向上快移並選取 [**應用程式許可權**]。
5. 展開下列清單中的 [**目錄**]，並啟用 [ **ReadAll**]。
6. 按一下 [**新增許可權**] 以接受變更。
7. [API 許可權] 面板現在應該會顯示 [*使用者. 讀取*] 和 [*目錄] ReadAll*。 請注意 [ *ReadAll*] 旁的 [**管理員同意**] 欄位中的警告。
8. 如果您是*Azure 訂用帳戶管理員*，請在下方按一下 **[授與系統管理員同意*訂*** 用帳戶名稱]。 如果您不是*Azure 訂用帳戶管理員*，請向您的系統管理員要求同意。

![[API 許可權] 面板的螢幕擷取畫面。 使用者。已新增 ReadAll 許可權，目錄需要系統管理員同意。 ReadAll](./media/howto-aad-app-configuration/permissions-required.png)

> [!IMPORTANT]
> 只有在授與同意之後，叢集系統管理員群組的同步處理才會生效。 您會在 [*需要管理員同意*] 資料行中看到具有核取記號的綠色圓圈和「已授與訂用帳戶*名稱*」訊息。

如需管理系統管理員和其他角色的詳細資訊，請參閱[新增或變更 Azure 訂](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator)用帳戶管理員。

## <a name="resources"></a>資源

* [Azure Active Directory 中的應用程式和服務主體物件](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)
* [快速入門：向 Azure Active Directory v1.0 端點註冊應用程式](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)

## <a name="next-steps"></a>後續步驟

如果您已符合所有[Azure Red Hat OpenShift 必要條件](howto-setup-environment.md)，就可以開始建立您的第一個叢集！

嘗試教學課程：
> [!div class="nextstepaction"]
> [建立 Azure Red Hat OpenShift 叢集](tutorial-create-cluster.md)

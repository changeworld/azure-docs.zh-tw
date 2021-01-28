---
title: 為託管的試用產品設定 Azure Marketplace 訂用帳戶
description: 說明如何為 Dynamics 365 for Customer Engagement 和 Dynamics 365 for Operations 試用產品設定 Azure Marketplace 訂用帳戶
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 11/09/2020
ms.openlocfilehash: 60eeceac916a7f8c64214b7a74a8cf60fd1ec8ac
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98986119"
---
# <a name="set-up-an-azure-marketplace-subscription-for-hosted-test-drives"></a>為託管的試用產品設定 Azure Marketplace 訂用帳戶

本文說明如何設定適用于試用產品的 Azure Marketplace 訂用帳戶和 **dynamics 365 For Customer Engagement** 或 **Dynamics 365 for Operations** 環境。

## <a name="set-up-for-dynamics-365-for-customer-engagement"></a>設定 Dynamics 365 for Customer Engagement

1. 使用系統管理員帳戶登入 [Azure 入口網站](https://portal.azure.com/) 。
2. 藉由將滑鼠游標移至右上角的帳戶圖示，確認您在與 Dynamics 365 試用產品實例相關聯的租使用者中。 如果您不在正確的租使用者中，請選取帳戶圖示來切換至正確的租使用者。

    :::image type="content" source="./media/test-drive/select-correct-tenant.png" alt-text="選取正確的租使用者。":::

3. 確認 **Dynamics 365 Customer Engagement 方案** 授權可供使用。

    [![正在檢查方案授權。](media/test-drive/check-plan-license.png)](media/test-drive/check-plan-license.png#lightbox)

4. 在 Azure 中建立 Azure Active Directory (AD) 應用程式。 AppSource 會使用此應用程式來布建和取消布建租使用者中的試用產品使用者。
    1. 從 [篩選] 窗格中，選取 [ **Azure Active Directory**]。
    2. 選取 **應用程式註冊**。

        [![選取應用程式註冊。](media/test-drive/app-registrations.png)](media/test-drive/app-registrations.png#lightbox)

    3. 選取 [新增註冊]。
    4. 提供適當的應用程式名稱。

        :::image type="content" source="./media/test-drive/register-an-application.png" alt-text="註冊應用程式。":::

    5. 在 [支援的帳戶類型] 下，選取 [ **任何組織目錄中的帳戶] 和 [個人 Microsoft 帳戶**]。
    6. 選取 [ **建立** ] 並等候您的應用程式建立。
    7. 建立應用程式之後，請記下 [總覽] 畫面上顯示的 **應用程式識別碼** 。 稍後設定試用產品時，您將需要此值。
    8. 若要加入 nativeclient-windowsstore 重新導向 URI，請選取 [ **驗證** ] 分頁。 在 [**平臺** 設定] 下，選取 [**新增 platform**  >  **Mobile**  >  **Desktop** 應用程式磚]。 選擇 **nativeclient-windowsstore** 重新導向 URI，然後選取 [ **設定**]。

        :::image type="content" source="./media/test-drive/configure-desktop-devices.png" alt-text="新增 nativeclient-windowsstore 重新導向 URI。":::

    9. 在 [ **管理應用程式**] 底下，選取 [ **API 許可權**]。
    10. 選取 [ **新增許可權** ]，然後 **Microsoft Graph API**]。
    11. 選取 [ **應用程式** ] 許可權類別目錄，然後選取 [ **讀取** ] 和 [目錄]。 **所有** 許可權。

        :::image type="content" source="./media/test-drive/microsoft-graph.png" alt-text="設定應用程式許可權。":::

    12. 若要為允許清單 Azure AD 應用程式新增 **DYNAMICS CRM-使用者** 模擬存取權，請再次選取 [ **新增許可權** ]。

        :::image type="content" source="./media/test-drive/request-api-permissions.png" alt-text="要求應用程式許可權。":::

    13. 新增許可權之後，請選取 **[授與 Microsoft 的管理員同意**]。
    14. 從訊息警示中選取 **[是]**。

        [![顯示成功授與的應用程式許可權。](media/test-drive/api-permissions-confirmation-customer.png)](media/test-drive/api-permissions-confirmation-customer.png#lightbox)

    15. 若要產生 Azure AD App 的秘密：
        1. 從 [ **管理應用程式**] 中，選取 [ **憑證和秘密**]。
        2. 在 [用戶端密碼] 下，選取 [ **新增用戶端密碼**]。
        3. 輸入描述，例如 *試用* 產品，然後選取適當的持續時間。 試用產品會在此金鑰到期後中斷，此時您將需要產生並提供 AppSource 新的金鑰。
        4. 選取 [ **新增** ] 以產生 Azure 應用程式秘密。 複製此值，因為當您 lave 此分頁時，就會隱藏此值。 稍後設定試用產品時，您將需要此值。

            :::image type="content" source="./media/test-drive/add-client-secret.png" alt-text="新增用戶端密碼。":::

5. 有時候，將使用者從 Azure AD 同步處理至 CRM 實例所花費的時間可能會比預期還要久。 為了協助您進行這項作業，我們新增了強制同步處理使用者的程式，但必須合作夥伴中心 allowlisted Azure AD 應用程式。 若要這樣做，請參閱 [使用者同步處理至 Customer Engagement 實例](https://github.com/microsoft/AppSource/blob/master/Microsoft%20Hosted%20Test%20Drive/CDS_Utility_to_ForceUserSync_in_CRM_Instance.md)。
6. 將服務主體角色新增至應用程式，以允許 Azure AD 應用程式從您的 Azure 租使用者移除使用者。
    1. 開啟系統管理層級的 PowerShell 命令提示字元。
    2. Install-Module MSOnline (如果) 未安裝 MSOnline，請執行此命令。
    3. Connect-MsolService (這會顯示快顯視窗;使用新建立的組織租使用者) 登入。
    4. $applicationId = **<YOUR_APPLICATION_ID>**。
    5. $sp = Get-MsolServicePrincipal-AppPrincipalId $applicationId。
    6. Add-MsolRoleMember-RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1-RoleMemberObjectId $sp。ObjectId-RoleMemberType servicePrincipal。

        :::image type="content" source="./media/test-drive/sign-in-to-account.png" alt-text="登入您的帳戶。":::

7. 將上述建立的 Azure 應用程式新增為試用產品 CRM 實例的應用程式使用者。
    1. 在 **Azure Active Directory** 中新增使用者。 只有 (屬於相同租使用者) 的名稱和使用者 **名稱** **，才能** 建立此使用者，將其他欄位保留為預設值。 複製 [使用者名稱] 值。
    2. 登入 **CRM 實例**，並選取 [**設定**  >  **安全性**  >  **使用者**]。
    3. 將視圖變更為 **應用程式使用者**。

        :::image type="content" source="./media/test-drive/application-users.png" alt-text="設定使用者的帳戶資訊。":::

    4. 新增使用者 (確定表單適用于應用程式使用者) 。
    5. 在 [ **主要電子郵件** ] 和 [使用者名稱] 欄位中輸入相同的 **使用者名稱** 。 在 **應用程式識別碼** 中新增 **Azure ApplicationId** 。
    6. 提供任何 **完整名稱**。
    7. 選取 [儲存]。
    8. 選取 [ **管理角色**]。
    9. 指派包含讀取、寫入和指派角色許可權（例如 *系統管理員*）的自訂或 OOB 安全性角色。

        :::image type="content" source="./media/test-drive/security-roles-selection.png" alt-text="選取角色許可權。":::

    10. 將您為試用產品建立的自訂安全性角色指派給應用程式使用者。

## <a name="set-up-for-dynamics-365-for-operations"></a>針對作業設定 Dynamics 365

1. 使用系統管理員帳戶登入 [Azure 入口網站](https://portal.azure.com/) 。
2. 藉由將滑鼠游標移至右上角的帳戶圖示，確認您在與 Dynamics 365 試用產品實例相關聯的租使用者中。 如果您不在正確的租使用者中，請選取帳戶圖示來切換至正確的租使用者。

    :::image type="content" source="./media/test-drive/select-correct-tenant.png" alt-text="選取正確的租使用者。":::

3. 在 Azure 中建立 Azure AD App。 AppSource 會使用此應用程式來布建和取消布建租使用者中的試用產品使用者。
    1. 從 [篩選] 窗格中，選取 [ **Azure Active Directory**]。
    2. 選取 **應用程式註冊**。

        :::image type="content" source="./media/test-drive/app-registrations.png" alt-text="選取應用程式註冊。":::

    3. 選取 [新增註冊]。
    4. 提供適當的應用程式名稱。

        :::image type="content" source="./media/test-drive/register-an-application.png" alt-text="註冊應用程式。":::

    5. 在 [支援的帳戶類型] 下，選取 [ **任何組織目錄中的帳戶] 和 [個人 Microsoft 帳戶**]。
    6. 選取 [ **建立** ] 並等候您的應用程式建立。
    7. 建立應用程式之後，請記下 [總覽] 畫面上顯示的 **應用程式識別碼** 。 稍後設定試用產品時，您將需要此值。
    8. 在 [ **管理應用程式**] 底下，選取 [ **API 許可權**]。
    9. 選取 [ **新增許可權** ]，然後 **Microsoft Graph API**]。
    10. 選取 [ **應用程式** ] 許可權類別目錄，然後選取 [ **讀取** ] 和 [目錄]。 **所有** 許可權。

        :::image type="content" source="./media/test-drive/microsoft-graph.png" alt-text="設定應用程式許可權。":::

    11. 選取 [ **新增許可權**]。
    12. 新增許可權之後，請選取 **[授與 Microsoft 的管理員同意**]。
    13. 從訊息警示中選取 **[是]**。

        [![顯示已成功授與應用程式許可權。](media/test-drive/api-permissions-confirmation-operations.png)](media/test-drive/api-permissions-confirmation-operations.png#lightbox)

    14. 若要產生 Azure AD App 的秘密：
        1. 從 [ **管理應用程式**] 中，選取 [ **憑證和秘密**]。
        2. 在 [用戶端密碼] 下，選取 [ **新增用戶端密碼**]。
        3. 輸入描述，例如 *試用* 產品，然後選取適當的持續時間。 試用產品會在此金鑰到期後中斷，此時您將需要產生並提供 AppSource 新的金鑰。
        4. 選取 [ **新增** ] 以產生 Azure 應用程式秘密。 複製此值，因為當您 lave 此分頁時，就會隱藏此值。 稍後設定試用產品時，您將需要此值。

            :::image type="content" source="./media/test-drive/add-client-secret.png" alt-text="新增用戶端密碼。":::

4. 將服務主體角色新增至應用程式，以允許 Azure AD 應用程式從您的 Azure 租使用者移除使用者。
    1. 開啟系統管理層級的 PowerShell 命令提示字元。
    2. Install-Module MSOnline (如果) 未安裝 MSOnline，請執行此命令。
    3. Connect-MsolService (這會顯示快顯視窗;使用新建立的組織租使用者) 登入。
    4. $applicationId = **<YOUR_APPLICATION_ID>**。
    5. $sp = Get-MsolServicePrincipal-AppPrincipalId $applicationId。
    6. Add-MsolRoleMember-RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1-RoleMemberObjectId $sp。ObjectId-RoleMemberType servicePrincipal。

        :::image type="content" source="./media/test-drive/sign-in-to-account.png" alt-text="登入您的帳戶。":::

5. 現在將上述應用程式新增至 **Dynamics 365 進行作業** ，讓應用程式可以管理使用者。
    1. 尋找您 **的 Dynamics 365 For Operations** 實例。
    2. 從左上角，按一下三行功能表 (漢堡) 。
    3. 選取 [ **系統管理**]。
    4. 選取 [ **Azure Active Directory 應用程式**]。
    5. 選取 [+ 新增]  。
    6. 輸入要執行執行中動作的 **Azure AD 應用程式的用戶端識別碼** 。

    > [!NOTE]
    > 要執行其動作的使用者識別碼 (通常是實例的系統管理員，或是有權新增其他使用者) 的使用者。

    [![要執行其動作的使用者識別碼 (通常是實例的系統管理員，或是有權新增其他使用者) 的使用者。](media/test-drive/system-admin-user-id.png)](media/test-drive/system-admin-user-id.png#lightbox)
<!--
## Next steps

- [Commercial marketplace partner and customer usage attribution](azure-partner-customer-usage-attribution.md) -->

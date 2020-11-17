---
title: 刪除 Azure AD 組織 (租用戶) - Azure Active Directory | Microsoft Docs
description: 說明如何準備 Azure AD 組織 (租用戶) 以進行刪除，包括自助組織
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: enterprise-users
ms.topic: how-to
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: addimitu
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 189eaf3a08ce0e8411ce67170fdf1a895cbc56d2
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94647710"
---
# <a name="delete-a-tenant-in-azure-active-directory"></a>在 Azure Active Directory 中刪除租用戶

刪除 Azure AD 組織 (租用戶) 時，也會一併刪除組織中包含的所有資源。 在刪除之前，您必須盡可能縮減組織的相關資源，使其就緒。 只有 Azure Active Directory (Azure AD) 全域管理員可從入口網站刪除 Azure AD 組織。

## <a name="prepare-the-organization"></a>準備組織

在 Azure AD 中的組織通過幾項檢查之前，您無法予以刪除。 這些檢查會降低刪除 Azure AD 組織對使用者存取造成負面影響的風險，例如能夠登入 Microsoft 365 或存取 Azure 中的資源。 例如，如果不小心刪除與訂用帳戶建立關聯的組織，使用者就無法存取該訂用帳戶的 Azure 資源。 會檢查下列條件：

* 除了一個要刪除組織的全域管理員以外，Azure AD 組織 (租用戶) 中不能有任何使用者。 其他任何使用者都必須先刪除，才能刪除組織。 如果使用者會從內部部署同步，則必須先關閉同步，而且必須使用 Azure 入口網站或 Azure PowerShell Cmdlet 來刪除雲端組織中的使用者。
* 組織中不能有任何應用程式。 任何應用程式都必須先移除，才能刪除組織。
* 不能有任何多重要素驗證提供者連結到組織。
* 與組織相關聯的任何 Microsoft 線上服務（例如 Microsoft Azure、Microsoft 365 或 Azure AD Premium）都不能有任何訂閱。 例如，在 Azure 中建立預設 Azure AD 組織時，如果您的 Azure 訂用帳戶仍然仰賴這個組織進行驗證，就無法刪除此組織。 同樣地，如果另一位使用者讓訂用帳戶與組織建立關聯，您也會無法予以刪除。

## <a name="delete-the-organization"></a>刪除組織

1. 使用具備組織全域管理員身分的帳戶來登入 [Azure AD 系統管理中心](https://aad.portal.azure.com)。

2. 選取 **Azure Active Directory**。

3. 切換到您想要刪除的組織。
  
   ![刪除之前先確認組織](./media/directory-delete-howto/delete-directory-command.png)

4. 選取 [刪除租用戶]。
  
   ![選取要刪除組織的命令](./media/directory-delete-howto/delete-directory-list.png)

5. 若您的組織未通過一或多項檢查，系統會提供您一個連結，詳細說明如何通過檢查。 在您通過所有檢查之後，選取 [刪除] 即可完成流程。

## <a name="if-you-cant-delete-the-organization"></a>如果您無法刪除組織

當您設定 Azure AD 組織時，也可能為組織啟用了以授權為基礎的訂用帳戶，例如 Azure AD Premium P2、Microsoft 365 商務版 Standard 或 Enterprise Mobility + Security E5。 為了避免意外的資料遺失，您在訂用帳戶完全刪除之前，無法刪除組織。 訂用帳戶的狀態必須為 **取消佈建**，組織才能刪除。 **過期** 或 **取消** 的訂用帳戶會進入 **停用** 狀態，而最終階段為 **取消佈建** 狀態。

若要瞭解試用版 Microsoft 365 訂用帳戶到期的情況 (不包括付費夥伴/CSP、Enterprise 合約或大量授權) ，請參閱下表。 如需 Microsoft 365 資料保留和訂閱生命週期的詳細資訊，請參閱 [當我的商務用 Microsoft 365 結束時，我的資料和存取權會發生什麼情況？](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3)。 

訂用帳戶狀態 | 資料 | 資料存取權
----- | ----- | -----
有效 (試用 30 天) | 資料可供全體存取 | 使用者可以正常存取 Microsoft 365 檔案或應用程式<br>管理員可以正常存取 Microsoft 365 系統管理中心及資源 
過期 (30 天) | 資料可供全體存取| 使用者可以正常存取 Microsoft 365 檔案或應用程式<br>管理員可以正常存取 Microsoft 365 系統管理中心及資源
停用 (30 天) | 資料僅供管理員存取 | 使用者無法存取 Microsoft 365 檔案或應用程式<br>管理員可以存取 Microsoft 365 系統管理中心，但無法指派授權或更新使用者
取消佈建 (「停用」後 30 天) | 資料已刪除 (若沒有其他使用中的服務，就會自動刪除) | 使用者無法存取 Microsoft 365 檔案或應用程式<br>管理員可以存取 Microsoft 365 系統管理中心以購買及管理其他訂用帳戶

## <a name="delete-a-subscription"></a>刪除訂用帳戶

您可以使用 Microsoft 365 系統管理中心讓訂用帳戶進入 **取消佈建** 狀態，以在 3 天內刪除。

1. 使用具備組織全域管理員身分的帳戶來登入 [Microsoft 365 系統管理中心](https://admin.microsoft.com)。 若您嘗試刪除初始預設網域為 contoso.onmicrosoft.com 的 “Contoso” 組織，請使用 admin@contoso.onmicrosoft.com 這樣的 UPN 登入。

2. 藉由確認已啟用 **嘗試新的系統管理中心** 切換，預覽新的 Microsoft 365 系統管理中心。

   ![預覽新的 M365 系統管理中心體驗](./media/directory-delete-howto/preview-toggle.png)

3. 一旦啟用新的系統管理中心，您必須取消訂用帳戶，然後才能將其刪除。 針對您想要取消的訂用帳戶，選取 [帳單]，選取 [產品與服務]，然後選取 [取消訂用帳戶]。 您將會進入意見反應頁面。

   ![選擇要取消的訂用帳戶](./media/directory-delete-howto/cancel-choose-subscription.png)

4. 完成意見反應表單，然後選取 [取消訂用帳戶] 以取消訂用帳戶。

   ![訂用帳戶預覽中的取消命令](./media/directory-delete-howto/cancel-command.png)

5. 您現在可以刪除訂用帳戶。 針對您想要刪除的訂用帳戶，選取 [刪除]。 如果您在 [產品與服務] 頁面中找不到訂用帳戶，請確定您已將 [訂用帳戶狀態] 設定為 [所有]。

   ![用於刪除訂用帳戶的 [刪除] 連結](./media/directory-delete-howto/delete-command.png)

6. 選取 [刪除訂用帳戶] 以刪除訂用帳戶並接受條款及條件。 所有資料都會在三天內永久刪除。 若您改變了心意，在這三天內都可以[重新啟用訂用帳戶](/office365/admin/subscriptions-and-billing/reactivate-your-subscription?view=o365-worldwide)。
  
   ![仔細閱讀條款及條件](./media/directory-delete-howto/delete-terms.png)

7. 訂用帳戶狀態現已變更，而訂用帳戶會標示為待刪除。 訂用帳戶會在 72 小時候進入 [已取消佈建] 狀態。

8. 在您刪除了組織中的訂用帳戶，並經過 72 小時之後，您就可以再次登入 Azure AD 系統管理中心，這時理應沒有要採取的動作，也不會有訂用帳戶使您無法刪除組織。 您應能夠成功刪除 Azure AD 組織。
  
   ![刪除畫面上的通過訂用帳戶檢查](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="i-have-a-trial-subscription-that-blocks-deletion"></a>我有阻擋刪除的試用版訂用帳戶

有一種 [自助式註冊產品](/office365/admin/misc/self-service-sign-up?view=o365-worldwide) （例如 Microsoft Power BI、Rights Management 服務、microsoft Power Apps 或 Dynamics 365），個別使用者可以透過 Microsoft 365 註冊，這也會在您的 Azure AD 組織中建立來賓使用者進行驗證。 這些自助產品會阻擋目錄刪除，直到產品完全從組織中刪除為止，以避免資料遺失。 只有在使用者個別註冊或被指派產品的情況下，Azure AD 系統管理員才可以將其刪除。

自助式註冊產品的指派有兩種類型： 

* 組織層級指派：Azure AD 系統管理員會將產品指派給整個組織，而使用者也可以使用此組織層級指派來主動使用此服務，即使他們不是個別獲得授權也一樣。
* 使用者層級指派：個別使用者在自助式註冊期間基本上會將產品指派給自己，而不需要系統管理員。當組織變成由系統管理員管理 (請參閱 [系統管理員接管非受控組織](domains-admin-takeover.md))，系統管理員就可以直接將產品指派給使用者，而不需要自助式註冊。  

當您開始刪除自助式註冊產品時，此動作會永久刪除資料，並移除該服務的所有使用者存取權。 個別或在組織層級獲指派供應項目的使用者，會遭到封鎖而無法登入或存取任何現有的資料。 如果您想要使用自助式註冊產品 (例如 [Microsoft Power BI 儀表板](/power-bi/service-export-to-pbix)或 [Rights Management Services 原則設定](/azure/information-protection/configure-policy#how-to-configure-the-azure-information-protection-policy)) 來防止資料遺失，請確定資料已備份並儲存在其他位置。

如需目前可用自助式註冊產品與服務的詳細資訊，請參閱[可用的自助式程式](/office365/admin/misc/self-service-sign-up?view=o365-worldwide#available-self-service-programs)。

若要瞭解試用版 Microsoft 365 訂用帳戶到期的情況 (不包括付費夥伴/CSP、Enterprise 合約或大量授權) ，請參閱下表。 如需 Microsoft 365 資料保留和訂閱生命週期的詳細資訊，請參閱 [當我的商務用 Microsoft 365 結束時，我的資料和存取權會發生什麼情況？](/office365/admin/subscriptions-and-billing/what-if-my-subscription-expires?view=o365-worldwide)。

產品狀態 | 資料 | 資料存取權
------------- | ---- | --------------
有效 (試用 30 天) | 資料可供全體存取 | 使用者可以正常存取自助式註冊產品、檔案或應用程式<br>管理員可以正常存取 Microsoft 365 系統管理中心及資源
Deleted | 資料已刪除 | 使用者無法存取自助式註冊產品、檔案或應用程式<br>管理員可以存取 Microsoft 365 系統管理中心以購買及管理其他訂用帳戶

## <a name="how-can-i-delete-a-self-service-sign-up-product-in-the-azure-portal"></a>如何刪除 Azure 入口網站中的自助式註冊產品？

您可以讓自助式註冊產品 (例如 Microsoft Power BI 或 Azure Rights Management Services) 進入 **刪除** 狀態，以在 Azure AD 入口網站中立即刪除。

1. 使用組織中全域系統管理員身分的帳戶來登入 [Azure AD 系統管理中心](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)。 若您嘗試刪除初始預設網域為 contoso.onmicrosoft.com 的 “Contoso” 組織，請使用 admin@contoso.onmicrosoft.com 這樣的 UPN 登入。

2. 選取 [授權]，然後選取 [自助式註冊產品]。 您可以個別查看所有自助式註冊產品與以基座為基礎的訂用帳戶。 選擇您想要永久刪除的產品。 以下是 Microsoft Power BI 中的範例：

    ![顯示 [授權-自助式註冊產品] 頁面的螢幕擷取畫面。](./media/directory-delete-howto/licenses-page.png)

3. 選取 [刪除] 以刪除產品，並接受立即刪除資料且無法復原的條款。 此刪除動作將會移除所有使用者，並移除對該產品的組織存取權。 按一下 [是] 以繼續刪除。  

    ![顯示 [授權-自助式註冊產品] 頁面的螢幕擷取畫面，其中已開啟 [刪除自助式註冊產品]。](./media/directory-delete-howto/delete-product.png)

4. 當您選取 [是] 時，將會起始自助式產品的刪除作業。 有通知會告知您正在進行刪除。  

    ![顯示 [授權-自助式註冊產品] 頁面的螢幕擷取畫面，其中顯示 [正在刪除] 通知。](./media/directory-delete-howto/progress-message.png)

5. 現在，自助式註冊產品狀態已變更為 **已刪除**。 當您重新整理頁面時，產品應該會從 [自助式註冊產品] 頁面中移除。  

    ![顯示 [授權-自助式註冊產品] 頁面的螢幕擷取畫面，其右側會顯示 [已刪除自助式註冊產品] 窗格。](./media/directory-delete-howto/product-deleted.png)

6. 在您刪除所有產品之後，您就可以再次登入 Azure AD 系統管理中心，這時理應沒有要採取的動作，也不會有產品使您無法刪除組織。 您應能夠成功刪除 Azure AD 組織。

    ![使用者名稱輸入錯誤或是找不到](./media/directory-delete-howto/delete-organization.png)

## <a name="next-steps"></a>後續步驟

[Azure Active Directory 文件](../index.yml)
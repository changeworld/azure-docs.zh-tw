---
title: 刪除 Azure AD 目錄-Azure Active Directory |Microsoft Docs
description: 說明如何準備要刪除的 Azure AD 目錄，包括自助目錄
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/15/2019
ms.author: curtand
ms.reviewer: addimitu
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 47a60ed44ddf057ef983f8f76f23fd784bc3efd5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "73961823"
---
# <a name="delete-a-directory-in-azure-active-directory"></a>刪除 Azure Active Directory 中的目錄

刪除 Azure AD 目錄時，也會一併刪除目錄中包含的所有資源。 在刪除之前，將其相關聯的資源最小化來準備您的組織。 只有 Azure Active Directory （Azure AD）全域管理員可以從入口網站刪除 Azure AD 目錄。

## <a name="prepare-the-directory"></a>準備目錄

您無法在 Azure AD 中刪除目錄，直到通過數個檢查為止。 這些檢查會降低刪除 Azure AD 目錄對使用者存取造成負面影響的風險，例如登入 Office 365 或存取 Azure 中資源的能力。 例如，如果不小心刪除與訂用帳戶相關聯的目錄，則使用者無法存取該訂用帳戶的 Azure 資源。 會檢查下列條件：

* 目錄中不能有任何使用者，除非有一個全域管理員要刪除目錄。 必須先刪除任何其他使用者，才能刪除目錄。 如果使用者從內部部署同步處理，則必須先關閉同步處理，而且必須使用 Azure 入口網站或 Azure PowerShell Cmdlet，在雲端目錄中刪除使用者。
* 目錄中可能沒有任何應用程式。 所有應用程式都必須先移除，才能刪除目錄。
* 不能有任何與目錄連結的多重要素驗證提供者。
* 任何 Microsoft Online Service 都沒有任何訂用帳戶 (例如與目錄相關聯 Microsoft Azure、Office 365 或 Azure AD Premium)。 例如，在 Azure 中建立預設目錄時，如果您的 Azure 訂用帳戶仍然依賴此目錄來進行驗證，則無法刪除此目錄。 同樣地，如果另一位使用者擁有與目錄相關聯的訂用帳戶，您無法刪除目錄。

## <a name="delete-the-directory"></a>刪除目錄

1. 使用您組織的全域管理員帳戶登入[Azure AD 系統管理中心](https://aad.portal.azure.com)。

2. 選取 **Azure Active Directory**。

3. 切換至您想要刪除的目錄。
  
   ![刪除前先確認組織](./media/directory-delete-howto/delete-directory-command.png)

4. 選取 [刪除目錄]****。
  
   ![選取要刪除組織的命令](./media/directory-delete-howto/delete-directory-list.png)

5. 如果您的目錄未通過一或多項檢查，則會提供如何通過的詳細資訊連結。 在您通過所有檢查之後，選取 [刪除]**** 即可完成流程。

## <a name="if-you-cant-delete-the-directory"></a>如果您無法刪除目錄

當您設定 Azure AD 目錄時，您可能也已為您的組織啟用以授權為基礎的訂閱，例如 Azure AD Premium P2、Office 365 Business Premium 或 Enterprise Mobility + Security E5。 為避免意外遺失資料，您無法刪除目錄，直到訂閱完全刪除為止。 訂閱必須處於**取消布建**狀態，才能刪除目錄。 **過期**或**取消**的訂用帳戶會移至**已停用**狀態，而最終階段為**取消布建**狀態。

至於試用 Office 365 訂用帳戶過期時會發生的情況 (不含付費合作夥伴/CSP、Enterprise 合約或大量授權)，請見下表。 如需 Office 365 資料保留期與訂用帳戶生命週期的詳細資訊，請參閱 [What happens to my data and access when my Office 365 for business subscription ends?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3) (當商務用 Office 365 訂閱結束時，我的資料和存取權會如何？)。 

訂用帳戶狀態 | 資料 | 資料存取權
----- | ----- | -----
有效 (試用 30 天) | 資料可供全體存取 | 使用者可以正常存取 Office 365 檔案或應用程式<br>系統管理員具有 Microsoft 365 系統管理中心和資源的一般存取權 
過期 (30 天) | 資料可供全體存取| 使用者可以正常存取 Office 365 檔案或應用程式<br>系統管理員具有 Microsoft 365 系統管理中心和資源的一般存取權
停用 (30 天) | 資料僅供管理員存取 | 使用者無法存取 Office 365 檔案或應用程式<br>系統管理員可以存取 Microsoft 365 系統管理中心，但無法將授權指派給或更新使用者
取消佈建 (「停用」後 30 天) | 資料已刪除 (若沒有其他使用中的服務，就會自動刪除) | 使用者無法存取 Office 365 檔案或應用程式<br>系統管理員可以存取 Microsoft 365 系統管理中心，以購買及管理其他訂閱

## <a name="delete-a-subscription"></a>刪除訂用帳戶

您可以使用 Microsoft 365 系統管理中心，讓訂用帳戶進入**取消布建**狀態，以在3天內刪除。

1. 使用您組織中全域管理員的帳戶登入[Microsoft 365 系統管理中心](https://admin.microsoft.com)。 如果您嘗試刪除具有初始預設網域 contoso.onmicrosoft.com 的 "Contoso" 目錄，請使用像admin@contoso.onmicrosoft.com是的 UPN 進行登入。

2. 請確定已啟用 [**試用新**的系統管理中心] 切換，以預覽新的 Microsoft 365 系統管理中心。

   ![預覽新的 M365 administration center 體驗](./media/directory-delete-howto/preview-toggle.png)

3. 啟用新的系統管理中心之後，您必須先取消訂用帳戶，才能將它刪除。 選取 [**帳單**] 並選取 [**產品 & 服務**]，然後針對您要取消的訂用帳戶選取 [**取消訂**用帳戶]。 您將會進入意見反應頁面。

   ![選擇要取消的訂用帳戶](./media/directory-delete-howto/cancel-choose-subscription.png)

4. 完成意見反應表單，然後選取 [**取消訂**用帳戶] 以取消訂用帳戶。

   ![訂用帳戶預覽中的取消命令](./media/directory-delete-howto/cancel-command.png)

5. 您現在可以刪除訂用帳戶。 針對您想要刪除的訂用帳戶，選取 [**刪除**]。 如果您在 [**產品 & 服務**] 頁面中找不到訂用帳戶，請確定 [訂用帳戶**狀態**] 設為 [**全部**]。

   ![用於刪除訂用帳戶的 [刪除] 連結](./media/directory-delete-howto/delete-command.png)

6. 選取 [**刪除訂**用帳戶] 以刪除訂用帳戶並接受條款及條件。 所有資料都會在三天內永久刪除。 如果您改變主意，可以在三天期間[重新啟用訂閱](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/reactivate-your-subscription?view=o365-worldwide)。
  
   ![仔細閱讀條款及條件](./media/directory-delete-howto/delete-terms.png)

7. 現在，訂用帳戶狀態已變更，且訂用帳戶已標示為要刪除。 訂用帳戶會在 72 小時候進入 [已取消佈建]**** 狀態。

8. 當您刪除目錄中的訂用帳戶，且已過了72小時之後，您可以再次重新登入 Azure AD 系統管理中心，而且應該不會有任何必要的動作，也不會有任何訂用帳戶會封鎖您的目錄刪除。 您應該能夠成功刪除您的 Azure AD 目錄。
  
   ![刪除畫面上的通過訂用帳戶檢查](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="i-have-a-trial-subscription-that-blocks-deletion"></a>我有一個會封鎖刪除的試用訂用帳戶

如 Microsoft Power BI、Rights Management Services、Microsoft Power Apps 或 Dynamics 365 等[自助式註冊產品](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide)，個別使用者可以透過 Office 365 註冊，這也會在您的 Azure AD 目錄中建立來賓使用者進行驗證。 這些自助產品會封鎖目錄刪除，直到從目錄中完全刪除為止，以避免資料遺失。 只有在使用者個別註冊或被指派產品的情況下，Azure AD 系統管理員才可以刪除它們。

有兩種類型的自助式註冊產品可供指派： 

* 組織層級指派： Azure AD 管理員會將產品指派給整個組織，而使用者也可以使用此組織層級的指派來主動使用此服務，即使他們不是個別授權也一樣。
* 使用者層級指派：自助註冊期間的個別使用者基本上會將產品指派給本身，而不需要系統管理員。當組織被系統管理員管理之後（請參閱[管理員接管非受控目錄](domains-admin-takeover.md)，管理員就可以直接將產品指派給使用者，而不需要自助式註冊）。  

當您開始刪除自助式註冊產品時，此動作會永久刪除資料，並移除所有使用者對該服務的存取權。 在個別指派供應專案或組織層級的使用者，會遭到封鎖而無法登入或存取任何現有的資料。 如果您想要使用自助式註冊產品（例如[Microsoft Power BI 儀表板](https://docs.microsoft.com/power-bi/service-export-to-pbix)或[Rights Management 服務原則](https://docs.microsoft.com/azure/information-protection/configure-policy#how-to-configure-the-azure-information-protection-policy)設定）來防止資料遺失，請確定資料已備份並儲存在其他位置。

如需目前可用的自助式註冊產品和服務的詳細資訊，請參閱[可用的自助程式](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide#available-self-service-programs)。

至於試用 Office 365 訂用帳戶過期時會發生的情況 (不含付費合作夥伴/CSP、Enterprise 合約或大量授權)，請見下表。 如需 Office 365 資料保留期與訂用帳戶生命週期的詳細資訊，請參閱 [What happens to my data and access when my Office 365 for business subscription ends?](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/what-if-my-subscription-expires?view=o365-worldwide) (當商務用 Office 365 訂閱結束時，我的資料和存取權會如何？)。

產品狀態 | 資料 | 資料存取權
------------- | ---- | --------------
有效 (試用 30 天) | 資料可供全體存取 | 使用者可以正常存取自助式註冊產品、檔案或應用程式<br>系統管理員具有 Microsoft 365 系統管理中心和資源的一般存取權
Deleted | 資料已刪除 | 使用者無法存取自助式註冊產品、檔案或應用程式<br>系統管理員可以存取 Microsoft 365 系統管理中心，以購買及管理其他訂閱

## <a name="how-can-i-delete-a-self-service-sign-up-product-in-the-azure-portal"></a>如何刪除 Azure 入口網站中的自助式註冊產品？

您可以將 Microsoft Power BI 或 Azure Rights Management 服務之類的自助式註冊產品放入**刪除**狀態，以在 Azure AD 入口網站中立即刪除。

1. 使用組織中全域管理員的帳戶登入[Azure AD 系統管理中心](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)。 如果您嘗試刪除具有初始預設網域 contoso.onmicrosoft.com 的 "Contoso" 目錄，請使用像admin@contoso.onmicrosoft.com是的 UPN 進行登入。

2. 選取 [**授權**]，然後選取 [**自助式註冊產品**]。 您可以單獨查看所有的自助式註冊產品與以基座為基礎的訂用帳戶。 選擇您想要永久刪除的產品。 以下是 Microsoft Power BI 中的範例：

    ![使用者名稱不是輸入錯誤，或找不到](./media/directory-delete-howto/licenses-page.png)

3. 選取 [**刪除**] 以刪除產品，並接受立即刪除資料並無法變更的條款。 此刪除動作將會移除所有使用者，並移除對該產品的組織存取權。 按一下 [是] 以向前移動刪除。  

    ![使用者名稱不是輸入錯誤，或找不到](./media/directory-delete-howto/delete-product.png)

4. 當您選取 [**是]** 時，將會起始移除自助產品的作業。 有通知會告知您正在進行刪除。  

    ![使用者名稱不是輸入錯誤，或找不到](./media/directory-delete-howto/progress-message.png)

5. 現在，自助式註冊產品狀態已變更為 [**已刪除**]。 當您重新整理頁面時，應該從 [**自助式註冊產品**] 頁面中移除產品。  

    ![使用者名稱不是輸入錯誤，或找不到](./media/directory-delete-howto/product-deleted.png)

6. 刪除所有產品之後，您可以再次重新登入 Azure AD 系統管理中心，而且不應該採取任何動作，也不會有任何產品封鎖您的目錄刪除。 您應該能夠成功刪除您的 Azure AD 目錄。

    ![使用者名稱不是輸入錯誤，或找不到](./media/directory-delete-howto/delete-organization.png)

## <a name="next-steps"></a>後續步驟

[Azure Active Directory 文件](https://docs.microsoft.com/azure/active-directory/)

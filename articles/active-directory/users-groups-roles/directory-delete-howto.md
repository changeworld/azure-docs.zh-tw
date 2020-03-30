---
title: 刪除 Azure AD 目錄 - Azure 活動目錄 |微軟文檔
description: 說明如何準備 Azure AD 目錄以進行刪除，包括自助服務目錄
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73961823"
---
# <a name="delete-a-directory-in-azure-active-directory"></a>刪除 Azure 活動目錄中的目錄

刪除 Azure AD 目錄後，目錄中包含的所有資源也會被刪除。 在刪除之前，通過最小化其關聯資源來準備組織。 只有 Azure 活動目錄 （Azure AD） 全域管理員才能從門戶中刪除 Azure AD 目錄。

## <a name="prepare-the-directory"></a>準備目錄

在 Azure AD 中通過多個檢查之前，無法刪除該目錄。 這些檢查降低了刪除 Azure AD 目錄會對使用者訪問產生負面影響的風險，例如登錄到 Office 365 或訪問 Azure 中的資源的能力。 例如，如果無意中刪除了與訂閱關聯的目錄，則使用者無法訪問該訂閱的 Azure 資源。 會檢查下列條件：

* 除了一個要刪除該目錄的全域管理員之外，目錄中不能有使用者。 必須先刪除任何其他使用者，才能刪除目錄。 如果使用者從本地同步，則必須首先關閉同步，並且必須使用 Azure 門戶或 Azure PowerShell Cmdlet 在雲目錄中刪除使用者。
* 目錄中可能沒有任何應用程式。 必須先刪除任何應用程式，然後才能刪除該目錄。
* 不能沒有連結到目錄的多重要素驗證提供程式。
* 任何 Microsoft Online Service 都沒有任何訂用帳戶 (例如與目錄相關聯 Microsoft Azure、Office 365 或 Azure AD Premium)。 例如，在 Azure 中建立預設目錄時，如果您的 Azure 訂用帳戶仍然依賴此目錄來進行驗證，則無法刪除此目錄。 同樣地，如果另一位使用者擁有與目錄相關聯的訂用帳戶，您無法刪除目錄。

## <a name="delete-the-directory"></a>刪除目錄

1. 使用屬於組織的全域管理員的帳戶登錄到[Azure AD 管理中心](https://aad.portal.azure.com)。

2. 選擇**Azure 活動目錄**。

3. 切換到要刪除的目錄。
  
   ![刪除前確認組織](./media/directory-delete-howto/delete-directory-command.png)

4. 選取 [刪除目錄]****。
  
   ![選擇刪除組織的命令](./media/directory-delete-howto/delete-directory-list.png)

5. 如果您的目錄未通過一個或多個檢查，則為您提供有關如何傳遞的詳細資訊的連結。 在您通過所有檢查之後，選取 [刪除]**** 即可完成流程。

## <a name="if-you-cant-delete-the-directory"></a>如果無法刪除目錄

配置 Azure AD 目錄時，您可能還為組織啟動了基於許可證的訂閱，例如 Azure AD 高級 P2、Office 365 業務高級版或企業移動性 + 安全 E5。 為了避免意外資料丟失，在完全刪除訂閱之前，您無法刪除目錄。 訂閱必須處於**已取消預配**狀態，才能允許刪除目錄。 **過期**或**已取消**的訂閱將移動到 **"已禁用"** 狀態，而最後階段是 **"已取消預配"** 狀態。

至於試用 Office 365 訂用帳戶過期時會發生的情況 (不含付費合作夥伴/CSP、Enterprise 合約或大量授權)，請見下表。 如需 Office 365 資料保留期與訂用帳戶生命週期的詳細資訊，請參閱 [What happens to my data and access when my Office 365 for business subscription ends?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3) (當商務用 Office 365 訂閱結束時，我的資料和存取權會如何？)。 

訂用帳戶狀態 | 資料 | 資料存取權
----- | ----- | -----
有效 (試用 30 天) | 資料可供全體存取 | 使用者可以正常存取 Office 365 檔案或應用程式<br>管理員可以正常訪問 Microsoft 365 管理中心和資源 
過期 (30 天) | 資料可供全體存取| 使用者可以正常存取 Office 365 檔案或應用程式<br>管理員可以正常訪問 Microsoft 365 管理中心和資源
停用 (30 天) | 資料僅供管理員存取 | 使用者無法存取 Office 365 檔案或應用程式<br>管理員可以訪問 Microsoft 365 管理中心，但不能向使用者分配許可證或更新使用者
取消佈建 (「停用」後 30 天) | 資料已刪除 (若沒有其他使用中的服務，就會自動刪除) | 使用者無法存取 Office 365 檔案或應用程式<br>管理員可以訪問 Microsoft 365 管理中心以購買和管理其他訂閱

## <a name="delete-a-subscription"></a>刪除訂用帳戶

您可以使用 Microsoft 365 管理中心將訂閱置於 **"已取消預配"** 狀態，在三天內刪除。

1. 使用組織中全域管理員的帳戶登錄到[Microsoft 365 管理中心](https://admin.microsoft.com)。 如果您嘗試刪除具有初始預設域contoso.onmicrosoft.com的"Contoso"目錄，請使用 UPN（如admin@contoso.onmicrosoft.com） 登錄。

2. 通過確保**啟用"嘗試新的管理中心**切換"來預覽新的 Microsoft 365 管理中心。

   ![預覽新的 M365 管理中心體驗](./media/directory-delete-howto/preview-toggle.png)

3. 啟用新管理中心後，您需要取消訂閱，然後才能將其刪除。 選擇 **"計費****"並選擇"產品&服務**，然後選擇 **"取消**要取消訂閱的訂閱"。 您將被帶到回饋頁面。

   ![選擇要取消的訂閱](./media/directory-delete-howto/cancel-choose-subscription.png)

4. 填寫回饋表並選擇 **"取消訂閱**"以取消訂閱。

   ![取消訂閱預覽中的命令](./media/directory-delete-howto/cancel-command.png)

5. 您現在可以刪除訂閱。 為要刪除的訂閱選擇 **"刪除**"。 如果在 **"產品&服務**"頁中找不到訂閱，請確保將**訂閱狀態**設置為 **"全部**"。

   ![用於刪除訂用帳戶的 [刪除] 連結](./media/directory-delete-howto/delete-command.png)

6. 選擇 **"刪除訂閱**"以刪除訂閱並接受條款及條件。 所有資料都會在三天內永久刪除。 如果您改變主意，您可以在三天內[重新啟動訂閱](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/reactivate-your-subscription?view=o365-worldwide)。
  
   ![仔細閱讀條款及條件](./media/directory-delete-howto/delete-terms.png)

7. 現在訂閱狀態已更改，並且訂閱標記為刪除。 訂用帳戶會在 72 小時候進入 [已取消佈建]**** 狀態。

8. 刪除目錄中的訂閱且已 72 小時過後，可以再次登錄到 Azure AD 管理中心，並且不應執行任何必需的操作，也沒有阻止目錄刪除的訂閱。 您應該能夠成功刪除 Azure AD 目錄。
  
   ![刪除畫面上的通過訂用帳戶檢查](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="i-have-a-trial-subscription-that-blocks-deletion"></a>我有一個阻止刪除的試用訂閱

有[自助服務註冊產品](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide)，如 Microsoft Power BI、版權管理服務、Microsoft 電源應用或 Dynamics 365，個人使用者可以通過 Office 365 進行註冊，Office 365 還會創建來賓使用者，用於在 Azure AD 目錄中進行身份驗證。 這些自助服務產品阻止目錄刪除，直到它們從目錄中完全刪除，以避免資料丟失。 無論使用者是單獨註冊還是已分配產品，Azure AD 管理員都只能刪除它們。

在分配它們的方式中，有兩種類型的自助服務註冊產品： 

* 組織級分配：Azure AD 管理員將產品分配給整個組織，使用者可以在此組織級別分配中主動使用服務，即使他們不是單獨許可的。
* 使用者級別分配：在自助服務註冊期間，單個使用者基本上在沒有管理員的情況下將產品分配給自己。一旦組織由管理員管理（請參閱[管理員接管非託管目錄](domains-admin-takeover.md)，則管理員可以直接將產品分配給沒有自助服務註冊的使用者。  

當您開始刪除自助服務註冊產品時，該操作會永久刪除資料並刪除所有使用者對服務的存取權限。 然後，單獨或組織級別分配產品/服務的任何使用者都被禁止登錄或訪問任何現有資料。 如果要使用自助服務註冊產品（如[Microsoft Power BI 儀表板](https://docs.microsoft.com/power-bi/service-export-to-pbix)或[版權管理服務策略配置](https://docs.microsoft.com/azure/information-protection/configure-policy#how-to-configure-the-azure-information-protection-policy)）防止資料丟失，請確保將資料備份並保存在其他位置。

有關當前可用的自助服務註冊產品和服務的詳細資訊，請參閱[可用自助服務程式](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide#available-self-service-programs)。

至於試用 Office 365 訂用帳戶過期時會發生的情況 (不含付費合作夥伴/CSP、Enterprise 合約或大量授權)，請見下表。 如需 Office 365 資料保留期與訂用帳戶生命週期的詳細資訊，請參閱 [What happens to my data and access when my Office 365 for business subscription ends?](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/what-if-my-subscription-expires?view=o365-worldwide) (當商務用 Office 365 訂閱結束時，我的資料和存取權會如何？)。

產品狀態 | 資料 | 資料存取權
------------- | ---- | --------------
有效 (試用 30 天) | 資料可供全體存取 | 使用者可以正常訪問自助服務註冊產品、檔或應用<br>管理員可以正常訪問 Microsoft 365 管理中心和資源
Deleted | 已刪除資料 | 使用者無法訪問自助服務註冊產品、檔或應用<br>管理員可以訪問 Microsoft 365 管理中心以購買和管理其他訂閱

## <a name="how-can-i-delete-a-self-service-sign-up-product-in-the-azure-portal"></a>如何在 Azure 門戶中刪除自助服務註冊產品？

您可以將自助服務註冊產品（如 Microsoft Power BI 或 Azure 版權管理服務）置於 **"刪除"** 狀態，以便立即在 Azure AD 門戶中刪除。

1. 使用組織中全域管理員的帳戶登錄到[Azure AD 管理中心](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)。 如果您嘗試刪除具有初始預設域contoso.onmicrosoft.com的"Contoso"目錄，請使用 UPN（如admin@contoso.onmicrosoft.com）登錄。

2. 選擇**許可證**，然後選擇**自助服務註冊產品**。 您可以查看所有自助服務註冊產品與基於座位的訂閱分開。 選擇要永久刪除的產品。 下面是 Microsoft Power BI 中的示例：

    ![使用者名鍵入錯誤或未找到](./media/directory-delete-howto/licenses-page.png)

3. 選擇 **"刪除"** 可刪除產品並接受立即刪除且不可撤銷的資料的條款。 此刪除操作將刪除所有使用者並刪除組織對產品的存取權限。 按一下"是"以繼續刪除。  

    ![使用者名鍵入錯誤或未找到](./media/directory-delete-howto/delete-product.png)

4. 當您選擇 **"是**"時，將啟動自助產品的刪除。 有一個通知將告訴您正在進行的刪除。  

    ![使用者名鍵入錯誤或未找到](./media/directory-delete-howto/progress-message.png)

5. 現在，自助服務註冊產品狀態已更改為 **"已刪除**"。 刷新頁面時，應從**自助服務註冊產品**頁面中刪除產品。  

    ![使用者名鍵入錯誤或未找到](./media/directory-delete-howto/product-deleted.png)

6. 刪除所有產品後，可以再次登錄到 Azure AD 管理中心，並且不應執行任何必需的操作，也不應阻止產品阻止目錄刪除。 您應該能夠成功刪除 Azure AD 目錄。

    ![使用者名鍵入錯誤或未找到](./media/directory-delete-howto/delete-organization.png)

## <a name="next-steps"></a>後續步驟

[Azure 活動目錄文檔](https://docs.microsoft.com/azure/active-directory/)

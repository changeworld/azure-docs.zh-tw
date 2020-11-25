---
title: 設定 Azure AD 資源庫應用程式的使用者布建時發生問題
description: 如何對在為已經列於 Azure AD 應用程式庫中的應用程式設定使用者佈建時所面臨的常見問題進行疑難排解
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/03/2019
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: 306c3771c0392bbc97260a726e153cfd385cebcd
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994786"
---
# <a name="problem-configuring-user-provisioning-to-an-azure-ad-gallery-application"></a>設定 Azure AD 資源庫應用程式的使用者佈建時遇到的問題

設定應用程式的[自動使用者佈建](user-provisioning.md) (如果支援)，需要遵循特定指示來準備應用程式以進行自動佈建。 接著，可以使用 Azure 入口網站來設定佈建服務，將使用者帳戶同步處理至應用程式。

您一開始總是應先尋找為應用程式設定佈建專用的設定教學課程。 然後，遵循這些步驟來設定應用程式和 Azure AD 以建立佈建連接。 您可以在[如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](../saas-apps/tutorial-list.md)中找到應用程式教學課程清單。

## <a name="how-to-see-if-provisioning-is-working"></a>如何查看佈建是否正常運作 

一旦設定服務之後，就能從下列兩個位置繪製出大多數對服務作業的深入見解：

-   布建記錄 **(預覽)** –布建 [記錄](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)會記錄布建服務所執行的所有作業，包括查詢布建範圍內的已指派使用者 Azure AD。 查詢目標應用程式以確定那些使用者是否存在，比較系統之間的使用者物件。 接著，根據比較，在目標系統中新增、更新或停用使用者帳戶。 您可以在 [活動] 區段中選取 [ **Azure Active Directory** &gt; **企業應用程式** 布建 &gt; **記錄] (預覽)** **Activity** ，以存取 Azure 入口網站中的布建記錄。

-   **目前狀態–** 您可以在 [ **Azure Active Directory &gt; Enterprise Apps &gt; \[ 應用程式名稱 \] &gt;** 布建] 區段的 [服務設定] 下方的 [企業應用程式] 的 [布建] 區段中，看到指定應用程式的最後一個布建執行的摘要。 [目前狀態] 區段會顯示布建週期是否已開始布建使用者帳戶。 您可以監看迴圈的進度、查看已布建的使用者和群組數目，以及查看有多少角色已建立。 如果有任何錯誤，您可以在 [布建記錄檔 ( 中找到詳細資料。/reports-monitoring/concept-provisioning-logs.md？內容 = azure/active directory/管理-應用程式/內容/管理-應用程式-內容) 。

## <a name="general-problem-areas-with-provisioning-to-consider"></a>關於佈建要考慮的一般問題領域

如果您已知道要從何處著手，則以下是您可以向下切入的一般問題領域清單。

* [佈建服務似乎未啟動](#provisioning-service-does-not-appear-to-start)
* 無法儲存設定，因為應用程式認證無法運作
* [布建記錄檔表示「略過」且未布建使用者，即使已指派它們](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>佈建服務似乎未啟動

如果您在 Azure 入口網站的 [Azure Active Directory]&gt;[企業應用程式][&gt; \[應用程式名稱]\] &gt;[佈建] 區段中，將 [佈建狀態] 設為 [啟用]。 不過，在後續重新載入之後，該頁面上並未顯示任何其他狀態詳細資料。 可能是服務正在執行，但尚未完成初始週期。 檢查上面所述的布建 **記錄** ，以判斷服務所執行的作業，以及是否有任何錯誤。

>[!NOTE]
>視 Azure AD 目錄的大小和布建範圍中的使用者數目而定，初始週期可能需要20分鐘到數小時的時間。 初始迴圈之後的後續同步處理會更快，因為布建服務會儲存浮水印，以代表兩個系統在初始迴圈之後的狀態，進而改善後續同步處理的效能。
>
>

## <a name="cant-save-configuration-due-to-app-credentials-not-working"></a>無法儲存設定，因為應用程式認證無法運作

為了讓佈建能夠運作，Azure AD 需要有效的認證，讓它能夠連接到該應用程式所提供的使用者管理 API。 如果這些認證無效，或您不了解它們，請檢閱先前所述用來設定此應用程式的教學課程。

## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>布建記錄會指出即使已指派使用者，也會略過並未布建使用者

當使用者在布建記錄檔中顯示為「已略過」時，請務必讀取記錄訊息中的延伸詳細資料，以判斷原因。 下面是常見原因和解決方式：

- **已設定範圍設定篩選，** **這會根據屬性值篩選出使用者**。 如需詳細資訊，請參閱 [使用範圍篩選器進行以屬性為基礎的應用程式布建](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。

- **使用者「不具有效權限」。** 如果您看到此特定錯誤訊息，這是因為儲存在 Azure AD 中的使用者指派記錄發生問題。 若要修正此問題，請從應用程式解除指派使用者 (或群組)，然後重新指派一次。 如需詳細資訊，請參閱 [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)。

- **必要屬性已遺失或未針對使用者填入。** 設定佈建時必須考量的重點是，檢視和設定屬性 (Attribute) 對應，以及定義哪些使用者 (或群組) 屬性 (Property) 會從 Azure AD 流向應用程式的工作流程。 這包括設定「比對屬性」，此屬性可用於唯一識別並比對兩個系統之間的使用者/群組。 如需這個重要程式的詳細資訊，請參閱 [自訂使用者布建屬性](../app-provisioning/customize-application-attributes.md)對應。

  * **群組的屬性對應：** 除了成員，還有群組名稱和群組詳細資訊的佈建 (如果某些應用程式有支援)。 您可以啟用或停用 [布 **建] 索引** 標籤中顯示之群組物件的 **對應**，以啟用或停用這項功能。如果已啟用布建群組，請務必檢查屬性對應，以確保「相符識別碼」使用適當的欄位。 這可以是顯示名稱或電子郵件別名，因為如果 Azure AD 中某個群組的比對屬性空白或未填入，則不會佈建群組和其成員。

## <a name="next-steps"></a>後續步驟
[自動化使用 Azure Active Directory 對於 SaaS 應用程式的使用者佈建和解除佈建](user-provisioning.md)

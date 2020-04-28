---
title: 設定 Azure AD 資源庫應用程式的使用者布建時發生問題
description: 如何對在為已經列於 Azure AD 應用程式庫中的應用程式設定使用者佈建時所面臨的常見問題進行疑難排解
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3955b96e4a7edfc79a229d927523bdd4473409d4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77522759"
---
# <a name="problem-configuring-user-provisioning-to-an-azure-ad-gallery-application"></a>設定 Azure AD 資源庫應用程式的使用者佈建時遇到的問題

設定應用程式的[自動使用者佈建](user-provisioning.md) (如果支援)，需要遵循特定指示來準備應用程式以進行自動佈建。 接著，可以使用 Azure 入口網站來設定佈建服務，將使用者帳戶同步處理至應用程式。

您一開始總是應先尋找為應用程式設定佈建專用的設定教學課程。 然後，遵循這些步驟來設定應用程式和 Azure AD 以建立佈建連接。 您可以在[如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](../saas-apps/tutorial-list.md)中找到應用程式教學課程清單。

## <a name="how-to-see-if-provisioning-is-working"></a>如何查看佈建是否正常運作 

一旦設定服務之後，就能從下列兩個位置繪製出大多數對服務作業的深入見解：

-   布建**記錄（預覽）** –布[建記錄會記錄布](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)建服務所執行的所有作業，包括針對位於布建範圍內的已指派使用者查詢 Azure AD。 查詢目標應用程式以確定那些使用者是否存在，比較系統之間的使用者物件。 接著，根據比較，在目標系統中新增、更新或停用使用者帳戶。 您可以在 [**活動**] 區段中選取 [ **Azure Active Directory** &gt; **企業應用程式** &gt;布建**記錄（預覽）** ]，以存取 Azure 入口網站中的布建記錄。

-   **目前狀態–** 在 [服務設定] 底下的畫面底部，您可以在 [ **Azure Active Directory &gt; Enterprise Apps &gt; \[ \] &gt;應用程式名稱**布建] 區段中看到給定應用程式的最後一次布建執行摘要。 [目前狀態] 區段會顯示布建週期是否已開始布建使用者帳戶。 您可以觀賞迴圈的進度、查看已布建多少個使用者和群組，以及查看有多少角色已建立。 如果有任何錯誤，則可以在 [布建記錄（.）中找到詳細資料。/reports-monitoring/concept-provisioning-logs.md？內容 = azure/active directory/管理-應用程式/內容/管理-應用程式-內容）。

## <a name="general-problem-areas-with-provisioning-to-consider"></a>關於佈建要考慮的一般問題領域

如果您已知道要從何處著手，則以下是您可以向下切入的一般問題領域清單。

* [佈建服務似乎未啟動](#provisioning-service-does-not-appear-to-start)
* 無法儲存設定，因為應用程式認證無法運作
* [布建記錄表示使用者「略過」且未布建，即使已指派](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>佈建服務似乎未啟動

如果您在 Azure 入口網站的 [Azure Active Directory]&gt;[企業應用程式][&gt; \[應用程式名稱]\] &gt;[佈建]**** 區段中，將 [佈建狀態]**** 設為 [啟用]****。 不過，在後續重新載入之後，該頁面上並未顯示任何其他狀態詳細資料。 可能是服務正在執行，但尚未完成初始迴圈。 檢查上面所述的布建**記錄**，以判斷服務正在執行哪些作業，以及是否有任何錯誤。

>[!NOTE]
>視 Azure AD 目錄大小和布建範圍中的使用者數目而定，初始週期可能需要20分鐘到數小時的時間。 在初始週期之後的後續同步處理會更快，因為布建服務會在初始週期後儲存代表兩個系統狀態的浮水印，以改善後續同步處理的效能。
>
>

## <a name="cant-save-configuration-due-to-app-credentials-not-working"></a>無法儲存設定，因為應用程式認證無法運作

為了讓佈建能夠運作，Azure AD 需要有效的認證，讓它能夠連接到該應用程式所提供的使用者管理 API。 如果這些認證無效，或您不了解它們，請檢閱先前所述用來設定此應用程式的教學課程。

## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>布建記錄表示會略過使用者，但即使已指派

當使用者在布建記錄檔中顯示為「已略過」時，請務必閱讀記錄訊息中的延伸詳細資料，以判斷原因。 下面是常見原因和解決方式：

- **已設定範圍設定篩選，** **這會根據屬性值篩選出使用者**。 如需詳細資訊，請參閱[使用範圍篩選器進行以屬性為基礎的應用程式布建](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。

- **使用者「不具有效權限」。** 如果您看到此特定錯誤訊息，這是因為儲存在 Azure AD 中的使用者指派記錄發生問題。 若要修正此問題，請從應用程式解除指派使用者 (或群組)，然後重新指派一次。 如需詳細資訊，請參閱[將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)。

- **必要屬性已遺失或未針對使用者填入。** 設定佈建時必須考量的重點是，檢視和設定屬性 (Attribute) 對應，以及定義哪些使用者 (或群組) 屬性 (Property) 會從 Azure AD 流向應用程式的工作流程。 這包括設定「比對屬性」，此屬性可用於唯一識別並比對兩個系統之間的使用者/群組。 如需這個重要程式的詳細資訊，請參閱[自訂使用者布建屬性](../app-provisioning/customize-application-attributes.md)對應。

  * **群組的屬性對應：** 除了成員，還有群組名稱和群組詳細資訊的佈建 (如果某些應用程式有支援)。 您可以啟用或停用 [布**建] 索引**標籤中所顯示之群組物件的**對應**，來啟用或停用這項功能。如果已啟用布建群組，請務必檢查屬性對應，以確保有適當的欄位用於「相符識別碼」。 這可以是顯示名稱或電子郵件別名，因為如果 Azure AD 中某個群組的比對屬性空白或未填入，則不會佈建群組和其成員。

## <a name="next-steps"></a>後續步驟
[使用 Azure Active Directory 自動化 SaaS 應用程式的使用者布建和解除布建](user-provisioning.md)

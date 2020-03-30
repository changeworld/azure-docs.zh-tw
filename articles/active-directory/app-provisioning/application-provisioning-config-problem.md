---
title: 配置使用者預配到 Azure AD 庫應用時遇到問題
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522759"
---
# <a name="problem-configuring-user-provisioning-to-an-azure-ad-gallery-application"></a>設定 Azure AD 資源庫應用程式的使用者佈建時遇到的問題

設定應用程式的[自動使用者佈建](user-provisioning.md) (如果支援)，需要遵循特定指示來準備應用程式以進行自動佈建。 接著，可以使用 Azure 入口網站來設定佈建服務，將使用者帳戶同步處理至應用程式。

您一開始總是應先尋找為應用程式設定佈建專用的設定教學課程。 然後，遵循這些步驟來設定應用程式和 Azure AD 以建立佈建連接。 您可以在[如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](../saas-apps/tutorial-list.md)中找到應用程式教學課程清單。

## <a name="how-to-see-if-provisioning-is-working"></a>如何查看佈建是否正常運作 

一旦設定服務之後，就能從下列兩個位置繪製出大多數對服務作業的深入見解：

-   **預配日誌（預覽）** -[預配日誌](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)記錄預配服務執行的所有操作，包括查詢 Azure AD 以指定範圍內的預配使用者。 查詢目標應用程式以確定那些使用者是否存在，比較系統之間的使用者物件。 接著，根據比較，在目標系統中新增、更新或停用使用者帳戶。 通過在 **"活動"** 部分中選擇**Azure 活動目錄**&gt;**企業應用**&gt;**預配日誌（預覽），** 可以在 Azure 門戶中訪問預配日誌。

-   **目前狀態 |** 在服務設置下螢幕底部的**Azure 活動&gt;目錄企業應用&gt;\[\]&gt;應用程式名稱預配**部分中可以看到給定應用的最後一次預配運行摘要。 "目前狀態"部分顯示預配週期是否已開始預配使用者帳戶。 您可以查看週期的進度，查看已預配的使用者和組數，並查看創建了多少個角色。 如果有任何錯誤，可以在 [預配日誌 （.. ） 中找到詳細資訊。/報告監視/概念-預配-logs.md？上下文_azure/主動目錄/管理應用/上下文/管理應用-上下文）。

## <a name="general-problem-areas-with-provisioning-to-consider"></a>關於佈建要考慮的一般問題領域

如果您已知道要從何處著手，則以下是您可以向下切入的一般問題領域清單。

* [佈建服務似乎未啟動](#provisioning-service-does-not-appear-to-start)
* 無法儲存設定，因為應用程式認證無法運作
* [預配日誌表示使用者被"跳過"，未預配，即使已分配使用者](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>佈建服務似乎未啟動

如果您在 Azure 入口網站的 [Azure Active Directory]&gt;[企業應用程式][&gt; \[應用程式名稱]\] &gt;[佈建]**** 區段中，將 [佈建狀態]**** 設為 [啟用]****。 不過，在後續重新載入之後，該頁面上並未顯示任何其他狀態詳細資料。 服務可能正在運行，但尚未完成初始週期。 檢查上面描述的**預配日誌**，以確定服務正在執行哪些操作，以及是否有任何錯誤。

>[!NOTE]
>初始週期可能需要 20 分鐘到幾個小時，具體取決於 Azure AD 目錄的大小和預配範圍內的使用者數。 初始週期之後的後續同步速度更快，因為預配服務存儲的浮水印，表示初始週期後兩個系統的狀態，從而提高後續同步的性能。
>
>

## <a name="cant-save-configuration-due-to-app-credentials-not-working"></a>無法儲存設定，因為應用程式認證無法運作

為了讓佈建能夠運作，Azure AD 需要有效的認證，讓它能夠連接到該應用程式所提供的使用者管理 API。 如果這些認證無效，或您不了解它們，請檢閱先前所述用來設定此應用程式的教學課程。

## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>預配日誌表示，即使使用者已分配，也不會預配使用者

當使用者在預配日誌中顯示為"跳過"時，請務必閱讀日誌消息中的擴展詳細資訊以確定原因。 下面是常見原因和解決方式：

- **已設定範圍設定篩選，** **這會根據屬性值篩選出使用者**。 有關詳細資訊，請參閱[使用範圍篩選器的基於屬性的應用程式預配](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。

- **使用者「不具有效權限」。** 如果您看到此特定錯誤訊息，這是因為儲存在 Azure AD 中的使用者指派記錄發生問題。 若要修正此問題，請從應用程式解除指派使用者 (或群組)，然後重新指派一次。 有關詳細資訊，請參閱[將使用者或組分配給企業應用](../manage-apps/assign-user-or-group-access-portal.md)。

- **必要屬性已遺失或未針對使用者填入。** 設定佈建時必須考量的重點是，檢視和設定屬性 (Attribute) 對應，以及定義哪些使用者 (或群組) 屬性 (Property) 會從 Azure AD 流向應用程式的工作流程。 這包括設定「比對屬性」，此屬性可用於唯一識別並比對兩個系統之間的使用者/群組。 有關此重要過程的詳細資訊，請參閱[自訂使用者預配屬性對應](../app-provisioning/customize-application-attributes.md)。

  * **群組的屬性對應：** 除了成員，還有群組名稱和群組詳細資訊的佈建 (如果某些應用程式有支援)。 您可以通過啟用或禁用**預配**選項卡中顯示的組物件的**映射**來啟用或禁用此功能。如果啟用了預配組，請確保查看屬性對應，以確保為"匹配 ID"使用適當的欄位。 這可以是顯示名稱或電子郵件別名，因為如果 Azure AD 中某個群組的比對屬性空白或未填入，則不會佈建群組和其成員。

## <a name="next-steps"></a>後續步驟
[使用 Azure 活動目錄自動將使用者預配和取消預配到 SaaS 應用程式](user-provisioning.md)

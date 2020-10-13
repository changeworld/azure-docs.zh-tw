---
title: 使用 Azure Active Directory 依需求布建使用者
description: 強制同步處理
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 10/01/2020
ms.author: mimart
ms.reviewer: arvinh
ms.openlocfilehash: f7cbe9e9f81b3b71ee7da2feac2908c36f1777e5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91629007"
---
# <a name="on-demand-provisioning"></a>隨選佈建
使用隨選布建將使用者布建到應用程式中（以秒為單位）。 此外，您可以使用這項功能來：

* 快速針對設定問題進行疑難排解。
* 驗證您已定義的運算式。
* 測試範圍篩選準則。

## <a name="how-to-use-on-demand-provisioning"></a>如何使用隨選布建

1. 登入 **Azure 入口網站**。
1. 移至**所有服務**  >  **企業應用程式**。
1. 選取您的應用程式，然後移至 [布建設定] 頁面。
1. 藉由提供您的系統管理員認證來設定布建。
1. 選取 [ **視需要**布建]。
1. 依名字、姓氏、顯示名稱、使用者主體名稱或電子郵件地址搜尋使用者。
   > [!NOTE]
   > 若為雲端 HR 布建應用程式 (Workday/SuccessFactors 至 AD/Azure AD) ，則輸入值會不同。 若為 Workday 案例，請在 Workday 中提供 "WID" 使用者。 針對 SuccessFactors 案例，請在 SuccessFactors 中提供使用者的 "personIdExternal"。 
 
1. 選取 **頁面** 底部的 [布建]。

:::image type="content" source="media/provision-on-demand/on-demand-provision-user.jpg" alt-text="顯示視需要布建使用者之 Azure 入口網站 UI 的螢幕擷取畫面。":::

## <a name="understand-the-provisioning-steps"></a>瞭解布建步驟

隨選布建程式會嘗試顯示布建服務在布建使用者時所需的步驟。 布建使用者通常有五個步驟。 在隨選布建體驗期間，將會顯示其中一或多個步驟（如下節所述）。

### <a name="step-1-test-connection"></a>步驟1：測試連接

布建服務會藉由提出「測試使用者」的要求，嘗試授權目標應用程式的存取權。 布建服務預期會有回應，指出已獲授權可繼續進行布建步驟的服務。 只有在失敗時，才會顯示此步驟。 當步驟成功時，不會在隨選布建體驗期間顯示。

#### <a name="troubleshooting-tips"></a>疑難排解提示

* 確定您已為目標應用程式提供有效的認證，例如秘密權杖和租使用者 URL。 所需的認證會因應用程式而異。 如需詳細的設定教學課程，請參閱 [教學課程清單](../saas-apps/tutorial-list.md)。 
* 請確定目標應用程式支援篩選 [ **屬性** 對應] 窗格中所定義的相符屬性。 您可能需要檢查應用程式開發人員所提供的 API 檔，以瞭解支援的篩選準則。
* 針對跨網域身分識別管理的系統 (SCIM) 應用程式，您可以使用 Postman 之類的工具。 這類工具可協助您確保應用程式以 Azure Active Directory (Azure AD) 布建服務預期的方式回應授權要求。 查看 [範例要求](./use-scim-to-provision-users-and-groups.md#request-3)。

### <a name="step-2-import-user"></a>步驟2：匯入使用者

接下來，布建服務會從來源系統抓取使用者。 服務所抓取的使用者屬性稍後會用來：

* 評估使用者是否在布建範圍內。
* 檢查現有使用者的目標系統。
* 判斷要將哪些使用者屬性匯出至目標系統。

#### <a name="view-details"></a>檢視詳細資料


[ **視圖詳細資料** ] 區段會顯示從來源系統匯入之使用者的屬性 (例如 Azure AD) 。

#### <a name="troubleshooting-tips"></a>疑難排解提示

* 當來源系統中的使用者物件缺少相符的屬性時，匯入使用者可能會失敗。 若要解決此錯誤，請嘗試下列其中一種方法：

  * 以相符屬性的值更新使用者物件。
  * 變更布建設定中的相符屬性。

* 如果匯入的清單中遺漏了預期的屬性，請確定屬性具有來源系統中使用者物件的值。 布建服務目前不支援布建 null 屬性。
* 請確定布建設定的 [ **屬性對應** ] 頁面包含您預期的屬性。

### <a name="step-3-determine-if-user-is-in-scope"></a>步驟3：判斷使用者是否在範圍內

接下來，布建服務會判斷使用者是否在布建 [範圍](./how-provisioning-works.md#scoping) 內。 此服務會考慮以下各方面：

* 使用者是否已指派給應用程式。
* 範圍是否設定為 [ **已指派同步** ] 或 [ **全部同步**]。
* 在布建設定中定義的範圍篩選器。  

#### <a name="view-details"></a>檢視詳細資料

[ **視圖詳細資料** ] 區段會顯示已評估的範圍條件。 您可能會看到下列一或多個屬性：

* 作用**中的來源系統**表示使用者在 `IsActive` Azure AD 中的屬性設定為**true** 。
* **指派給應用程式** 表示使用者已在 Azure AD 中指派給應用程式。
* **範圍同步全部** 表示範圍設定允許租使用者中的所有使用者和群組。
* [**使用者具有必要角色**] 表示使用者具有要布建到應用程式中的必要角色。 
* 如果您已為應用程式定義範圍篩選器，也會顯示**範圍篩選器**。 篩選器會以下列格式顯示： {範圍篩選標題} {範圍篩選屬性} {範圍篩選器運算子} {範圍篩選值}。

#### <a name="troubleshooting-tips"></a>疑難排解提示

* 請確定您已定義有效的範圍角色。 例如，請避免使用非整數值的 [Greater_Than 運算子](./define-conditional-rules-for-provisioning-user-accounts.md#create-a-scoping-filter) 。
* 如果使用者沒有必要的角色，請參閱 [提供指派給預設存取角色之使用者的秘訣](./application-provisioning-config-problem-no-users-provisioned.md#provisioning-users-assigned-to-the-default-access-role)。

### <a name="step-4-match-user-between-source-and-target"></a>步驟4：比對來源與目標之間的使用者

在此步驟中，服務會嘗試將匯入步驟中所抓取的使用者與目標系統中的使用者進行比對。

#### <a name="view-details"></a>檢視詳細資料

[ **視圖詳細資料** ] 頁面會顯示目標系統中相符之使用者的屬性。 您在內容窗格中看到的屬性會如下所示：

* 如果目標系統中沒有相符的使用者，您將不會看到任何屬性。
* 如果目標系統中有一位以上的使用者，您將會看到來自目標系統的相符使用者屬性。
* 如果有多個使用者符合，您將會看到兩個相符使用者的屬性。
* 如果多個相符的屬性是屬性對應的一部分，則會依序評估每個相符的屬性，並顯示該屬性的相符使用者。

#### <a name="troubleshooting-tips"></a>疑難排解提示

* 布建服務可能無法將來源系統中的使用者與目標中的使用者唯一比對。 請確定相符的屬性是唯一的，以解決這個問題。
* 請確定目標應用程式支援篩選定義為相符屬性的屬性。  

### <a name="step-5-perform-action"></a>步驟5：執行動作

最後，布建服務會採取動作，例如建立、更新、刪除或略過使用者。

以下是您在成功布建使用者時可能會看到的範例：

:::image type="content" source="media/provision-on-demand/success-on-demand-provision.jpg" alt-text="顯示視需要布建使用者之 Azure 入口網站 UI 的螢幕擷取畫面。":::

#### <a name="view-details"></a>檢視詳細資料

[ **視圖詳細資料** ] 區段會顯示已在目標應用程式中修改的屬性。 此顯示代表布建服務活動的最終輸出和已匯出的屬性。 如果此步驟失敗，則顯示的屬性代表布建服務嘗試修改的屬性。

#### <a name="troubleshooting-tips"></a>疑難排解提示

* 匯出變更的失敗可能會有很大的差異。 請參閱 [檔以](../reports-monitoring/concept-provisioning-logs.md#error-codes) 瞭解常見失敗的布建記錄。

## <a name="frequently-asked-questions"></a>常見問題集

* **您需要關閉布建以使用隨選布建嗎？** 針對使用長期持有人權杖或使用者名稱和密碼進行授權的應用程式，不需要額外的步驟。 使用 OAuth 進行授權的應用程式目前需要先停止布建作業，才能使用隨選布建。 G Suite、Box、Workplace by Facebook 和時差等應用程式都屬於此類別。 工作正在進行中，為所有應用程式支援隨選布建，而不需停止布建作業。

* **隨選布建需要多久的時間？** 隨選布建通常需要不到30秒的時間。

## <a name="known-limitations"></a>已知限制

隨選布建目前有一些已知的限制。 張貼您的 [建議和意見反應，](https://aka.ms/appprovisioningfeaturerequest) 讓我們可以更清楚地判斷接下來要做什麼改進。

> [!NOTE]
> 以下是隨選布建功能的特定限制。 如需應用程式是否支援布建群組、刪除或其他功能的相關資訊，請參閱該應用程式的教學課程。

* Amazon Web Services (AWS) 應用程式不支援隨選布建。 
* 不支援依需求布建群組和角色。
* 隨選布建支援停用從應用程式取消指派的使用者。 不過，它不支援停用或刪除已從 Azure AD 中停用或刪除的使用者。 當您搜尋使用者時，不會出現這些使用者。

## <a name="next-steps"></a>後續步驟

* [布建疑難排解](./application-provisioning-config-problem.md)
---
title: 使用 Azure Active Directory 視需要布建使用者
description: 強制同步
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 06/23/2020
ms.author: mimart
ms.reviewer: arvinh
ms.openlocfilehash: 52819fc37cf0d10cb36009feb82dec234184752c
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88235531"
---
# <a name="on-demand-provisioning"></a>隨選布建
使用隨選布建，在幾秒內將使用者布建到應用程式中。 除此之外，您還可以使用這項功能來執行下列動作：

* 快速針對設定問題進行疑難排解。
* 驗證您已定義的運算式。
* 測試範圍篩選器。

## <a name="how-to-use-on-demand-provisioning"></a>如何使用隨選布建

1. 登入 **Azure 入口網站**。
2. 前往 [**所有服務**] [  >  **企業應用程式**]。
3. 選取您的應用程式，然後移至 [布建設定] 頁面。
4. 提供您的系統管理員認證來設定布建。
5. 選取 [ **隨選**布建]。
6. 依名字、姓氏、顯示名稱、使用者主體名稱或電子郵件地址來搜尋使用者。
7. 選取 **頁面** 底部的 [布建]。

:::image type="content" source="media/provision-on-demand/on-demand-provision-user.jpg" alt-text="顯示視需要布建使用者之 Azure 入口網站 UI 的螢幕擷取畫面。":::

## <a name="understand-the-provisioning-steps"></a>瞭解布建步驟

隨選布建程式會嘗試顯示布建服務布建使用者時所採取的步驟。 布建使用者通常有五個步驟。 在隨選布建體驗期間，將會顯示其中一個或多個步驟（在下列各節中說明）。

### <a name="step-1-test-connection"></a>步驟1：測試連接

布建服務會嘗試對「測試使用者」提出要求，以授權存取目標應用程式。 布建服務預期會有一個回應，指出已授權的服務繼續進行布建步驟。 只有在失敗時，才會顯示此步驟。 當步驟成功時，不會在隨選布建體驗期間顯示。

#### <a name="troubleshooting-tips"></a>疑難排解秘訣

* 請確定您已為目標應用程式提供有效的認證，例如秘密權杖和租使用者 URL。 所需的認證會因應用程式而異。 如需詳細的設定教學課程，請參閱 [教學課程清單](../saas-apps/tutorial-list.md)。 
* 請確定目標應用程式支援篩選 [ **屬性** 對應] 窗格中所定義的比對屬性。 您可能需要查看應用程式開發人員所提供的 API 檔，以瞭解支援的篩選準則。
* 針對跨網域身分識別管理 (SCIM) 應用程式的系統，您可以使用 Postman 之類的工具。 這類工具可協助您確保應用程式以 Azure Active Directory (Azure AD) 布建服務所預期的方式來回應授權要求。 請查看 [範例要求](./use-scim-to-provision-users-and-groups.md#request-3)。

### <a name="step-2-import-user"></a>步驟2：匯入使用者

接下來，布建服務會從來源系統抓取使用者。 稍後會使用服務所抓取的使用者屬性來執行下列動作：

* 評估使用者是否在布建範圍內。
* 檢查現有使用者的目標系統。
* 決定要匯出至目標系統的使用者屬性。

#### <a name="view-details"></a>檢視詳細資料


[ **查看詳細資料** ] 區段會顯示從來源系統匯入之使用者的屬性 (例如 Azure AD) 。

#### <a name="troubleshooting-tips"></a>疑難排解秘訣

* 當來源系統中的使用者物件上遺漏相符的屬性時，匯入使用者可能會失敗。 若要解決此失敗，請嘗試下列其中一種方法：

  * 以相符屬性的值更新 user 物件。
  * 變更布建設定中的相符屬性。

* 如果匯入的清單中遺漏您預期的屬性，請確定該屬性在來源系統中的使用者物件上具有值。 布建服務目前不支援提供 null 屬性。
* 請確定布建設定的 [ **屬性對應** ] 頁面包含您預期的屬性。

### <a name="step-3-determine-if-user-is-in-scope"></a>步驟3：判斷使用者是否在範圍內

接下來，布建服務會判斷使用者是否在布建 [範圍內](./how-provisioning-works.md#scoping) 。 服務會考慮下列各方面：

* 是否將使用者指派給應用程式。
* 範圍是否設定為 [ **同步指派** ] 或 [ **全部同步**]。
* 在布建設定中定義的範圍篩選器。  

#### <a name="view-details"></a>檢視詳細資料

[ **查看詳細資料** ] 區段會顯示已評估的範圍條件。 您可能會看到下列一個或多個屬性：

* [**在來源系統中作用中**] 表示使用者 `IsActive` 在 Azure AD 中將屬性設定為**true** 。
* [**指派給應用程式**] 表示已將使用者指派給 Azure AD 中的應用程式。
* [**範圍同步全部**] 表示範圍設定允許租使用者中的所有使用者和群組。
* [**使用者具有必要角色**] 表示使用者擁有要布建到應用程式的必要角色。 
* 如果您已為應用程式定義範圍篩選器，則也會顯示**範圍篩選器**。 此篩選器會以下列格式顯示： {範圍篩選標題} {範圍篩選屬性} {範圍篩選器運算子} {範圍篩選值}。

#### <a name="troubleshooting-tips"></a>疑難排解秘訣

* 請確定您已定義有效的範圍角色。 例如，請避免使用 [Greater_Than 運算子](./define-conditional-rules-for-provisioning-user-accounts.md#create-a-scoping-filter) 搭配非整數值。
* 如果使用者沒有必要的角色，請參閱布建 [指派給預設存取角色之使用者的秘訣](./application-provisioning-config-problem-no-users-provisioned.md#provisioning-users-assigned-to-the-default-access-role)。

### <a name="step-4-match-user-between-source-and-target"></a>步驟4：比對來源與目標之間的使用者

在此步驟中，服務會嘗試將匯入步驟中抓取的使用者與目標系統中的使用者進行比對。

#### <a name="view-details"></a>檢視詳細資料

[ **查看詳細資料** ] 頁面會顯示目標系統中符合的使用者屬性。 您在內容窗格中看到的屬性會有所不同，如下所示：

* 如果目標系統中沒有符合的使用者，您就不會看到任何屬性。
* 如果目標系統中有一位使用者符合，您會看到該使用者與目標系統的屬性相符。
* 如果符合多個使用者，您會看到兩個相符使用者的屬性。
* 如果多個比對屬性是屬性對應的一部分，則會依序評估每個相符的屬性，並顯示該屬性的相符使用者。

#### <a name="troubleshooting-tips"></a>疑難排解秘訣

* 布建服務可能無法讓來源系統中的使用者與目標中的使用者唯一相符。 請確定相符的屬性是唯一的，以解決此問題。
* 請確定目標應用程式支援篩選定義為相符屬性的屬性。  

### <a name="step-5-perform-action"></a>步驟5：執行動作

最後，布建服務會採取動作，例如建立、更新、刪除或略過使用者。

以下是您在成功布建使用者的要求後可能會看到的範例：

:::image type="content" source="media/provision-on-demand/success-on-demand-provision.jpg" alt-text="顯示使用者的成功隨選布建的螢幕擷取畫面。":::

#### <a name="view-details"></a>檢視詳細資料

[ **View details** ] 區段會顯示已在目標應用程式中修改的屬性。 此顯示代表布建服務活動的最終輸出，以及已匯出的屬性。 如果此步驟失敗，顯示的屬性代表布建服務嘗試修改的屬性。

#### <a name="troubleshooting-tips"></a>疑難排解秘訣

* 匯出變更的失敗可能會有很大的差異。 如需常見失敗的布建記錄，請參閱 [檔](../reports-monitoring/concept-provisioning-logs.md#error-codes) 。

## <a name="frequently-asked-questions"></a>常見問題集

* **您需要關閉布建以使用隨選布建嗎？** 針對使用長期持有人權杖或使用者名稱和密碼進行授權的應用程式，不需要任何額外的步驟。 使用 OAuth 進行授權的應用程式目前需要先停止布建作業，才能使用隨選布建。 G Suite、Box、Workplace by Facebook 和時差等應用程式都屬於此類別。 工作正在進行，以支援所有應用程式的隨選布建，而不需停止布建作業。

* **隨選布建需要多久的時間？** 隨選布建通常不到30秒的時間。

## <a name="known-limitations"></a>已知限制

目前有幾個已知的隨選布建限制。 張貼您的 [建議和意見](https://aka.ms/appprovisioningfeaturerequest) 反應，讓我們能夠更清楚地判斷接下來要做什麼改進。

> [!NOTE]
> 下列限制適用于隨選布建功能。 如需應用程式是否支援布建群組、刪除或其他功能的相關資訊，請參閱該應用程式的教學課程。

* Workday、Amazon Web Services (AWS) 和 SuccessFactors 應用程式不支援隨選布建。 
* 不支援依需求布建群組和角色。
* 隨選布建支援停用已從應用程式取消指派的使用者。 不過，它不支援停用或刪除已從 Azure AD 停用或刪除的使用者。 當您搜尋使用者時，將不會顯示這些使用者。

## <a name="next-steps"></a>後續步驟

* [布建疑難排解](./application-provisioning-config-problem.md)
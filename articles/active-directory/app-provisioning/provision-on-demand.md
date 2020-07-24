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
ms.openlocfilehash: 7799e873afb117481cebafd982df59a3267f4405
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87051560"
---
# <a name="on-demand-provisioning"></a>隨選布建
隨選布建可讓您在幾秒內將使用者布建到應用程式中。 您可以使用此功能快速針對設定問題進行疑難排解、驗證您已定義的運算式、測試範圍篩選器，以及其他更多功能。 

## <a name="how-to-use-on-demand-provisioning"></a>如何使用隨選布建 

1. 登入 **Azure 入口網站**。
2. 瀏覽至 [企業應用程式]****。
3. 選取您的應用程式，並流覽至 [布建設定] 頁面。
4. 提供您的系統管理員認證來設定布建。
5. 按一下 [**隨選布建**]。
6. 依名字、姓氏、顯示名稱、使用者主體名稱或電子郵件來搜尋使用者。
7. 選取頁面底部的 [布建]。

:::image type="content" source="media/provision-on-demand/on-demand-provision-user.jpg" alt-text="隨選布建使用者。":::

## <a name="understanding-the-provisioning-steps"></a>瞭解布建步驟
隨選布建功能會嘗試顯示布建服務布建使用者時所採取的步驟。 布建使用者通常有五個步驟，而且下列一個或多個步驟將會顯示在隨選布建體驗中。

### <a name="step-1-test-connection"></a>步驟1：測試連接
布建服務會嘗試對「測試使用者」提出要求，以授權存取目標應用程式。 布建服務預期會有回應，指出它已獲授權可以繼續布建步驟。 只有當步驟失敗時，才會顯示此步驟。 當步驟成功時，它不會顯示在隨選布建體驗中。 

**疑難排解提示**
* 請確定您已為目標應用程式提供有效的認證，例如秘密權杖和租使用者 URL。 所需的認證會因應用程式而異。 您可以在這裡找到詳細的設定教學[課程](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)。 
* 確定目標應用程式支援篩選 [屬性對應] 分頁中所定義的比對屬性。 您可能需要查看應用程式開發人員所提供的 API 檔，以瞭解它們所支援的篩選器。  
* 針對 SCIM 應用程式，您可以使用 postman 之類的工具，以確保應用程式會回應 Azure AD 布建服務所預期的授權要求。 您可以在[這裡](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#request-3)找到範例要求。

### <a name="step-2-import-user"></a>步驟2：匯入使用者
接下來，布建服務會從來源系統抓取使用者。 服務所抓取的使用者屬性會在稍後用來評估使用者是否在布建範圍內、檢查現有使用者的目標系統，以及判斷要將哪些使用者屬性匯出至目標系統。 

**檢視詳細資料**

[View details] 區段會顯示從來源系統匯入之使用者的屬性（例如 Azure AD）。

**疑難排解提示**
* 當來源系統中的使用者物件上遺漏相符的屬性時，匯入使用者可能會失敗。 您可以使用相符屬性的值來更新使用者物件，或變更布建設定中的比對屬性，以解決此失敗。  
* 如果匯入的清單中遺漏您預期的屬性，請確定該屬性在來源系統中的使用者物件上具有值。 布建服務目前不支援提供 null 屬性。 
* 確定您的 [布建設定屬性對應] 頁面包含您預期的屬性。 

### <a name="step-3-determine-if-user-is-in-scope"></a>步驟3：判斷使用者是否在範圍內
接下來，布建服務會判斷使用者是否在布建[範圍內](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works#scoping)。 此服務會考慮各種層面，例如是否將使用者指派給應用程式、是否將 [範圍] 設定為 [同步指派] 或 [全部同步]，以及在您的布建設定中定義的範圍篩選器。  

**檢視詳細資料**

[查看詳細資料] 區段會顯示已評估的範圍條件。 您可能會看到下列其中一個屬性：
* [**在來源系統中作用中**] 表示使用者在 Azure AD 中將屬性設為 [true]。
* [**指派給應用程式**] 表示已將使用者指派給中的應用程式 Azure AD
* [**範圍同步全部**] 表示範圍設定允許租使用者中的所有使用者和群組。
* [**使用者具有必要角色**] 表示使用者擁有要布建到應用程式的必要角色。 
* 如果您已定義應用程式的範圍篩選器，也會顯示**範圍篩選器**。 此篩選器會以下列格式顯示： {範圍篩選標題} {範圍篩選屬性} {範圍篩選運算子} {範圍篩選值}。 

**疑難排解提示**
* 請確定您已定義有效的範圍角色。 例如，請避免使用「[大於](https://docs.microsoft.com/azure/active-directory/app-provisioning/define-conditional-rules-for-provisioning-user-accounts#create-a-scoping-filter)」運算子搭配非整數值。 
* 如果使用者沒有必要的角色，請參閱[這裡](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned#provisioning-users-assigned-to-the-default-access-role)所述的秘訣。 

### <a name="step-4-match-user-between-source-and-target"></a>步驟4：比對來源與目標之間的使用者
在此步驟中。 服務會嘗試將匯入步驟中抓取的使用者與目標系統中的使用者進行比對。 

**檢視詳細資料**

[查看詳細資料] 頁面會顯示目標系統中符合的使用者屬性。 您在內容窗格中看到的屬性會有所不同，如下所示：
* 如果目標系統中沒有符合的使用者，您就不會看到任何屬性。
* 如果目標系統中有一位使用者符合，您會看到該使用者與目標系統的屬性相符。
* 如果符合多個使用者，您會看到兩個相符使用者的屬性。
* 如果多個比對屬性是屬性對應的一部分，則會依序評估每個符合的屬性，並顯示相符的使用者。 

**疑難排解詳細資料**
* 布建服務無法以目標中的使用者唯一符合來源中的使用者。 藉由確保相符的屬性是唯一的，即可解決此問題。 
* 確定目標應用程式支援篩選定義為相符屬性的屬性。  

### <a name="step-5-perform-action"></a>步驟5：執行動作
最後，布建服務會採取動作，例如建立、更新、刪除或略過使用者。 

:::image type="content" source="media/provision-on-demand/success-on-demand-provision.jpg" alt-text="已成功布建使用者。":::

**檢視詳細資料**

[View details] 區段會顯示已在目標應用程式中修改的屬性。 這代表布建服務活動的最終輸出，以及已匯出的屬性。 如果此步驟失敗，顯示的屬性代表布建服務嘗試修改的屬性。  

**疑難排解提示**
* 匯出變更的失敗可能會有很大的差異。 如需常見的失敗，請參閱布建記錄[檔](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs#error-codes)。


## <a name="frequently-asked-questions"></a>常見問題集
**您需要關閉布建以使用隨選布建嗎？** 針對使用長期持有人權杖或使用者名稱和密碼進行授權的應用程式，不需要任何其他步驟。 使用 OAuth 進行授權的應用程式目前需要先停止布建作業，才能使用隨選布建。 G Suite、Box、Workplace by Facebook 和時差等應用程式都屬於此類別。 工作正在進行中，以允許對所有應用程式執行隨選布建，而不需停止布建。 

**隨選布建需要多久的時間？** 通常不到30秒的時間。 

## <a name="known-limitations"></a>已知限制
今天有幾個已知的限制。 請在[使用者心聲](https://aka.ms/appprovisioningfeaturerequest)張貼，讓我們能夠更妥善設定下一個改進功能的優先順序。 請注意，這些限制適用于隨選布建功能。 如需有關應用程式是否支援布建群組、刪除等的詳細資訊，請參閱應用程式教學課程。 

* 應用程式 Workday、AWS 和 SuccessFactors 不支援隨選布建。
* 不支援隨選布建群組和角色。
* 隨選布建支援停用已從應用程式取消指派的使用者，但不支援停用或刪除已從 Azure Active Directory 停用或刪除的使用者（搜尋使用者時不會出現這些使用者）。

## <a name="next-steps"></a>後續步驟

* [布建疑難排解](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem)

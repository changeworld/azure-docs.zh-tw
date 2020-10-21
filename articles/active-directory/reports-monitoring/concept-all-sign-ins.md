---
title: Azure Active Directory 登入活動報告-預覽 |Microsoft Docs
description: Azure Active Directory 入口網站中的登入活動報告簡介
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 09/23/2020
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36e6642e230fa809630751e224ff9384ea8524d1
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92319785"
---
# <a name="azure-active-directory-sign-in-activity-reports---preview"></a>Azure Active Directory 登入活動報告-預覽

Azure Active Directory (Azure AD) 中的報告架構包含下列元件：

- **活動** 
    - 登**入-使用者**、應用程式和受控資源登入以 Azure AD 並存取資源時的相關資訊。
    - **審核記錄**  - [Audit 記錄](concept-audit-logs.md)檔提供有關使用者和群組管理、受控應用程式和目錄活動的系統活動資訊。
- **安全性** 
    - 有**風險**的登入：有[風險](../identity-protection/overview-identity-protection.md)的登入是指使用者帳戶不是合法擁有者的登入指示。
    - **標示為有風險的使用者** -有 [風險的使用者](../identity-protection/overview-identity-protection.md) 是指可能遭到盜用的使用者帳戶指標。

Azure Active Directory 中的傳統登入報告提供互動式使用者登入的總覽。此外，您現在可以存取三個現在處於預覽狀態的額外登入報告：

- 非互動式使用者登入

- 服務主體登入

- 適用于 Azure 資源登入的受控識別

本文提供 Azure 資源登入的非互動式、應用程式和受控識別預覽，讓您瞭解登入活動報告。如需不含預覽功能之登入報告的詳細資訊，請參閱  [Azure Active Directory 入口網站中的登入活動報告](concept-sign-ins.md)。



## <a name="prerequisites"></a>先決條件

開始使用這項功能之前，您應該先知道下列問題的答案：

- 誰可以存取資料？

- 您需要哪項 Azure AD 授權才能存取登入活動？

### <a name="who-can-access-the-data"></a>誰可以存取資料？

- 安全性系統管理員、安全性讀取者和報表讀取者角色中的使用者

- 全域系統管理員

- 任何使用者 (非系統管理員) 都可以存取自己的登入 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>您需要哪項 Azure AD 授權才能存取登入活動？

您的租使用者必須有與其相關聯的 Azure AD Premium 授權，才能看到登入活動。 請參閱[開始使用 Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) 來升級 Azure Active Directory 版本。 升級至 premium 授權之後，在升級之前沒有任何資料活動，將需要幾天的時間才會在報表中顯示資料。



## <a name="sign-ins-report"></a>登入報告

登入報告可提供下列問題的答案：

- 使用者、應用程式或服務的登入模式為何？
- 一周內有多少使用者、應用程式或服務登入？
- 這些登入的狀態為何？


在 [登入報告] 分頁中，您可以在下列兩者之間切換：

- **互動式使用者登入** -使用者提供驗證因素的登入，例如密碼、透過 MFA 應用程式的回應、生物特徵辨識因素或 QR 代碼。

- **非互動式使用者登入** -用戶端代表使用者執行的登入。 這些登入不需要使用者進行任何互動或驗證因素。 例如，使用不需要使用者輸入認證的重新整理和存取權杖來進行驗證和授權。

- **服務主體登入** -依應用程式和服務主體登入，而不涉及任何使用者。 在這些登入中，應用程式或服務會代表自己提供認證，以驗證或存取資源。

- **適用于 azure 資源的受控** 識別登入-透過 azure 資源登入，這些資源具有由 azure 管理的秘密。 如需詳細資訊，請參閱 [什麼是適用于 Azure 資源的受控識別？](../managed-identities-azure-resources/overview.md) 


![登入報告類型](./media/concept-all-sign-ins/sign-ins-report-types.png)












## <a name="user-sign-ins"></a>使用者登入

[登入] 分頁中的每個索引標籤都會顯示以下的預設資料行。 某些索引標籤有額外的資料行：

- 登入日期

- 要求識別碼

- 使用者名稱或使用者識別碼

- 應用程式名稱或應用程式識別碼

- 登入狀態

- 用於登入之裝置的 IP 位址



### <a name="interactive-user-sign-ins"></a>互動式使用者登入


互動式使用者登入是一種登入，使用者可在其中提供驗證因素來 Azure AD 或直接與 Azure AD 或協助程式應用程式互動，例如 Microsoft Authenticator 應用程式。 使用者提供的因素包括密碼、MFA 挑戰的回應、生物識別因素，或使用者提供給 Azure AD 或協助程式的 QR 代碼。

這份報表也包含同盟登入，來自與 Azure AD 的身分識別提供者。  


**報表大小：** 小型 <br> 
**例子：**

- 使用者會在 Azure AD 登入畫面中提供使用者名稱和密碼。

- 使用者通過 SMS MFA 挑戰。

- 使用者提供生物特徵辨識手勢，以 Windows Hello 企業版將其 Windows 電腦解除鎖定。

- 使用者已與 AD FS 的 SAML 判斷提示 Azure AD 同盟。


除了預設欄位以外，互動式登入報告也會顯示： 

- 登入位置

- 是否已套用條件式存取



您可以按一下工具列中的 [資料 **行** ]，以自訂清單查看。

![互動式使用者登入資料行](./media/concept-all-sign-ins/columns-interactive.png "互動式使用者登入資料行")





自訂此視圖可讓您顯示其他欄位，或移除已顯示的欄位。

![所有互動式資料行](./media/concept-all-sign-ins/all-interactive-columns.png)


在清單視圖中選取一個專案，以取得相關登入的詳細資訊。

![登入活動](./media/concept-all-sign-ins/interactive-user-sign-in-details.png "互動式使用者登入")



### <a name="non-interactive-user-sign-ins"></a>非互動式使用者登入

非互動式使用者登入是由用戶端應用程式或作業系統元件代表使用者所執行的登入。 就像互動式使用者登入一樣，這些登入都是由使用者來完成。 不同于互動式使用者登入，這些登入不需要使用者提供驗證因素。 相反地，裝置或用戶端應用程式會使用權杖或程式碼來代表使用者驗證或存取資源。 一般情況下，使用者會在使用者活動的背景中察覺這些登入。


**報表大小：** 大 <br>
**範例：** 

- 用戶端應用程式會使用 OAuth 2.0 重新整理權杖來取得存取權杖。

- 用戶端會使用 OAuth 2.0 授權碼來取得存取權杖和重新整理權杖。

- 使用者會在已加入 Azure AD 的電腦上，對 web 或 Windows 應用程式執行單一登入 (SSO) 。

- 當使用者在行動裝置上具有使用 FOCI (系列用戶端識別碼) 的會話時，會登入第二個 Microsoft Office 應用程式。




除了預設欄位以外，非互動式登入報告也會顯示： 

- 資源識別碼

- 群組的登入數




您無法自訂此報表中顯示的欄位。


![停用的資料行](./media/concept-all-sign-ins/disabled-columns.png "停用的資料行")

為了更輕鬆地將資料摘要，非互動式登入事件會進行分組。 用戶端通常會在短時間內代表相同的使用者建立許多非互動式登入，這會共用所有相同的特性，但嘗試登入的時間除外。 例如，用戶端可能會代表使用者每小時取得一次存取權杖。 如果使用者或用戶端未變更狀態，則每個存取權杖要求的 IP 位址、資源和其他所有資訊都相同。 當 Azure AD 記錄的多個登入與 [時間] 和 [日期] 不同時，這些登入將會從相同的實體匯總成單一資料列。 具有多個相同登入的資料列 (除了發出的日期和時間之外) 在 # 登入資料行中的值大於1。 您可以展開資料列，以查看所有不同的登入及其不同的時間戳記。 當下列資料相符時，登入會在非互動式使用者中匯總：


- 應用程式

- User

- IP 位址

- 狀態

- 資源識別碼


您可以：

- 展開節點以查看群組的個別專案。  

- 按一下個別專案以查看所有詳細資料 


![非互動式使用者登入詳細資料](./media/concept-all-sign-ins/non-interactive-sign-ins-details.png)




## <a name="service-principal-sign-ins"></a>服務主體登入

不同于互動式和非互動式使用者登入，服務主體登入不需要使用者。 相反地，它們是由任何非使用者帳戶（例如應用程式或服務主體）登入，但僅包含在受控識別登入報告) 中的受控識別登入 (例外。 在這些登入中，應用程式或服務會提供自己的認證，例如憑證或應用程式密碼來驗證或存取資源。


**報表大小：** 大 <br>
**範例：**

- 服務主體會使用憑證來驗證和存取 Microsoft Graph。 

- 應用程式會使用用戶端密碼在 OAuth 用戶端認證流程中進行驗證。 


這份報表有一個預設的清單視圖，顯示：

- 登入日期

- 要求識別碼

- 服務主體名稱或識別碼

- 狀態

- IP 位址

- 資源名稱

- 資源識別碼

- 登入次數

您無法自訂此報表中顯示的欄位。

![停用的資料行](./media/concept-all-sign-ins/disabled-columns.png "停用的資料行")

為了讓您更輕鬆地將服務主體登入記錄中的資料摘要，會將服務主體登入事件分組。 相同條件下的相同實體登入會匯總成單一資料列。 您可以展開資料列，以查看所有不同的登入及其不同的時間戳記。 當下列資料相符時，登入就會在服務主體報告中匯總：

- 服務主體名稱或識別碼

- 狀態

- IP 位址

- 資源名稱或識別碼

您可以：

- 展開節點以查看群組的個別專案。  

- 按一下個別專案，以查看所有詳細資料 


![資料行詳細資料](./media/concept-all-sign-ins/service-principals-sign-ins-view.png "資料行詳細資料")




## <a name="managed-identity-for-azure-resources-sign-ins"></a>適用于 Azure 資源的受控識別登入 

適用于 Azure 資源的受控識別登入是由 Azure 所管理的資源所執行的登入，可簡化認證管理。

**報表大小：** 小 <br> 
**例子：**

具有受控認證的 VM 會使用 Azure AD 來取得存取權杖。   


這份報表有一個預設的清單視圖，顯示：


- 受控識別識別碼

- 受控識別名稱

- 資源

- 資源識別碼

- 群組的登入數

您無法自訂此報表中顯示的欄位。

為了更輕鬆地將資料摘要，適用于 Azure 資源的受控識別登入記錄、非互動式登入事件都會分組。 從相同實體登入會匯總成單一資料列。 您可以展開資料列，以查看所有不同的登入及其不同的時間戳記。 當下列所有資料相符時，登入就會匯總在受控識別報告中：

- 受控識別名稱或識別碼

- 狀態

- IP 位址

- 資源名稱或識別碼

在清單視圖中選取一個專案，以顯示在某個節點下分組的所有登入。

選取群組專案，以查看登入的所有詳細資料。 


## <a name="filter-sign-in-activities"></a>篩選登入活動

藉由設定篩選準則，您可以縮小所傳回登入資料的範圍。 Azure AD 提供您可以設定的各種額外篩選準則。 設定篩選準則時，請務必特別注意設定的 **日期** 範圍篩選準則。 適當的日期範圍篩選準則可確保 Azure AD 只會傳回您真正在意的資料。     

**日期**範圍篩選器可讓您定義傳回資料的時間範圍。
可能的值包括：

- 一個月

- 7 天

- 二十個小時

- 自訂

![日期範圍篩選準則](./media/concept-all-sign-ins/date-range-filter.png)





### <a name="filter-user-sign-ins"></a>篩選使用者登入

互動式和非互動式登入的篩選器相同。 基於這個原因，您為互動式登入設定的篩選器會保留給非互動式登入，反之亦然。 






## <a name="access-the-new-sign-in-activity-reports"></a>存取新的登入活動報告 

Azure 入口網站中的 [登入活動] 報表會提供您一個簡單的方法來切換預覽報表。 如果您已啟用預覽報表，您會看到新的功能表，可讓您存取所有登入活動報表類型。     


若要使用非互動式和應用程式登入來存取新的登入報告： 

1. 在 [Azure 入口網站] 中，選取 [Azure Active Directory]。

    ![選取 Azure AD](./media/concept-all-sign-ins/azure-services.png)

2. 在 [ **監視** ] 區段中，按一下 [登 **入**]。

    ![選取登入](./media/concept-all-sign-ins/sign-ins.png)

3. 按一下 [ **預覽** ] 列。

    ![啟用新的視圖](./media/concept-all-sign-ins/enable-new-preview.png)

4. 若要切換回預設視圖，請再次按一下 [ **預覽** ] 列。 

    ![還原傳統視圖](./media/concept-all-sign-ins/switch-back.png)







## <a name="download-sign-in-activity-reports"></a>下載登入活動報告

當您下載登入活動報告時，會發生下列情況：

- 您可以將登入報表下載為 CSV 或 JSON 檔案。

- 您最多可以下載 100-K 筆記錄。 如果您想要下載更多資料，請使用報告 API。

- 您的下載是以您所選的篩選器為基礎。

- 您可以下載的記錄數目會受限於 [Azure Active Directory 報告保留原則](reference-reports-data-retention.md)。 


![下載報表](./media/concept-all-sign-ins/download-reports.png "下載報表")


每個 CSV 下載都包含六個不同的檔案：

- 互動式登入

- 互動式登入的驗證詳細資料

- 非互動式登入

- 非互動式登入的驗證詳細資料

- 服務主體登入

- 適用于 Azure 資源的受控識別登入

每個 JSON 下載都是由四個不同的檔案所組成：

- 互動式登入 (包含驗證詳細資料) 

- 非互動式登入 (包含驗證詳細資料) 

- 服務主體登入

- 適用于 Azure 資源的受控識別登入

![下載檔案](./media/concept-all-sign-ins/download-files.png "下載檔案")




## <a name="next-steps"></a>後續步驟

* [登入活動報告錯誤碼](reference-sign-ins-error-codes.md)
* [Azure AD 資料保留原則](reference-reports-data-retention.md)
* [Azure AD 報告延遲](reference-reports-latencies.md)
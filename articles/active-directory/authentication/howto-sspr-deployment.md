---
title: 自助服務密碼重置部署 ─ Azure 活動目錄
description: 成功測試 Azure AD 自助服務密碼重置的策略
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: baselden
author: barbaraselden
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7be99959c2ae420cff667491f68c40dfa0862a9
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652396"
---
# <a name="plan-an-azure-active-directory-self-service-password-reset"></a>計畫 Azure 活動目錄自助服務密碼重置

> [!NOTE]
> 此部署計畫提供了部署 Azure AD 自助服務密碼重置 (SSPR) 的規劃指南和最佳實務。 <br>**如果您要尋找 SSPR 工具來傳回您的帳號,請[https://aka.ms/sspr](https://aka.ms/sspr)轉到**。

[自助服務密碼重置 (SSPR)](https://www.youtube.com/watch?v=tnb2Qf4hTP8)是 Azure 活動目錄 (AD) 功能,使用戶能夠重置其密碼,而無需聯繫 IT 人員尋求説明。 用戶可以快速解除自我阻止,並繼續工作,無論他們在哪裡或一天的時間。 通過允許員工自行解除阻止,您的組織可以減少大多數常見與密碼相關的問題的非生產時間和高支援成本。 

SSPR 具有以下關鍵功能:

* 自助服務允許最終使用者重置其過期或未過期的密碼,而無需聯繫管理員或説明台尋求支援。

* [密碼回寫](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)允許管理本地密碼,並解決通過雲的帳戶鎖定問題。

* 密碼管理活動報告使管理員能夠深入瞭解其組織中發生的密碼重置和註冊活動。

## <a name="learn-about-sspr"></a>瞭解 SSPR

瞭解有關 SSPR 的更多詳細資訊。 請參考[其工作原理:Azure AD 自助服務密碼重置](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks)。

### <a name="key-benefits"></a>主要權益

開啟 SSPR 的主要好處是:

* **管理成本**。 SSPR 允許使用者自行重置密碼,從而降低 IT 支援成本。 它還減少了由於密碼丟失和鎖定而損失的時間成本。 

* **直觀的使用者體驗**。 它提供了直觀的一次性使用者註冊過程,允許使用者重置密碼,並取消阻止任何設備或位置的帳戶。 SSPR 允許使用者更快地恢復工作並提高工作效率。

* **靈活性和安全性**。 SSPR 使企業能夠存取雲平臺提供的安全性和靈活性。 管理員可以更改設置以適應新的安全要求,並在不中斷其登錄的情況下向使用者推出這些更改。

* **強大的稽核與使用追蹤**。 組織可以確保業務系統在使用者重置自己的密碼時保持安全。 可靠的審核日誌包括密碼重置過程每個步驟的資訊。 這些日誌可從 API 獲得,並使用戶能夠將數據導入首選的安全事件和事件監視 (SIEM) 系統。

### <a name="licensing"></a>授權

Azure 活動目錄是每個使用者的許可,這意味著每個使用者都需要一個適當的許可證來用於他們使用的功能。 我們建議基於組的許可進行 SSPR。 

要比較版本和功能並啟用基於組或使用者的許可,請參閱[Azure AD 自助服務密碼重置的許可要求](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-licensing)。

有關定價的詳細資訊,請參閱[Azure 活動目錄定價](https://azure.microsoft.com/pricing/details/active-directory/)。

### <a name="prerequisites"></a>Prerequisites

* 可運作且至少已啟用試用版授權的 Azure AD 租用戶。 如有需要，[請建立免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 具有全域系統管理員權限的帳戶。


### <a name="training-resources"></a>培訓資源

| 資源| 連結並描述 |
| - | - |
| 影片| [為您的使用者提供更好的 IT 可擴充性](https://youtu.be/g9RpRnylxS8) 
| |[什麼是自助服務密碼重置?](https://youtu.be/hc97Yx5PJiM)|
| |[部署自助服務密碼重置](https://www.youtube.com/watch?v=Pa0eyqjEjvQ&index=18&list=PLLasX02E8BPBm1xNMRdvP6GtA6otQUqp0)|
| |[如何為 Azure AD 中的使用者配置自助服務密碼重置?](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/) |
| |[如何 [準備使用者] 註冊 Azure 活動目錄的安全資訊](https://youtu.be/gXuh0XS18wA) |
| 線上課程|[管理 Microsoft Azure 活動目錄中的識別](https://www.pluralsight.com/courses/microsoft-azure-active-directory-managing-identities)使用 SSPR 為使用者提供現代、受保護的體驗。 請特別檢視「[管理 Azure」 活動目錄使用者和群組](https://app.pluralsight.com/library/courses/microsoft-azure-active-directory-managing-identities/table-of-contents)「 模組」。 |
|複數付費課程 |[身份與存取管理問題](https://www.pluralsight.com/courses/identity-access-management-issues)瞭解組織中需要注意的IAM和安全問題。 請參閱"其他身份驗證方法"模組。|
| |[開始使用 Microsoft 企業移動套件](https://www.pluralsight.com/courses/microsoft-enterprise-mobility-suite-getting-started)瞭解以允許身份驗證、授權、加密和安全行動體驗的方式將本地資產擴展到雲端的最佳做法。 請特別查看「設定 Microsoft Azure 活動目錄高級版的進階功能」模組。
|教學課程 |[完成 Azure AD 自助密碼重置試點推出](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot) |
| |[啟用密碼回寫](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-writeback) |
| |[Azure AD 密碼從 Windows 10 的登入螢幕重置](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows) |
| 常見問題集|[密碼管理常見問題](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-faq) |


### <a name="solution-architecture"></a>方案架構

下面的範例描述了常見混合環境的密碼重置解決方案體系結構。

![解決方案結構圖](./media/howto-sspr-deployment//solutions-architecture.png)

工作流描述

要重置密碼,使用者轉到[密碼重置門戶](https://aka.ms/sspr)。 他們必須驗證以前註冊的身份驗證方法或方法,以證明其身份。 如果他們成功重置密碼,則開始重置過程。

* 對於僅限雲的使用者,SSPR 將新密碼存儲在 Azure AD 中。 

* 對於混合使用者,SSPR 通過 Azure AD 連接服務將密碼寫回預運行目錄。 

注意:對於禁用[密碼哈希同步 (PHS)](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs)的使用者,SSPR 僅將密碼存儲在打開狀態的活動目錄中。

### <a name="best-practices"></a>最佳作法

通過將 SSPR 與組織中的另一個常用應用程式或服務一起部署,可以幫助使用者快速註冊。 此操作將生成大量登錄,並驅動註冊。

在部署 SSPR 之前,您可以選擇確定每個密碼重置調用的數量和平均成本。 您可以使用此資料後部署來顯示 SSPR 給組織帶來的值。

#### <a name="enable-combined-registration-for-sspr-and-mfa"></a>開啟 SSPR 與 MFA 的組合註冊

Microsoft 建議組織啟用 SSPR 和多重身份驗證的組合註冊體驗。 啟用此合併註冊體驗時,使用者只需選擇一次註冊資訊,即啟用這兩種功能。

組合註冊體驗不需要組織同時啟用 SSPR 和 Azure 多重身份驗證。 合併註冊為組織提供更好的用戶體驗。 有關詳細資訊,請參閱[組合安全資訊註冊(預覽)](concept-registration-mfa-sspr-combined.md)

## <a name="plan-the-deployment-project"></a>規劃部署項目

在確定環境中此部署的策略時,請考慮組織需求。

### <a name="engage-the-right-stakeholders"></a>讓合適的利益相關者參與進來

當技術項目失敗時,它們通常是由於對影響、結果和責任的期望不匹配而達到這一要求。 為了避免這些陷阱,[確保您與合適的利益相關者接觸](https://aka.ms/deploymentplans),並通過記錄利益相關者及其專案投入和問責制,充分理解專案中的利益相關者角色。

#### <a name="required-administrator-roles"></a>必要系統管理員角色


| 商務角色/人物| Azure AD 角色(如有必要) |
| - | - |
| 1 級説明台| 密碼管理員 |
| 2 級説明台| 使用者管理員 |
| SSPR 管理員| 全域管理員 |


### <a name="plan-communications"></a>規劃通訊

溝通對於任何新服務的成功都至關重要。 您應該主動與用戶溝通他們的體驗將如何改變、何時更改,以及如果他們遇到問題,如何獲得支援。 查看[Microsoft 下載中心的自助服務密碼重置推出材料](https://www.microsoft.com/download/details.aspx?id=56768),瞭解如何規劃最終使用者通信策略。

### <a name="plan-a-pilot"></a>規劃試點

我們建議 SSPR 的初始配置位於測試環境中。 通過為組織中的使用者子集啟用 SSPR,從試驗組開始。 請參閱[飛行員的最佳做法](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans)。

要建立群組,請參閱如何在[Azure 活動目錄中建立群組和新增成員](https://docs.microsoft.com/azure/active-directory/active-directory-groups-create-azure-portal)。 

## <a name="plan-configuration"></a>排程設定

啟用 SSPR 以及建議的值需要以下設定。

| 區域 | 設定 | 值 |
| --- | --- | --- |
| **SSPR 屬性** | 開啟自助服務密碼重設 | **在試驗的群組**/**全部**用於生產 |
| **驗證方法** | 註冊需要的身份驗證方法 | 始終比重置所需的多 1 個 |
|   | 重置所需的身份驗證方法 | 一個或兩個 |
| **註冊** | 登入時要求使用者註冊 | 是 |
|   | 要求使用者重新確認其驗證資訊的等候天數 | 90 ~ 180 天 |
| **通知** | 通知使用者密碼重設 | 是 |
|   | 當其他系統管理員重設其密碼時通知所有系統管理員 | 是 |
| **自訂** | 自訂幫助台連結 | 是 |
|   | 自訂說明電子郵件或網址 | 支援網站或電子郵件地址 |
| **內部部署整合** | 將密碼寫回本地 AD | 是 |
|   | 允許使用者在不重置密碼的情況下解鎖帳戶 | 是 |

### <a name="sspr-properties"></a>SSPR 屬性

啟用 SSPR 時,在試驗環境中選擇適當的安全組。

* 要為每個人強制實施 SSPR 註冊,我們建議使用 **"全部"** 選項。
* 否則,請選擇相應的 Azure AD 或 AD 安全組。

### <a name="authentication-methods"></a>驗證方法

啟用 SSPR 後,使用者僅當管理員啟用的身份驗證方法中存在數據時,才能重置其密碼。 方法包括電話、身份驗證器應用通知、安全問題等。有關詳細資訊,請參閱[什麼是身份驗證方法?](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)

我們建議使用以下身份驗證方法設定:

* 將**註冊所需的身份驗證方法**設置為至少比重置所需的數量多一個。 允許使用者在需要重置時靈活進行身份驗證。

* 設定重置為適合您的組織的等級**所需的方法數**。 一個要求最少的摩擦,而兩個可能會增加你的安全姿勢。 

注意: 使用者必須在[Azure 活動目錄中的密碼策略和限制中](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy)配置身份驗證方法。

### <a name="registration-settings"></a>註冊設定

設置**要求使用者在登錄到** **「是**」時進行註冊。 此設置要求使用者在登錄時進行註冊,以確保所有使用者都受到保護。

設置**要求使用者重新確認其身份驗證資訊的天數**在**90**到**180**天之間,除非您的組織需要更短的時間範圍。

### <a name="notifications-settings"></a>通知設定

**當其他管理員將密碼重置**為 **「是**」時,請設定 **「密碼重置時通知使用者**」和「通知所有管理員」。 通過確保使用者知道重置密碼時,在這兩個上選擇 **「是**」可提高安全性。 它還確保所有管理員在管理員更改密碼時都瞭解。 如果使用者或管理員收到通知,但他們還沒有啟動更改,他們可以立即報告潛在的安全問題。

### <a name="customization-settings"></a>自訂設定

自定義説明台電子郵件或 URL 以確保遇到問題的用戶能夠立即獲得説明,這一點至關重要。 將此選項設置為使用者熟悉的公共説明台電子郵寄地址或網頁。 

有關詳細資訊,請參閱自訂[Azure AD 功能以進行自助服務密碼重置](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization)。

### <a name="password-writeback"></a>密碼回寫

使用 Azure AD[連接](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity)啟用**密碼回寫**,並在雲端即時將密碼重置寫入現有本地目錄。 有關詳細資訊,請參閱[什麼是密碼回寫?](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)

我們建議使用以下設定:

* 確保**將密碼寫回本地 AD**設定為 **「是**」。 
* 將 **「允許使用者解鎖帳戶而不重置密碼**為**是**」。。

根據預設，Azure AD 可以在執行密碼重設時解除鎖定帳戶。

### <a name="administrator-password-setting"></a>管理員密碼設定

管理員帳戶具有提升的許可權。 本地企業或域管理員無法通過 SSPR 重置其密碼。 本地管理員帳戶具有以下限制:

* 只能在其預置環境中更改其密碼。
* 永遠不能使用秘密問題和答案作為重置密碼的方法。

我們建議您不要將上置活動目錄管理員帳戶與 Azure AD 同步。

### <a name="environments-with-multiple-identity-management-systems"></a>具有多個識別管理系統的環境

某些環境具有多個標識管理系統。 預置識別管理員(如 Oracle AM 和 SiteMinder)需要與 AD 同步才能獲得密碼。 您可以使用 Microsoft 識別管理員 (MIM) 的密碼更改通知服務 (PCNS) 等工具執行此操作。 要查找有關此更複雜的方案的資訊,請參閱在[域控制器上部署 MIM 密碼更改通知服務](https://docs.microsoft.com/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller)一文。

## <a name="plan-testing-and-support"></a>排建測試並支援

在從初始試驗組到全組織的部署每個階段,確保結果按預期進行。

### <a name="plan-testing"></a>排建測試

為確保部署按預期工作,請規劃一組測試用例以驗證實現。 要評估測試用例,您需要一個帶密碼的非管理員測試使用者。 如果需要建立使用者,請參閱[將新使用者新增到 Azure 活動目錄](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)。

下表包括有用的測試方案,可用於根據策略記錄組織的預期結果。
<br>


| 商業案例| 預期的結果 |
| - | - |
| SSPR 門戶可從企業網路內訪問| 由您的組織決定 |
| SSPR 門戶可從企業網路外部存取| 由您的組織決定 |
| 未啟用使用者密碼重置時,從瀏覽器重置使用者密碼| 使用者無法存取密碼重置流 |
| 當使用者尚未註冊密碼重置時,從瀏覽器重置使用者密碼| 使用者無法存取密碼重置流 |
| 使用者登入時強制執行進行密碼重置註冊| 提示使用者註冊安全資訊 |
| 密碼重置註冊完成後使用者登入| 提示使用者註冊安全資訊 |
| 當使用者沒有授權時,SSPR 門戶是可存取的| 可存取 |
| 從 Windows 10 Azure AD 聯接或混合 Azure AD 加入裝置鎖定螢幕重置使用者密碼| 使用者可以重置密碼 |
| SSPR 註冊和使用資料可近乎即時地提供給管理員| 可透過稽核紀錄 |

您還可以參考[完成 Azure AD 自助服務密碼重置試試卷](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot)。 在本教學中,您將在組織中啟用 SSPR 的試點推出,並使用非管理員帳戶進行測試。

### <a name="plan-support"></a>計劃支援

雖然 SSPR 通常不會造成用戶問題,但重要的是讓支援人員做好準備,以處理可能出現的問題。 雖然管理員可以通過 Azure AD 門戶重置最終用戶的密碼,但最好通過自助服務支持過程説明解決問題。

要使支援團隊取得成功,您可以根據從使用者收到的問題創建常見問題解答。 以下是一些範例：

| 案例| 描述 |
| - | - |
| 使用者沒有任何可用的註冊身份驗證方法| 使用者嘗試重置其密碼,但沒有他們註冊的任何身份驗證方法可用(例如:他們離開手機在家,無法訪問電子郵件) |
| 使用者在辦公室或手機上未收到簡訊或呼叫| 用戶嘗試通過文本或呼叫驗證其身份,但未收到文本/呼叫。 |
| 使用者無法存取密碼重置門戶| 使用者希望重置其密碼,但未啟用密碼重置功能,並且無法訪問頁面以更新密碼。 |
| 使用者無法設定新密碼| 使用者在密碼重置流期間完成驗證,但無法設置新密碼。 |
| 使用者在 Windows 10 裝置上看不到重置密碼連結| 使用者嘗試從 Windows 10 鎖定螢幕介面重置密碼,但裝置未加入 Azure AD,或者未啟用 Intune 裝置策略 |

### <a name="plan-roll-back"></a>排程

要回捲動部署:

* 對於單個使用者,請從安全組中刪除該使用者 

* 對您群組,從 SSPR 設定中移除群組

* 對於所有人,禁用 Azure AD 租戶的 SSPR

## <a name="deploy-sspr"></a>部署 SSPR

在部署之前,請確保已完成以下操作:

1. 建立並開始執行您的[溝通計畫](#plan-communications)。

1. 確定適當的[設定設定](#plan-configuration)。

1. 標識[了試驗](#plan-a-pilot)環境和生產環境的使用者和組。

1. [已確定的](#plan-configuration)註冊和自助服務配置設置。

1. 如果混合環境設定了[密碼寫回](#password-writeback)。


**您現在已準備好部署 SSPR!**

有關設定以下區域的完整分步說明,請參閱[啟用自助服務密碼重置](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot#enable-self-service-password-reset)。

1. [驗證方法](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)

1. [註冊設定](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined)

1. [通知設定](#notifications-settings)

1. [自訂設定](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization)

1. [內部部署整合](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-writeback)

### <a name="enable-sspr-in-windows"></a>在視窗中啟用 SSPR
對於執行 Windows 7、8、8.1 與 10 的電腦,您可以[允許使用者在 Windows 登入螢幕重置密碼](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-windows)

## <a name="manage-sspr"></a>管理 SSPR

Azure AD 可以通過審核和報告提供有關 SSPR 性能的其他資訊。

### <a name="password-management-activity-reports"></a>密碼管理活動報告 

您可以在 Azure 門戶上使用預建構的報表來測量 SSPR 性能。 如果您已適當地取得授權，則也可以建立自訂查詢。 有關詳細資訊,請參閱[Azure AD 密碼管理的報告選項](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-reporting)

> [!NOTE]
>  您必須[是全域管理員](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles),並且必須選擇加入才能為您的組織收集此數據。 要加入加入,必須至少訪問 Azure 門戶上的「報告」選項卡或審核日誌一次。 在此之前,不會為您的組織收集數據。

註冊和密碼重置的審核日誌提供 30 天。 如果公司內的安全審核需要更長的保留時間,則需要將日誌匯出並消耗到 SIEM 工具中,如 Azure Sentinel、Splunk 或[Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-active-directory)ArcSight。

![SSPR 報告螢幕擷取](./media/howto-sspr-deployment/sspr-reporting.png)

### <a name="authentication-methods--usage-and-insights"></a>認證方法 ─使用與見解

[使用和見解](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights)使您能夠瞭解 Azure MFA 和 SSPR 等功能的身份驗證方法在組織中的工作方式。 此報告功能為您的組織提供了瞭解註冊哪些方法以及如何使用它們的方法的方法。

### <a name="troubleshoot"></a>疑難排解

* 請參閱[故障排除自助服務密碼重置](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-troubleshoot) 

* 遵循[密碼管理常見問題](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-faq) 

### <a name="helpful-documentation"></a>有用的文件

* [驗證方法有哪些？](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)

* [工作原理:Azure AD 自助服務密碼重置?](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks)

* [自訂 Azure AD 的自助式密碼重設功能](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization)

* [Azure Active Directory 中的密碼原則和限制](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy)

* [什麼是密碼回寫？](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)

## <a name="next-steps"></a>後續步驟

* 要開始部署 SSPR,請參閱[完成 Azure AD 自助密碼重置試驗推出](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot)

* [考慮 azure AD 密碼保護](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad)

* [考慮 Azure AD 智慧鎖定](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout)
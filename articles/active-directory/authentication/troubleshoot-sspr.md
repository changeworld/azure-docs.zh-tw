---
title: 針對自助式密碼重設進行疑難排解 - Azure Active Directory
description: 瞭解如何針對 Azure Active Directory 中自助式密碼重設的常見問題和解決步驟進行疑難排解
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 08/26/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c0ddd9ad395da5f5cf4d65ea8ccf849a6473263
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89663870"
---
# <a name="troubleshoot-self-service-password-reset-in-azure-active-directory"></a>針對 Azure Active Directory 中的自助式密碼重設進行疑難排解

Azure Active Directory (Azure AD) 自助式密碼重設 (SSPR) 可讓使用者重設其在雲端中的密碼。

如果您有 SSPR 的問題，下列疑難排解步驟和常見錯誤可能會有所説明。 如果您找不到問題的答案， [我們的支援小組隨時都](#contact-microsoft-support) 能協助您。

## <a name="sspr-configuration-in-the-azure-portal"></a>Azure 入口網站中的 SSPR 設定

如果您在 Azure 入口網站中看到或設定 SSPR 選項時發生問題，請參閱下列疑難排解步驟：

### <a name="i-dont-see-the-password-reset-section-under-azure-ad-in-the-azure-portal"></a>我在 Azure 入口網站中的 Azure AD 之下看不到 [密碼重設] 區段。

如果您沒有將 Azure AD 授權指派給執行此作業的系統管理員，您將不會看到 [ **密碼重設** ] 功能表選項。

若要將授權指派給有問題的系統管理員帳戶，請遵循 [指派、驗證和解決授權問題](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses)的步驟。

### <a name="i-dont-see-a-particular-configuration-option"></a>我看不到特定的設定選項。

UI 的許多元素在需要之前都是隱藏的。 在您尋找特定的設定選項之前，請確定已啟用此選項。

### <a name="i-dont-see-the-on-premises-integration-tab"></a>我看不到 [內部部署整合] 索引標籤。

只有當您已下載 Azure AD Connect 並已設定功能時，才會顯示內部部署密碼回寫。

如需詳細資訊，請參閱 [開始使用 Azure AD Connect](../hybrid/how-to-connect-install-express.md)。

## <a name="sspr-reporting"></a>SSPR 報告

如果您在 Azure 入口網站中遇到 SSPR 報告的問題，請參閱下列疑難排解步驟：

### <a name="i-dont-see-any-password-management-activity-types-in-the-self-service-password-management-audit-event-category"></a>我在 **自助密碼管理** audit 事件類別目錄中看不到任何密碼管理活動類型。

如果未將 Azure AD 授權指派給執行此作業的系統管理員，就會發生這種情況。

若要將授權指派給有問題的系統管理員帳戶，請遵循 [指派、驗證和解決授權問題](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses)的步驟。

### <a name="user-registrations-show-multiple-times"></a>使用者註冊顯示多個時間。

當使用者註冊時，我們目前會將每個註冊的個別資料片段記錄為個別事件。

如果您需要彙總此資料並以更有彈性的方式檢視，可以下載報告，並在 Excel 中以樞紐分析表開啟資料。

## <a name="sspr-registration-portal"></a>SSPR 註冊入口網站

如果您的使用者在註冊 SSPR 時發生問題，請參閱下列疑難排解步驟：

### <a name="the-directory-isnt-enabled-for-password-reset-the-user-may-see-an-error-that-reports-your-administrator-has-not-enabled-you-to-use-this-feature"></a>目錄未啟用密碼重設功能。 使用者可能會看到「您的系統管理員尚未允許您使用此功能」的錯誤。

您可以針對所有使用者、沒有使用者或針對選取的使用者群組啟用 SSPR。 目前只有一個 Azure AD 群組可以使用 Azure 入口網站啟用 SSPR。 在較廣泛的 SSPR 部署中，可支援巢狀群組。 請確定您選擇的群組中的使用者已獲派適當的授權。

在 [Azure 入口網站中，將 [ **已啟用自助式密碼重設] 設定** 變更為 [已 *選取* ] 或 [ *全部* ]，然後選取 [ **儲存**]。

### <a name="the-user-doesnt-have-an-azure-ad-license-assigned-the-user-may-see-an-error-that-reports-your-administrator-has-not-enabled-you-to-use-this-feature"></a>使用者未獲指派 Azure AD 授權。 使用者可能會看到「您的系統管理員尚未允許您使用此功能」的錯誤。

目前只有一個 Azure AD 群組可以使用 Azure 入口網站啟用 SSPR。 在較廣泛的 SSPR 部署中，可支援巢狀群組。 請確定您選擇的群組中的使用者已獲派適當的授權。 請參閱先前的疑難排解步驟，以視需要啟用 SSPR。

另請參閱疑難排解步驟，以確定執行設定選項的系統管理員已獲指派授權。 若要將授權指派給有問題的系統管理員帳戶，請遵循 [指派、驗證和解決授權問題](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses)的步驟。

### <a name="theres-an-error-processing-the-request"></a>處理要求時發生錯誤。

一般 SSPR 註冊錯誤可能是由許多問題所造成，但此錯誤通常是因為服務中斷或設定問題所導致。 如果您在重試 SSPR 註冊程式時繼續看到這個一般錯誤，請 [洽詢 Microsoft 支援服務](#contact-microsoft-support) 以取得其他協助。

## <a name="sspr-usage"></a>SSPR 使用方式

如果您或您的使用者在使用 SSPR 時遇到問題，請參閱下列疑難排解案例和解決步驟：

| 錯誤 | 解決方法 |
| --- | --- |
| 目錄未啟用密碼重設功能。 | 在 [Azure 入口網站中，將 [ **已啟用自助式密碼重設] 設定** 變更為 [已 *選取* ] 或 [ *全部* ]，然後選取 [ **儲存**]。 |
| 使用者未獲指派 Azure AD 授權。 | 如果您沒有將 Azure AD 授權指派給所需的使用者，就會發生這種情況。 若要將授權指派給有問題的系統管理員帳戶，請遵循 [指派、驗證和解決授權問題](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses)的步驟。 |
| 目錄已啟用密碼重設，但使用者的驗證資訊遺失或格式不正確。 | 請確定使用者已在目錄中的檔案上正確地建立連絡人資料格式。 如需詳細資訊，請參閱 [Azure AD 自助式密碼重設所用的資料](howto-sspr-authenticationdata.md)。 |
| 目錄已啟用密碼重設，原則設定為需要兩個驗證方法，但使用者只登記一個連絡資料。 | 請確定使用者至少有兩個正確設定的連絡人方法。 例如，擁有行動電話號碼和辦公室電話號碼。 |
| 目錄已啟用密碼重設，並已正確設定使用者，但無法連絡到使用者。 | 這可能是暫時性服務錯誤的結果，或是因為無法正確偵測到不正確的連絡人資料。 <br> <br> 如果使用者等候10秒，則會顯示 [再試一次] 和 [洽詢您的系統管理員] 連結。 如果使用者選取 [再試一次]，它會重試呼叫。 如果使用者選取 [洽詢您的系統管理員]，它會傳送表單電子郵件給系統管理員，要求對該使用者帳戶執行密碼重設。 |
| 使用者從未收到密碼重設 SMS 或來電。 | 原因可能是目錄中的電話號碼格式不正確。 請確定電話號碼的格式為 "+ 1 4251234567"。 <br> <br>密碼重設不支援延伸模組，即使您在目錄中指定，也是如此。 在進行呼叫之前，會先移除擴充功能。 使用沒有分機號碼的數位，或將分機號碼整合至私用分支 exchange 的電話號碼， (PBX) 。 |
| 使用者從未收到密碼重設電子郵件。 | 這個問題最常見的原因，是垃圾郵件篩選器拒絕了郵件。 請檢查您的垃圾郵件或刪除的郵件資料夾中是否有該電子郵件。 <br> <br> 此外，請確定使用者檢查的電子郵件帳戶是否已向 SSPR 註冊。 |
| 我已設定密碼重設原則，但是當系統管理員帳戶使用密碼重設時，不會套用該原則。 | Microsoft 會管理及控制系統管理員的密碼重設原則，以確保最高層級的安全性。 |
| 禁止使用者在一天當中嘗試重設密碼太多次。 | 自動節流機制可用來防止使用者在短時間內嘗試重設密碼太多次。 節流會在下列案例中發生： <br><ul><li>使用者嘗試在一個小時內驗證電話號碼 5 次。</li><li>使用者在一個小時內嘗試使用安全性問題關卡五次。</li><li>使用者在一個小時內嘗試重設相同使用者帳戶的密碼五次。</li></ul>如果使用者遇到此問題，則必須在最後一次嘗試後等候24小時。 使用者接著可以重設其密碼。 |
| 使用者在驗證其電話號碼時看到錯誤。 | 當輸入的電話號碼與檔案上的電話號碼不相符時，就會發生此錯誤。 請確定使用者在嘗試使用電話式方法來重設密碼時，輸入了完整的電話號碼，包括區碼和國碼。 |
| 處理要求時發生錯誤。 | 一般 SSPR 註冊錯誤可能是由許多問題所造成，但此錯誤通常是因為服務中斷或設定問題所導致。 如果您在重新嘗試 SSPR 註冊程式時繼續看到這個一般錯誤，請 [洽詢 Microsoft 支援服務](#contact-microsoft-support) 以取得其他協助。 |
| 內部部署原則違規 | 密碼不符合內部部署 Active Directory 密碼原則。 使用者必須定義符合複雜性或強度需求的密碼。 |
| 密碼不符合模糊原則 | 使用的密碼會出現在 [禁用密碼清單](./concept-password-ban-bad.md#how-are-passwords-evaluated) 中，而且無法使用。 使用者必須定義符合或超過禁用密碼清單原則的密碼。 |

## <a name="sspr-errors-that-a-user-might-see"></a>SSPR 使用者可能會看到的錯誤

在 SSPR 流程中，使用者可能會看到下列錯誤和技術詳細資料。 由於 SSPR 功能需要針對其帳戶啟用、設定或註冊，因此通常不會自行解決錯誤。

您可以使用下列資訊來瞭解問題，以及需要在 Azure AD 租使用者或個別使用者帳戶上修正的事項。

| 錯誤 | 詳細資料 | 技術詳細資訊 |
| --- | --- | --- |
| TenantSSPRFlagDisabled = 9 | 抱歉，因為您的系統管理員已為您的組織停用密碼重設，所以目前無法重設您的密碼。 您無法採取進一步的動作來解決這種情況。 請連絡您的系統管理員，並要求他們啟用此功能。<br /><br />若要深入了解，請參閱[忘記 Azure AD 密碼的說明](../user-help/active-directory-passwords-update-your-own-password.md#common-problems-and-their-solutions)。 | SSPR_0009：我們已偵測到您的系統管理員尚未啟用「密碼重設」。 請連絡您的系統管理員，並要求他們為組織啟用「密碼重設」。 |
| WritebackNotEnabled = 10 |抱歉，因為您的系統管理員尚未為貴組織啟用必要的服務，此時您無法重設密碼。 您無法採取進一步的動作來解決這種情況。 請洽詢您的系統管理員，並要求他們檢查您組織的設定。<br /><br />若要深入了解必要的服務，請參閱[設定密碼回寫](./tutorial-enable-sspr-writeback.md)。 | SSPR_0010：我們已偵測到「密碼回寫」尚未啟用。 請連絡您的系統管理員，並要求他們啟用「密碼回寫」。 |
| SsprNotEnabledInUserPolicy = 11 | 抱歉，因為您的系統管理員尚未為貴組織設定密碼重設，所以目前無法重設您的密碼。 您無法採取進一步的動作來解決這種情況。 請連絡您的系統管理員，並要求他們設定密碼重設。<br /><br />若要深入了解密碼重設設定，請參閱[快速入門：Azure AD 自助式密碼重設](./tutorial-enable-sspr.md)。 | SSPR_0011：您的組織尚未定義密碼重設原則。 請連絡您的系統管理員，並要求他們定義密碼重設原則。 |
| UserNotLicensed = 12 | 很抱歉，您目前無法重設您的密碼，因為您的組織缺少必要的授權。 您無法採取進一步的動作來解決這種情況。 請連絡您的系統管理員，並要求他們檢查授權指派。<br /><br />若要深入了解有關授權的詳細資訊，請參閱 [Azure AD 自助式密碼重設的授權需求](./concept-sspr-licensing.md)。 | SSPR_0012：您的組織沒有執行密碼重設所需的必要授權。 請連絡您的系統管理員，並要求他們檢閱授權指派。 |
| UserNotMemberOfScopedAccessGroup = 13 | 抱歉，因為您的系統管理員尚未將您的帳戶設定為使用密碼重設，所以目前無法重設您的密碼。 您無法採取進一步的動作來解決這種情況。 請連絡您的系統管理員，並要求他們設定您的帳戶以供密碼重設使用。<br /><br />若要深入了解密碼重設的帳戶設定，請參閱[為使用者推出密碼重設](./howto-sspr-deployment.md)。 | SSPR_0013：您不是已啟用密碼重設之群組的成員。 請連絡您的系統管理員，並要求加入群組。 |
| UserNotProperlyConfigured = 14 | 很抱歉，您目前無法重設您的密碼，因為帳戶缺少必要的資訊。 您無法採取進一步的動作來解決這種情況。 請連絡您的系統管理員，並要求他們為您重設密碼。 當您再次可以存取您的帳戶之後，必須註冊所需的資訊。<br /><br />若要註冊資訊，請遵循[註冊自助式密碼重設](../user-help/active-directory-passwords-reset-register.md)一文。 | SSPR_0014：重設密碼所需要的其他安全性資訊。 若要繼續進行，請連絡您的系統管理員，並要求他們重設您的密碼。 當您可以存取您的帳戶之後，可在 https://aka.ms/ssprsetup 註冊其他安全性資訊。 您的系統管理員可以遵循[設定與閱讀密碼重設的驗證資料](howto-sspr-authenticationdata.md)中的步驟，將其他安全性資訊新增至您的帳戶中。 |
| OnPremisesAdminActionRequired = 29 | 很抱歉，因為貴組織的密碼重設設定發生問題，所以目前無法重設您的密碼。 您無法採取進一步的動作來解決這種情況。 請連絡您的系統管理員，並要求他們調查。<br /><br />若要深入了解潛在問題，請參閱[疑難排解密碼回寫](troubleshoot-sspr-writeback.md)。 | SSPR_0029：因為您的內部部署設定發生錯誤，我們無法重設您的密碼。 請連絡您的系統管理員，並要求他們調查。 |
| OnPremisesConnectivityError = 30 | 很抱歉，因為您的組織有連線問題，所以目前無法重設您的密碼。 現在無需採取任何動作，但如果您稍後再試，可能會解決問題。 如果問題持續發生，請連絡您的系統管理員，並要求他們調查。<br /><br />若要深入了解連線問題，請參閱[針對密碼回寫連線問題進行疑難排解](troubleshoot-sspr-writeback.md)。 | SSPR_0030：因為您內部部署環境的連線不佳，我們無法重設您的密碼。 請連絡您的系統管理員，並要求他們調查。|

## <a name="azure-ad-forums"></a>Azure AD 論壇

如果您有關于 Azure AD 和自助式密碼重設的一般問題，您可以在 Azure Active Directory 的問題頁面中要求該社區提供 [Microsoft Q&](/answers/topics/azure-active-directory.html)的協助。 社群的成員包括工程師、產品經理、MVP 和夥伴 IT 專業人員。

## <a name="contact-microsoft-support"></a>連絡 Microsoft 支援

如果找不到問題的答案，我們的支援團隊一直都能進一步協助您。

為了正確地提供協助，我們會要求您在開啟案例時，提供盡可能詳細的資料。 這些詳細資料包含下列各項：

* **錯誤的一般描述**：錯誤為何？ 注意到何種行為？ 我們如何才能重現錯誤？ 請提供盡可能詳細的資料。
* **頁面**：您注意到錯誤時的所在頁面？ 盡可能包含 URL 和頁面的螢幕擷取畫面。
* **支援碼**：使用者看到錯誤時所產生的支援碼？
   * 若要找到支援碼，請重現錯誤，然後按一下畫面底部的 [支援碼] 連結，將所產生的 GUID 傳送給支援工程師。

    :::image type="content" source="./media/troubleshoot-sspr-writeback/view-support-code.png" alt-text="支援碼位於網頁瀏覽器視窗右下方。":::

  * 如果您所在的頁面底部沒有支援碼，請選取 F12，搜尋 SID 和 CID，然後將這兩個結果傳送給支援工程師。
* **日期、時間和時區**：請包含發生錯誤的精確日期和時間 (含時區)。
* **使用者識別碼**：看到錯誤的使用者是誰？ 例如，使用者 \@contoso.com。
   * 這是同盟使用者嗎？
   * 這是傳遞驗證使用者嗎？
   * 這是密碼雜湊同步使用者嗎？
   * 這是否僅限雲端的使用者？
* **授權**：使用者是否獲得指派 Azure AD 授權？
* **應用程式事件記錄**：如果您使用密碼回寫，而且錯誤位於您的內部部署基礎結構中，請包含來自 Azure AD Connect 伺服器的應用程式事件記錄壓縮複本。

## <a name="next-steps"></a>接下來的步驟

若要深入瞭解 SSPR，請參閱 [運作方式： Azure AD 自助式密碼重設](concept-sspr-howitworks.md) ，或 [自助式密碼重設回寫在 Azure AD 中的運作](concept-sspr-writeback.md)方式。

---
title: 自助式密碼重設深入探討-Azure Active Directory
description: 自助式密碼重設如何運作
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fa1c2627917bfe386c488470f6a78db4c51f2ec
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994259"
---
# <a name="how-it-works-azure-ad-self-service-password-reset"></a>運作方式：Azure AD 自助式密碼重設

Azure Active Directory (Azure AD) 自助式密碼重設 (SSPR) 可讓使用者直接變更或重設其密碼，而無需系統管理員或技術支援中心介入。 如果使用者的帳戶遭到鎖定，或使用者忘記其密碼，他們可以依照提示自行解除封鎖，並繼續工作。 當使用者無法登入其裝置或應用程式時，這項功能將可減少技術服務中心的通話量，並避免失去生產力。

> [!IMPORTANT]
> 本文說明如何將自助式密碼重設運作的系統管理員。 如果您是已註冊自助式密碼重設的終端使用者，且需要取回您的帳戶，請移至 [https://aka.ms/sspr](https://aka.ms/sspr) 。
>
> 如果您的 IT 小組尚未啟用重設您密碼的功能，請與您的技術服務人員聯繫以取得其他協助。

## <a name="how-does-the-password-reset-process-work"></a>密碼重設程式如何運作？

使用者可以使用 [SSPR 入口網站](https://aka.ms/sspr)重設或變更其密碼。 他們必須先註冊其所需的驗證方法。 當使用者存取 SSPR 入口網站時，Azure 平臺會考慮下列因素：

* 如何將頁面當地語系化？
* 使用者帳戶是否有效？
* 使用者屬於哪個組織？
* 使用者的密碼管理在哪裡？
* 使用者是否已獲得授權使用此功能？

當使用者從應用程式或頁面選取 [ **無法存取您的帳戶** ] 連結，或直接移至時 [https://aka.ms/sspr](https://passwordreset.microsoftonline.com) ，在 SSPR 入口網站中使用的語言會根據下列選項：

* 根據預設，瀏覽器地區設定會用來以適當的語言顯示 SSPR。 密碼重設體驗會當地語系化成 [Microsoft 365 支援](https://support.microsoft.com/office/what-languages-is-office-available-in-26d30382-9fba-45dd-bf55-02ab03e2a7ec)的相同語言。
* 如果您想要以特定的當地語系化語言連結到 SSPR，請附加 `?mkt=` 至密碼重設 URL 的結尾，以及所需的地區設定。
    * 例如，若要指定西班牙文的 *es* 地區設定，請使用 `?mkt=es-us`  -  [https://passwordreset.microsoftonline.com/?mkt=es-us](https://passwordreset.microsoftonline.com/?mkt=es-us) 。

在 SSPR 入口網站以所需的語言顯示之後，系統會提示使用者輸入使用者識別碼並通過 captcha。 Azure AD 現在會執行下列檢查，以驗證使用者是否能夠使用 SSPR：

* 檢查使用者是否已啟用 SSPR，並獲指派 Azure AD 授權。
  * 如果使用者未啟用 SSPR 或未獲指派授權，系統會要求使用者洽詢其系統管理員以重設其密碼。
* 檢查使用者是否已按照系統管理員原則，在其帳戶上定義正確的驗證方法。
  * 如果原則只需要一種方法，請檢查使用者是否已針對管理員原則所啟用的至少一個驗證方法定義適當的資料。
    * 如果未設定驗證方法，則建議使用者洽詢其系統管理員以重設其密碼。
  * 如果原則需要兩個方法，請檢查使用者是否已針對系統管理員原則所啟用的至少兩種驗證方法定義適當的資料。
    * 如果未設定驗證方法，則建議使用者洽詢其系統管理員以重設其密碼。
  * 如果將 Azure 系統管理員角色指派給使用者，則會強制執行強式雙閘道密碼原則。 如需詳細資訊，請參閱[系統管理員重設原則差異](concept-sspr-policy.md#administrator-reset-policy-differences)。
* 檢查是否在內部部署環境中管理使用者的密碼，例如 Azure AD 租使用者使用同盟、傳遞驗證或密碼雜湊同步處理：
  * 如果已設定 SSPR 回寫，且使用者的密碼是在內部部署環境中管理，則允許使用者進行驗證及重設其密碼。
  * 如果未部署 SSPR 回寫，且使用者的密碼是在內部部署環境中管理，系統會要求使用者洽詢其系統管理員以重設其密碼。

如果所有先前的檢查都順利完成，則會引導使用者完成重設或變更其密碼的程式。

> [!NOTE]
> SSPR 可能會在密碼重設過程中傳送電子郵件通知給使用者。 這些電子郵件會使用 SMTP 轉送服務來傳送，此服務會在數個區域間以主動-主動模式運作。
>
> SMTP 轉送服務會接收並處理電子郵件內文，但不會加以儲存。 可能包含客戶所提供資訊的 SSPR 電子郵件本文不會儲存在 SMTP 轉送服務記錄中。 記錄只包含通訊協定中繼資料。

若要開始使用 SSPR，請完成下列教學課程：

> [!div class="nextstepaction"]
> [教學課程：啟用 (SSPR) 的自助式密碼重設 ](tutorial-enable-sspr.md)

## <a name="registration-options"></a>註冊選項

使用者必須先註冊自己和要使用的驗證方法，使用者才能使用 SSPR 重設或變更其密碼。 如上一節所述，使用者必須註冊 SSPR，並已套用適當的授權。

### <a name="require-users-to-register-when-they-sign-in"></a>登入時要求使用者註冊

如果使用者使用 Azure AD 登入任何應用程式，您可以啟用此選項以要求使用者完成 SSPR 註冊。 此工作流程包括下列應用程式︰

* Microsoft 365
* Azure 入口網站
* 存取面板
* 同盟應用程式
* 使用 Azure AD 自訂應用程式

當您不需要註冊時，系統不會在登入期間提示使用者，但可以手動註冊。 使用者可以在 [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) 存取面板的 [**設定檔**] 索引標籤下，流覽或選取 [**註冊密碼重設**] 連結。

![Azure 入口網站中 SSPR 的註冊選項][Registration]

> [!NOTE]
> 使用者可以藉由選取 [ **取消** ] 或關閉視窗來關閉 SSPR 註冊入口網站。 不過，系統會在每次登入時提示他們註冊，直到他們完成註冊為止。
>
> 註冊 SSPR 的這個插斷會中斷使用者的連線（如果已登入）。

### <a name="set-the-number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>設定要求使用者重新確認其驗證資訊的等候天數

為確保驗證方法在需要重設或變更其密碼時正確無誤，您可以要求使用者在一段時間後確認其資訊已註冊的資訊。 只有當您啟用 [ **需要使用者在登入時註冊** ] 選項時，才能使用此選項。

提示使用者確認其註冊方法的有效值為從 *0* 到 *730* 天。 將此值設定為 *0* 表示不會要求使用者確認其驗證資訊。

## <a name="authentication-methods"></a>驗證方法

當使用者啟用 SSPR 時，他們必須註冊至少一個驗證方法。 強烈建議您選擇兩個或更多驗證方法，讓您的使用者在需要時無法存取一個方法時，擁有更大的彈性。 如需詳細資訊，請參閱 [什麼是驗證方法？](concept-authentication-methods.md)。

下列驗證方法適用于 SSPR：

* 行動應用程式通知
* 行動應用程式代碼
* 電子郵件
* 行動電話
* 辦公室電話
* 安全性問題

如果使用者已註冊系統管理員已啟用的驗證方法，則只能重設其密碼。

> [!WARNING]
> 需要帳戶指派的 Azure *系統管理員* 角色，才能使用 [系統管理員重設原則差異](concept-sspr-policy.md#administrator-reset-policy-differences)一節中所定義的方法。

![Azure 入口網站中的驗證方法選擇][Authentication]

### <a name="number-of-authentication-methods-required"></a>必要驗證方法數目

您可以設定使用者必須提供才能重設或解除鎖定其密碼的可用驗證方法數目。 這個值可以設定為 *一* 或 *兩個*。

使用者可以，也可以註冊多個驗證方法。 同樣地，強烈建議使用者註冊兩個或多個驗證方法，讓他們在需要時無法存取一個方法時，擁有更大的彈性。

如果使用者在嘗試使用 SSPR 時沒有註冊的必要方法數目下限，則會看到錯誤頁面，引導他們要求系統管理員重設其密碼。 如果您有現有的使用者註冊 SSPR，而且他們之後無法使用該功能，請注意如果您將現有的使用者註冊到兩個，就會將其所需的方法數目提高。 如需詳細資訊，請參閱下一節來 [變更驗證方法](#change-authentication-methods)。

#### <a name="mobile-app-and-sspr"></a>行動裝置應用程式和 SSPR

使用行動應用程式做為密碼重設的方法時（例如 Microsoft Authenticator 應用程式），適用下列考慮事項：

* 當系統管理員需要一個可用來重設密碼的方法時，驗證碼是唯一可用的選項。
* 當系統管理員需要使用兩種方法來重設密碼時，使用者除了任何其他已啟用的方法之外，還可以使用通知 **或** 驗證碼。

| 需要重設的方法數 | 一個 | 兩個 |
| :---: | :---: | :---: |
| 可用的行動應用程式功能 | 程式碼 | 程式碼或通知 |

當註冊自助式密碼重設時，使用者無法選擇註冊其行動應用程式 [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) 。 使用者可以在中註冊其行動應用程式 [https://aka.ms/mfasetup](https://aka.ms/mfasetup) ，或在的結合安全性資訊註冊中註冊 [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) 。

> [!IMPORTANT]
> 只有在需要方法時，才能選取驗證器應用程式作為唯一的驗證方法。 同樣地，在需要兩個方法時，不能選取驗證器應用程式和一個額外的方法。
>
> 當您將包含驗證器應用程式的 SSPR 原則設定為方法時，如果需要一個方法，則至少應選取一個額外的方法，而且必須在設定兩個方法時，選取至少兩個額外的方法。
>
> 這項需求是因為目前的 SSPR 註冊體驗不包含註冊驗證器應用程式的選項。 註冊驗證器應用程式的選項包含在新的 [綜合註冊體驗](./concept-registration-mfa-sspr-combined.md)中。
>
> 只有在需要 (一個方法時，才允許使用驗證器應用程式的原則) 或驗證器應用程式，而只有一個額外的方法 (當) 需要兩個方法時，可能會導致使用者無法註冊 SSPR，直到他們設定為使用新的結合註冊體驗為止。

### <a name="change-authentication-methods"></a>變更驗證方法

如果您從只有註冊一個重設或解除鎖定所需之驗證方法的原則開始，然後變更成兩個驗證方法，會發生什麼情況？

| 已註冊的方法數 | 所需的方法數 | 結果 |
| :---: | :---: | :---: |
| 1 或多個 | 1 | **能夠** 重設或解除鎖定 |
| 1 | 2 | **無法** 重設或解除鎖定 |
| 2 以上 | 2 | **能夠** 重設或解除鎖定 |

變更可用的驗證方法也可能會造成使用者的問題。 如果您變更使用者可使用的驗證方法類型，可能會不小心導致使用者在沒有最基本可用資料量時無法使用 SSPR。

請考慮下列範例案例：

1. 原始原則已設定兩個必要的驗證方法。 該原則僅使用辦公室電話號碼和安全性問題。
1. 管理員將原則變更為不再使用安全性問題，但允許使用行動電話和備用電子郵件。
1. 未填入行動電話或備用電子郵件欄位的使用者，現在無法重設其密碼。

## <a name="notifications"></a>通知

為了改善對密碼事件的認知，SSPR 可讓您設定使用者和身分識別系統管理員的通知。

### <a name="notify-users-on-password-resets"></a>通知使用者密碼重設

如果此選項設定為 **[是]**，則重設其密碼的使用者會收到一封電子郵件，通知他們密碼已變更。 電子郵件會透過 SSPR 入口網站傳送至儲存在 Azure AD 中的主要和備用電子郵件地址。 沒有人會接獲此重設事件的通知。

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>當其他系統管理員重設其密碼時通知所有系統管理員

如果此選項設定為 **[是]**，則所有其他 Azure 系統管理員都會收到電子郵件，指向儲存在 Azure AD 中的主要電子郵件地址。 此電子郵件會通知他們有其他管理員已使用 SSPR 變更其密碼。

請考慮下列範例案例：

* 環境中有四個系統管理員。
* 系統管理員 *A* 會使用 SSPR 重設其密碼。
* 系統管理員 *B*、 *C* 和 *D* 收到一封電子郵件，警示他們密碼重設。

## <a name="on-premises-integration"></a>內部部署整合

如果您有混合式環境，您可以設定 Azure AD Connect，將密碼變更事件從 Azure AD 回寫到內部部署目錄。

![驗證密碼回寫已啟用且正常運作][Writeback]

Azure AD 會檢查目前的混合式連線能力，並在 Azure 入口網站中提供下列其中一則訊息：

* 您的內部部署回寫用戶端已啟動並正在執行。
* Azure AD 已上線，並已連線至您的內部部署回寫用戶端。 不過，Azure AD Connect 的已安裝版本似乎已過期。 請考慮[升級 Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md)，以確保您具有最新的連線功能及重要錯誤修正。
* 抱歉，因為安裝的 Azure AD Connect 版本已過期，所以我們無法檢查您的內部部署回寫用戶端狀態。 請[升級 Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) 以檢查您的連線狀態。
* 抱歉，我們目前似乎無法連線至您的內部部署回寫用戶端。 請[對 Azure AD Connect 進行疑難排解](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity)以還原連線。
* 抱歉，因為密碼回寫未正確設定，所以我們無法連線至您的內部部署回寫用戶端。 請[設定密碼回寫](./tutorial-enable-sspr-writeback.md)以還原連線。
* 抱歉，我們目前似乎無法連線至您的內部部署回寫用戶端。 這可能是我們這端的暫時性問題所造成。 如果問題持續發生，請[對 Azure AD Connect 進行疑難排解](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity)以還原連線。

若要開始使用 SSPR 回寫，請完成下列教學課程：

> [!div class="nextstepaction"]
> [教學課程：啟用自助式密碼重設 (SSPR) 回寫](./tutorial-enable-sspr-writeback.md)

### <a name="write-back-passwords-to-your-on-premises-directory"></a>將密碼寫回至內部部署目錄

您可以使用 Azure 入口網站來啟用密碼回寫。 您也可以暫時停用密碼回寫，而不需要重新設定 Azure AD Connect。

* 如果選項設定為 **[是]**，則會啟用回寫。 同盟、傳遞驗證或密碼雜湊同步使用者可以重設其密碼。
* 如果選項設定為 [ **否**]，則回寫會停用。 同盟、傳遞驗證或密碼雜湊同步處理的使用者無法重設其密碼。

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>允許使用者在不重設密碼的情況下解除鎖定帳戶

根據預設，Azure AD 可以在執行密碼重設時解除鎖定帳戶。 若要提供彈性，您可以選擇讓使用者解除鎖定其內部部署帳戶，而不需要重設其密碼。 使用此設定來分隔這兩個作業。

* 如果設定為 **[是]**，則會提供使用者重設其密碼並解除鎖定帳戶的選項，或是在不重設密碼的情況下解除鎖定其帳戶的選項。
* 如果設定為 [ **否**]，則使用者只能執行合併的密碼重設和帳戶解除鎖定作業。

### <a name="on-premises-active-directory-password-filters"></a>內部部署 Active Directory 密碼篩選器

SSPR 會在 Active Directory 中執行系統管理員起始的密碼重設等專案。 如果您使用協力廠商密碼篩選器來強制執行自訂密碼規則，而且您需要在 Azure AD 自助式密碼重設期間檢查此密碼篩選器，請確定協力廠商密碼篩選器解決方案已設定為在系統管理員密碼重設案例中套用。 預設支援[Active Directory Domain Services 的 Azure AD 密碼保護](concept-password-ban-bad-on-premises.md)。

## <a name="password-reset-for-b2b-users"></a>B2B 使用者的密碼重設

所有企業對企業 (B2B) 組態完全支援密碼重設和變更。 下列三種案例支援 B2B 使用者密碼重設：

* **來自具有現有 Azure AD 租使用者之合作夥伴組織的使用者**：如果您夥伴的組織有現有的 Azure AD 租使用者，我們會遵循該租使用者上啟用的任何密碼重設原則。 若要讓密碼重設得以運作，合作夥伴組織只需要確定已啟用 Azure AD SSPR。 Microsoft 365 的客戶無須額外付費。
* **透過** 自助式註冊註冊的使用者：如果您合作的組織使用 [自助式註冊](../enterprise-users/directory-self-service-signup.md) 功能進入租使用者，我們會讓他們以註冊的電子郵件重設密碼。
* **B2b 使用者**：使用新的 [Azure AD b2b 功能](../external-identities/what-is-b2b.md) 建立的任何新 B2B 使用者，也可以使用在邀請程式期間註冊的電子郵件重設其密碼。

若要測試此案例，請使用其中一個合作夥伴使用者前往 https://passwordreset.microsoftonline.com。 如果他們有定義備用電子郵件或驗證電子郵件，密碼重設就會如預期般運作。

> [!NOTE]
> 已授與來賓存取您 Azure AD 租使用者的 Microsoft 帳戶（例如 Hotmail.com、Outlook.com 或其他個人電子郵件地址的帳戶）無法使用 Azure AD SSPR。 他們需要使用[當您無法登入您的 Microsoft 帳戶時](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)文章所述的資訊來重設其密碼。

## <a name="next-steps"></a>後續步驟

若要開始使用 SSPR，請完成下列教學課程：

> [!div class="nextstepaction"]
> [教學課程：啟用 (SSPR) 的自助式密碼重設 ](tutorial-enable-sspr.md)

下列文章提供有關透過 Azure AD 重設密碼的其他資訊：

[Authentication]: ./media/concept-sspr-howitworks/manage-authentication-methods-for-password-reset.png "可供使用的 Azure AD 驗證方法和所需的數量"
[Registration]: ./media/concept-sspr-howitworks/configure-registration-options.png "在 Azure 入口網站中設定 SSPR 註冊選項"
[Writeback]: ./media/concept-sspr-howitworks/on-premises-integration.png "Azure 入口網站中適用于 SSPR 的內部部署整合"
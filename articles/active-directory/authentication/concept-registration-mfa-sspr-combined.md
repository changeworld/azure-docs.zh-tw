---
title: SSPR 和 Azure Multi-Factor Authentication Azure Active Directory 的合併註冊
description: 瞭解 Azure Active Directory 的綜合註冊體驗，讓使用者註冊 Azure Multi-Factor Authentication 和自助式密碼重設
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
ms.openlocfilehash: 79a5d306643fa9597b30f6941c420c403695c22e
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92424570"
---
# <a name="combined-security-information-registration-for-azure-active-directory-overview"></a>Azure Active Directory 總覽的結合安全性資訊註冊

在有合併註冊之前，使用者要分開註冊 Azure Multi-Factor Authentication 和自助式密碼重設 (SSPR) 的驗證方法。 人們搞不清楚，Multi-Factor Authentication 和 SSPR 使用類似的方法，但他們必須註冊這兩項功能。 現在，透過結合註冊，使用者可以註冊一次，並獲得 Multi-Factor Authentication 和 SSPR 的優點。

> [!NOTE]
> 自2020年8月15日起，所有新的 Azure AD 租使用者都會自動啟用合併註冊。

本文概述安全性註冊的組合。 若要開始使用合併的安全性註冊，請參閱下列文章：

> [!div class="nextstepaction"]
> [啟用結合的安全性註冊](howto-registration-mfa-sspr-combined.md)

![顯示使用者已註冊之安全性資訊的設定檔](media/concept-registration-mfa-sspr-combined/combined-security-info-defualts-registered.png)

在啟用新的體驗之前，請先查看以系統管理員為主的檔和以使用者為主的檔，以確保您瞭解這項功能的功能和效果。 根據 [使用者檔](../user-help/security-info-setup-signin.md) 的訓練，讓您的使用者能夠獲得新的體驗，並協助確保成功推出。

Azure AD 組合的安全性資訊註冊目前無法供國家雲端使用，例如 Azure 德國或 Azure 中國的世紀。 適用于 Azure 美國政府。

> [!IMPORTANT]
> 針對原始預覽和增強的組合註冊體驗啟用的使用者，會看到新的行為。 同時啟用這兩種體驗的使用者只會看到新的「我的設定檔」體驗。 新的「 *我的個人資料* 」符合合併註冊的外觀和風格，並為使用者提供順暢的體驗。 使用者可以查看我的設定檔，方法是前往 [https://myprofile.microsoft.com](https://myprofile.microsoft.com) 。
>
> 當您嘗試存取安全性資訊選項時，可能會遇到錯誤訊息，例如「抱歉，我們無法將您登入」。 確認您沒有任何設定或群組原則物件會在網頁瀏覽器上封鎖協力廠商 cookie。

*我的設定檔* 頁面會根據存取頁面之電腦的語言設定來當地語系化。 Microsoft 會儲存在瀏覽器快取中使用的最新語言，所以後續存取頁面的嘗試會繼續以最後使用的語言呈現。 如果您清除快取，頁面就會重新呈現。

如果您想要強制執行特定語言，您可以新增 `?lng=<language>` 至 URL 的結尾，其中 `<language>` 是您想要轉譯之語言的程式碼。

![設定 SSPR 或其他安全性驗證方法](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-combined-registration"></a>合併註冊中提供的方法

結合的註冊支援下列驗證方法和動作：

| 方法 | 註冊 | 變更 | 刪除 |
| --- | --- | --- | --- |
| Microsoft 驗證器 | 是 (最多 5)  | 否 | 是 |
| 其他驗證器應用程式 | 是 (最多 5)  | 否 | 是 |
| 硬體權杖 | 否 | 否 | 是 |
| 電話 | 是 | 是 | 是 |
| 備用手機 | 是 | 是 | 是 |
| 辦公室電話 | 否 | 否 | 否 |
| 電子郵件 | 是 | 是 | 是 |
| 安全性問題 | 是 | 否 | 是 |
| 應用程式密碼 | 是 | 否 | 是 |
| FIDO2 安全性金鑰<br />*只有來自 [ [安全性資訊](https://mysignins.microsoft.com/security-info) ] 頁面的 Managed 模式*| 是 | 是 | 是 |

> [!NOTE]
> 應用程式密碼僅適用于已針對 Multi-Factor Authentication 強制執行的使用者。 透過條件式存取原則啟用 Multi-Factor Authentication 的使用者無法使用應用程式密碼。

使用者可以將下列其中一個選項設定為預設的 Multi-Factor Authentication 方法：

- Microsoft Authenticator-通知。
- 驗證器應用程式或硬體權杖–程式碼。
- 撥打電話。
- 簡訊。

當我們繼續將更多驗證方法新增至 Azure AD 時，這些方法會在合併的註冊中提供。

## <a name="combined-registration-modes"></a>合併的註冊模式

結合註冊的模式有兩種：中斷和管理。

- **中斷模式** 是類似于嚮導的體驗，當使用者在登入時註冊或重新整理其安全性資訊時，就會向使用者顯示。
- **管理模式** 是使用者設定檔的一部分，可讓使用者管理其安全性資訊。

在這兩種模式中，先前註冊可用於 Multi-Factor Authentication 之方法的使用者，必須先執行 Multi-Factor Authentication，才能存取其安全性資訊。 使用者必須先確認其資訊，才能繼續使用先前註冊的方法。 

### <a name="interrupt-mode"></a>中斷模式

如果您的租使用者已啟用這兩者，則結合的註冊會同時 Multi-Factor Authentication 和 SSPR 原則。 這些原則可控制使用者是否在登入期間中斷註冊，以及哪些方法可供註冊。

以下是可能會提示使用者註冊或重新整理其安全性資訊的範例案例：

- *透過 Identity Protection 強制執行的註冊 Multi-Factor Authentication：* 系統會要求使用者在登入期間進行註冊。 他們會註冊 Multi-Factor Authentication 方法和 SSPR 方法， (使用者是否已啟用 SSPR) 。
- *透過每個使用者 Multi-Factor Authentication 強制執行的註冊 Multi-Factor Authentication：* 系統會要求使用者在登入期間進行註冊。 他們會註冊 Multi-Factor Authentication 方法和 SSPR 方法， (使用者是否已啟用 SSPR) 。
- *透過條件式存取或其他原則強制執行的註冊 Multi-Factor Authentication：* 當使用者使用需要 Multi-Factor Authentication 的資源時，系統會要求使用者進行註冊。 他們會註冊 Multi-Factor Authentication 方法和 SSPR 方法， (使用者是否已啟用 SSPR) 。
- *SSPR 註冊已強制執行：* 系統會要求使用者在登入期間進行註冊。 他們只會註冊 SSPR 方法。
- 已*強制執行 SSPR*重新整理：使用者必須以系統管理員設定的間隔來檢查其安全性資訊。使用者會看到其資訊，並可確認目前的資訊，或視需要進行變更。

強制註冊時，會顯示使用者最少需要符合 Multi-Factor Authentication 和 SSPR 原則的最少方法數目，從最高到最不安全。

請考慮下列範例案例：

- 使用者已啟用 SSPR。 SSPR 原則需要兩種方法來重設和啟用行動裝置應用程式代碼、電子郵件和電話。
- 此使用者必須註冊兩個方法。
   - 根據預設，使用者會看到驗證器應用程式和電話。
   - 使用者可以選擇註冊電子郵件，而不是驗證器應用程式或電話。

下列流程圖說明當使用者在登入期間中斷註冊時，會向使用者顯示哪些方法：

![結合的安全性資訊流程圖](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

如果您已啟用 Multi-Factor Authentication 和 SSPR，建議您強制執行 Multi-Factor Authentication 註冊。

如果 SSPR 原則要求使用者定期查看其安全性資訊，則使用者會在登入期間中斷，並顯示其所有已註冊的方法。 他們可以確認目前的資訊是否為最新狀態，或者可以在需要時進行變更。 使用者必須在存取此頁面時執行多重要素驗證。

### <a name="manage-mode"></a>管理模式

使用者可以前往 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) 或選取 [我的設定檔] 中的 [ **安全性資訊** ]，以存取 [管理] 模式。 使用者可以從該處新增方法、刪除或變更現有的方法、變更預設方法等等。

## <a name="key-usage-scenarios"></a>主要使用案例

### <a name="set-up-security-info-during-sign-in"></a>在登入期間設定安全性資訊

系統管理員已強制註冊。

使用者尚未設定所有必要的安全性資訊，並移至 Azure 入口網站。 輸入使用者名稱和密碼之後，系統會提示使用者設定安全性資訊。 然後，使用者會遵循嚮導中所示的步驟來設定所需的安全性資訊。 如果您的設定允許，使用者可以選擇設定非預設顯示的方法。 完成嚮導之後，使用者會檢查他們所設定的方法，以及其 Multi-Factor Authentication 的預設方法。 若要完成安裝程式，使用者會確認資訊，並繼續進行 Azure 入口網站。

### <a name="set-up-security-info-from-my-profile"></a>從我的設定檔設定安全性資訊

系統管理員未強制執行註冊。

尚未設定所有必要安全性資訊的使用者會進入 [https://myprofile.microsoft.com](https://myprofile.microsoft.com) 。 使用者會在左窗格中選取 [ **安全性資訊** ]。 使用者可以從該處選擇新增方法、選取任何可用的方法，並遵循設定該方法的步驟。 完成之後，使用者會看到在 [安全性資訊] 頁面上設定的方法。

### <a name="delete-security-info-from-my-profile"></a>從我的設定檔刪除安全性資訊

先前已設定至少一個方法的使用者會流覽至 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) 。 使用者選擇刪除其中一個先前註冊的方法。 完成之後，使用者就不會再于 [安全性資訊] 頁面上看到該方法。

### <a name="change-the-default-method-from-my-profile"></a>從我的設定檔變更預設方法

先前已設定至少一個方法的使用者，可用於 Multi-Factor Authentication 流覽至 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) 。 使用者將目前的預設方法變更為不同的預設方法。 完成時，使用者會在 [安全性資訊] 頁面上看到新的預設方法。

## <a name="next-steps"></a>後續步驟

若要開始使用，請參閱教學課程以 [啟用自助式密碼重設](tutorial-enable-sspr.md) 和 [啟用 Azure Multi-Factor Authentication](tutorial-enable-azure-mfa.md)。

瞭解如何 [在您的租使用者中啟用合併註冊](howto-registration-mfa-sspr-combined.md) ，或 [強制使用者重新註冊驗證方法](howto-mfa-userdevicesettings.md#manage-user-authentication-options)。

您也可以檢閱 [Azure Multi-Factor Authentication 與 SSPR 的可用方法](concept-authentication-methods.md)。
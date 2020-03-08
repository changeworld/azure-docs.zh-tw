---
title: 設定 Azure 多重要素驗證-Azure Active Directory
description: 本文將說明如何在 Azure 入口網站中設定 Azure Multi-Factor Authentication 設定
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 077032e4fe3886d5bf9a678dffdffca1a5802091
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78377504"
---
# <a name="configure-azure-multi-factor-authentication-settings"></a>設定 Azure Multi-Factor Authentication 設定

本文可協助您在 Azure 入口網站中管理 Multi-Factor Authentication 設定。 本文涵蓋各種主題，協助您充分發揮 Azure Multi-Factor Authentication 的功效。 並非所有功能都適用于每個 Azure 多重要素驗證版本。

您可以流覽至**Azure Active Directory** > 的**安全性** > **MFA**，以從 Azure 入口網站存取 Azure 多重要素驗證的相關設定。

![Azure 入口網站 - Azure AD Multi-Factor Authentication 設定](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-portal.png)

## <a name="settings"></a>設定

其中某些設定適用於 MFA 伺服器、Azure MFA 或兩者。

| 功能 | 描述 |
| ------- | ----------- |
| 帳戶鎖定 | 如果連續太多次驗證嘗試都遭到拒絕，就會在 Multi-Factor Authentication 服務中暫時鎖定帳戶。 這項功能僅適用於輸入 PIN 以進行驗證的使用者。 (MFA 伺服器) |
| [封鎖/解除封鎖使用者](#block-and-unblock-users) | 用來封鎖特定使用者無法接收多重要素驗證要求。 系統會自動拒絕已封鎖使用者的任何驗證嘗試。 使用者自封鎖的時刻起，將持續封鎖 90 天。 |
| [詐騙警示](#fraud-alert) | 設定使用者報告詐騙驗證要求的能力相關設定 |
| [通知](#notifications) | 啟用 MFA 伺服器中事件的通知。 |
| [OATH 權杖](concept-authentication-methods.md#oath-hardware-tokens-public-preview) | 用於雲端式 Azure MFA 環境，以管理使用者的 OATH 權杖。 |
| [通話設定](#phone-call-settings) | 設定雲端和內部部署環境之通話和問候語相關的設定。 |
| 提供者 | 這會顯示任何可能已與您帳戶建立關聯的現有驗證提供者。 自 2018 年 9 月 1 日起，可能無法建立新的驗證提供者 |

## <a name="manage-mfa-server"></a>管理 MFA 伺服器

本節中的設定僅適用於 MFA 伺服器。

| 功能 | 描述 |
| ------- | ----------- |
| 伺服器設定 | 下載 MFA 伺服器並產生啟用認證，以初始化您的環境 |
| [一次性略過](#one-time-bypass) | 允許使用者在有限時間內不需要執行雙步驟驗證即可驗證。 |
| [快取規則](#caching-rules) |  快取主要是在內部部署系統 (例如 VPN) 於第一個要求仍在進行中的同時，傳送多個驗證要求時使用。 此功能可讓後續要求在使用者成功完成進行中的第一次驗證後自動成功。 |
| 伺服器狀態 | 查看您內部部署 MFA 伺服器的狀態，包括版本、狀態、IP 和最後的通訊時間與日期。 |

## <a name="activity-report"></a>活動報表

此處可用的報表特定於 MFA 伺服器 (內部部署)。 針對 Azure MFA (雲端) 的報表，請參閱 Azure AD 中的登入報表。

## <a name="block-and-unblock-users"></a>封鎖及解除封鎖使用者

使用_封鎖及解除封鎖使用者_功能來防止使用者接收驗證要求。 系統會自動拒絕已封鎖使用者的任何驗證嘗試。 使用者自封鎖的時刻起，將持續封鎖 90 天。

### <a name="block-a-user"></a>封鎖使用者

1. 以系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
2. 流覽至**Azure Active Directory** > **安全性** > **MFA** > **封鎖/解除封鎖使用者**。
3. 選取 [新增] 以封鎖使用者。
4. 選取 [複寫群組]。 將 [封鎖的使用者名稱] 輸入為**username\@domain.com**。 在 [原因] 欄位中輸入註解。
5. 選取 [新增] 以完成封鎖使用者。

### <a name="unblock-a-user"></a>解除封鎖使用者

1. 以系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
2. 流覽至**Azure Active Directory** > **安全性** > **MFA** > **封鎖/解除封鎖使用者**。
3. 在要解除封鎖之使用者旁的 [動作] 資料行中，選取 [解除封鎖]。
4. 在 [解除封鎖的原因] 欄位中輸入註解。
5. 選取 [解除封鎖] 以完成解除封鎖使用者。

## <a name="fraud-alert"></a>詐騙警示

設定_詐騙警示_功能，讓使用者得以針對存取其資源的詐騙嘗試提出報告。 使用者可以使用行動應用程式或透過電話來報告詐騙嘗試。

### <a name="turn-on-fraud-alerts"></a>開啟詐騙警示

1. 以系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
2. 流覽至**Azure Active Directory** > **安全性** > **MFA** > **詐騙警示**。
3. 將 [允許使用者提交詐騙警示] 設定設為 [開啟]。
4. 選取 [儲存]。

### <a name="configuration-options"></a>設定選項

* **回報詐騙時封鎖使用者**：如果使用者回報詐騙，他們的帳戶會遭封鎖 90 天，或是封鎖到系統管理員解除封鎖其帳戶為止。 系統管理員可以使用登入報告來檢閱登入，並採取適當的動作以避免未來的詐騙。 系統管理員接著可以[解除封鎖](#unblock-a-user)使用者的帳戶。
* **初始問候語期間以代碼報告詐騙**：當使用者收到電話以執行雙步驟驗證時，他們一般會按下 **#** 來確認登入。 若要報告詐騙，則需要在按 **#** 之前輸入代碼。 根據預設，此代碼是 **0**，但您可以自訂。

   >[!NOTE]
   >Microsoft 的預設語音問候語會指示使用者按 **0#** 以提交詐騙警示。 如果您要使用 **0** 以外的代碼，則應該記錄下來，並上傳您自己的自訂語音問候語和適當的指示。
   >

### <a name="view-fraud-reports"></a>檢視詐騙報告

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取**Azure Active Directory** > 登**入**。詐騙報告現在是標準 Azure AD 登入報告的一部分。

## <a name="notifications"></a>通知

在此為將接收詐騙警示電子郵件的使用者設定電子郵件地址。

![通知詐騙警示電子郵件範例](./media/howto-mfa-mfasettings/multi-factor-authentication-fraud-alert-email.png)

## <a name="phone-call-settings"></a>通話設定

### <a name="caller-id"></a>呼叫者識別碼

**MFA 呼叫者識別碼** - 這是使用者會在電話上看到的數字。 僅允許使用美國的號碼。

>[!NOTE]
>當透過公共電話網路發出 Multi-Factor Authentication 通話時，有時會透過不支援來電顯示的電信業者路由傳送。 因此，即使 Multi-Factor Authentication 系統一律會傳送來電顯示號碼，但並不保證一定會顯示來電號碼。

在美國，如果您尚未設定 MFA 呼叫者識別碼，來自 Microsoft 的語音電話會來自下列數位： + 1 （866） 539 4191、+ 1 （855） 330 8653 和 + 1 （877） 668 6536。 如果使用垃圾郵件篩選器，請務必排除這些數位。

### <a name="custom-voice-messages"></a>自訂語音訊息

您可以透過_自訂語音訊息_功能，將自己的錄音或問候語用於雙步驟驗證。 您可以在 Microsoft 錄音之外額外使用這些訊息，也可以取代 Microsoft 的錄音。

開始之前，請留意下列限制：

* 支援的檔案格式為 .wav 和 .mp3。
* 檔案大小限制為 1 MB。
* 驗證訊息應該少於 20 秒。 超過 20 秒的訊息可能會導致驗證失敗。 在訊息結束且驗證逾時之前，使用者可能無法回應。

### <a name="custom-message-language-behavior"></a>自訂訊息語言行為

對使用者播放自訂語音訊息時，訊息的語言取決於下列因素：

* 目前使用者的語言。
  * 使用者的瀏覽器偵測到的語言。
  * 其他驗證案例可能會有不同的行為。
* 任何可用自訂訊息的語言。
  * 新增自訂訊息時，系統管理員會選擇此語言。

例如，如果只有一則使用德語的自訂訊息：

* 以德語進行驗證的使用者會聽到自訂德文訊息。
* 以英文進行驗證的使用者會聽到標準英文訊息。

### <a name="set-up-a-custom-message"></a>設定自訂訊息

1. 以系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
1. 流覽至**Azure Active Directory** > **安全性** > **MFA** > **撥打電話設定**。
1. 選取 [新增問候語]。
1. 選擇問候語的類型。
1. 選擇語言。
1. 選取要上傳的 .mp3 或 .wav 音效檔。
1. 選取 [新增]。

### <a name="custom-voice-message-defaults"></a>自訂語音訊息預設值

用來建立自訂訊息的範例腳本。

| 訊息名稱 | 指令檔 |
| --- | --- |
| 驗證成功 | 已成功驗證您的登入。 不用. |
| 延伸模組提示 | 感謝您使用 Microsoft 的登入驗證系統。 請按井字鍵以繼續。 |
| 詐騙確認 | 已提交詐騙警示。 若要解除鎖定您的帳戶，請洽詢您公司的 IT 支援人員。 |
| 詐騙問候語（標準） | 感謝您使用 Microsoft 的登入驗證系統。 請按井字鍵完成您的驗證。 如果您未起始此驗證，可能是有人嘗試存取您的帳戶。 請按零英鎊以提交詐騙警示。 這會通知您公司的 IT 小組，並封鎖進一步的驗證嘗試。 |
| 詐騙回報已提交詐騙警示。 | 若要解除鎖定您的帳戶，請洽詢您公司的 IT 支援人員。 |
| 啟用 | 感謝您使用 Microsoft 的登入驗證系統。 請按井字鍵完成您的驗證。 |
| 拒絕驗證重試 | 拒絕驗證。 |
| 重試（標準） | 感謝您使用 Microsoft 的登入驗證系統。 請按井字鍵完成您的驗證。 |
| 問候（標準） | 感謝您使用 Microsoft 的登入驗證系統。 請按井字鍵完成您的驗證。 |
| 問候（PIN） | 感謝您使用 Microsoft 的登入驗證系統。 請輸入您的 PIN，然後按井字鍵完成您的驗證。 |
| 詐騙問候語（PIN） | 感謝您使用 Microsoft 的登入驗證系統。  請輸入您的 PIN，然後按井字鍵完成您的驗證。 如果您未起始此驗證，可能是有人嘗試存取您的帳戶。 請按零英鎊以提交詐騙警示。 這會通知您公司的 IT 小組，並封鎖進一步的驗證嘗試。 |
| 重試（PIN） | 感謝您使用 Microsoft 的登入驗證系統。 請輸入您的 PIN，然後按井字鍵完成您的驗證。 |
| 數位之後的延伸提示 | 如果已在此延伸模組中，請按井字鍵以繼續。 |
| 拒絕驗證 | 很抱歉，我們目前無法將您登入。 請稍後再試。 |
| 啟用問候語（標準） | 感謝您使用 Microsoft 的登入驗證系統。 請按井字鍵完成您的驗證。 |
| 啟用重試（標準） | 感謝您使用 Microsoft 的登入驗證系統。 請按井字鍵完成您的驗證。 |
| 啟用問候語（PIN） | 感謝您使用 Microsoft 的登入驗證系統。 請輸入您的 PIN，然後按井字鍵完成您的驗證。 |
| 數位前的延伸提示 | 感謝您使用 Microsoft 的登入驗證系統。 請將此來電轉接至延伸模組 。 |

## <a name="one-time-bypass"></a>一次性略過

_單次許可_可讓使用者只驗證一次，不需要執行雙步驟驗證。 許可只是暫時性，經過指定的秒數之後就會到期。 在行動裝置應用程式或電話沒有收到通知或來電的情況下，您可以啟用單次許可，讓使用者能夠存取所需的資源。

### <a name="create-a-one-time-bypass"></a>建立單次許可

1. 以系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
2. 流覽至**Azure Active Directory** > **安全性** > **MFA** > **一次性略過**。
3. 選取 [新增]。
4. 如有必要，請選取此許可的複寫群組。
5. 在 [使用者**名稱]\@domain.com**中輸入使用者名稱。 輸入許可需持續的秒數。 輸入許可的原因。
6. 選取 [新增]。 時間限制會立即生效。 使用者必須在單次許可到期之前登入。

### <a name="view-the-one-time-bypass-report"></a>檢視單次許可報告

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 流覽至**Azure Active Directory** > **安全性** > **MFA** > **一次性略過**。

## <a name="caching-rules"></a>快取規則

您可以使用_快取_功能，來設定使用者已驗證之後，允許驗證嘗試的時段。 使用者在指定期間內的後續驗證嘗試會成功自動。 快取主要是在內部部署系統 (例如 VPN) 於第一個要求仍在進行中的同時，傳送多個驗證要求時使用。 此功能可讓後續要求在使用者成功完成進行中的第一次驗證後自動成功。

>[!NOTE]
>快取功能的目的不是要用於登入 Azure Active Directory (Azure AD)。

### <a name="set-up-caching"></a>設定快取

1. 以系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
2. 流覽至**Azure Active Directory** > **安全性** > **MFA** > 快取**規則**。
3. 選取 [新增]。
4. 從下拉式清單中選取 [快取類型]。 輸入**快取秒數**的最大數目。
5. 如有必要，選取驗證類型並指定應用程式。
6. 選取 [新增]。

## <a name="mfa-service-settings"></a>MFA 服務設定

Azure Multi-Factor Authentication 的應用程式密碼、受信任的 IP、驗證選項和成員多重要素驗證等設定，位於服務設定中。 藉由流覽至**Azure Active Directory** > **安全性** > **MFA** ** > 開始使用 > ** **設定** > **其他雲端式 MFA 設定**，即可從 Azure 入口網站存取服務設定。

![Azure Multi-Factor Authentication 服務設定](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-service-settings.png)

信任的 IP 位址範圍可以是私用或公用。

## <a name="app-passwords"></a>應用程式密碼

某些應用程式 (例如 Office 2010 或更舊版本和 iOS 11 之前的 Apple Mail) 不支援雙步驟驗證。 應用程式未設定為接受第二次驗證。 若要使用這些應用程式，請善用_應用程式密碼_功能。 您可以使用應用程式密碼取代您的傳統密碼，以允許應用程式略過雙步驟驗證並繼續工作。

Microsoft Office 2013 用戶端和更新版本支援新式驗證。 Office 2013 用戶端 (包括 Outlook) 支援新式驗證通訊協定，而且可搭配雙步驟驗證運作。 啟用用戶端之後，用戶端並不需要應用程式密碼。

>[!NOTE]
>應用程式密碼不適用於以條件式存取為基礎的多重要素驗證原則和新式驗證。

### <a name="considerations-about-app-passwords"></a>關於應用程式密碼的考量

使用應用程式密碼時，請考量下列要點：

* 每個應用程式只能輸入一次應用程式密碼。 使用者不需要追蹤密碼，也不需要每次輸入。
* 實際使用的密碼是自動產生的，而不是由使用者提供。 攻擊者比較難以猜中自動產生的密碼，所以比較安全。
* 每位使用者的密碼以 40 組為限。
* 因為公司或學校帳戶以外的應用程式密碼是未知的，快取密碼並在內部部署案例中使用這些密碼的應用程式可能會開始失敗。 將 Exchange 電子郵件存放在內部部署設施，但是將封存郵件存放在雲端即是此案例的一個範例。 在此案例中，同樣的密碼無法適用於兩者。
* 在使用者的帳戶上啟用 Multi-Factor Authentication 之後，應用程式密碼就能用於大部分的非瀏覽器用戶端 (例如 Outlook 和 Microsoft 商務用 Skype)。 透過非瀏覽器的應用程式 (例如 Windows PowerShell) 使用應用程式密碼無法執行系統管理動作。 即使使用者具有系統管理員帳戶，仍無法執行動作。 若要執行 PowerShell 指令碼，請使用強式密碼建立服務帳戶，且請勿為該帳戶啟用雙步驟驗證。

>[!WARNING]
>應用程式密碼無法在用戶端會同時與內部部署及雲端自動探索端點通訊的混合環境作用。 需要網域密碼才能驗證內部部署。 需要應用程式密碼才能向雲端驗證。

### <a name="guidance-for-app-password-names"></a>應用程式密碼名稱的指引

應用程式密碼名稱應該反映出它們用在哪個裝置。 如果膝上型電腦有 Outlook、Word 及 Excel 之類的非瀏覽器應用程式，請針對這些應用程式建立一個名為 **Laptop** 的應用程式密碼。 接著，為在桌上型電腦上執行的同樣應用程式建立另一個應用程式密碼，名為 **Desktop**。

>[!NOTE]
>建議您為每個裝置建立一個應用程式密碼，而不是為每個應用程式建立一個應用程式密碼。

### <a name="federated-or-single-sign-on-app-passwords"></a>同盟或單一登入應用程式密碼

Azure AD 使用內部部署 Windows Server Active Directory Domain Services (AD DS) 支援同盟或單一登入 (SSO)。 如果貴組織與 Azure AD 同盟，且您要使用 Azure Multi-Factor Authentication，請考量以下有關應用程式密碼的資訊。

>[!NOTE]
>下列各點僅適用於同盟 (SSO) 客戶。

* 應用程式密碼由 Azure AD 驗證，因此會略過同盟。 唯有在設定應用程式密碼時才會主動使用同盟。
* 對於同盟 (SSO) 使用者，不會像被動流程一樣連絡識別提供者 (IdP)。 應用程式密碼會儲存在公司或學校帳戶中。 如果使用者離開公司，使用者的資訊就會即時使用 **DirSync** 流向公司或學校帳戶。 停用/刪除帳戶最多可能需要三小時的時間來同步處理，而這可能會使在 Azure AD 中停用/刪除應用程式密碼有所延遲。
* 應用程式密碼功能不會遵守內部部署用戶端存取控制設定。
* 應用程式密碼功能不適用與內部部署驗證記錄/稽核功能搭配使用。
* 某些進階的架構需要向用戶端進行雙步驟驗證的認證組合。 這些認證可能包含公司或學校帳戶使用者名稱和密碼，以及應用程式密碼。 這些需求取決於執行驗證的方式。 對於根據內部部署基礎結構進行驗證的用戶端，需要公司或學校帳戶的使用者名稱和密碼。 對於根據 Azure AD 進行驗證的用戶端，需要應用程式密碼。

  例如，假設您有下列架構：

  * 您的內部部署 Active Directory 執行個體會與 Azure AD 同盟。
  * 您正在使用 Exchange Online。
  * 您正在使用內部部署商務用 Skype。
  * 您正在使用 Azure Multi-Factor Authentication。

  在此案例中，您要使用下列認證：

  * 若要登入商務用 Skype，請使用您公司或學校帳戶的使用者名稱和密碼。
  * 若要從連線至 Exchange Online 的 Outlook 用戶端存取通訊錄，請使用應用程式密碼。

### <a name="allow-users-to-create-app-passwords"></a>允許使用者建立應用程式密碼

根據預設，使用者無法建立應用程式密碼。 必須啟用應用程式密碼功能。 若要讓使用者能夠建立應用程式密碼，請使用下列程序：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取左邊的 [Azure Active Directory] > [使用者]。
3. 選取 [多重要素驗證]。
4. 在 [Multi-Factor Authentication] 下，選取 [服務設定]。
5. 在 [服務設定] 頁面上，選取 [允許使用者建立應用程式密碼以登入非瀏覽器應用程式] 選項。

### <a name="create-app-passwords"></a>建立應用程式密碼

使用者可以在一開始的註冊期間建立應用程式密碼。 使用者可選擇在註冊程序結束時建立應用程式密碼。

使用者也可以在註冊後建立應用程式密碼。 如需使用者的詳細資訊和詳細步驟，請參閱[什麼是 Azure Multi-Factor Authentication 中的應用程式密碼？](../user-help/multi-factor-authentication-end-user-app-passwords.md)

## <a name="trusted-ips"></a>信任的 IP

受控或同盟租用戶的系統管理員會使用 Azure Multi-Factor Authentication 的_信任 IP_ 功能。 此功能會略過從公司內部網路登入之使用者的雙步驟驗證。 完整版 Azure Multi-Factor Authentication 提供此功能給管理員使用，免費版則不提供。 如需有關如何取得完整版 Azure Multi-Factor Authentication 的詳細資訊，請參閱[ Azure Multi-Factor Authentication](multi-factor-authentication.md)。

> [!NOTE]
> MFA 信任的 Ip 和名為「位置」的條件式存取僅適用于 IPV4 位址。

如果您的組織部署 NPS 延伸模組以提供 MFA 給內部部署應用程式，請注意來源 IP 位址一律會顯示為驗證嘗試流經的 NPS 伺服器。

| Azure AD 租用戶類型 | 信任的 IP 功能選項 |
|:--- |:--- |
| Managed |**特定 IP 位址的範圍**：對於從公司內部網路登入的使用者，管理員會指定允許略過雙步驟驗證的 IP 位址範圍。 最多可設定50個信任的 IP 範圍。|
| 同盟 |**所有同盟使用者**：所有從組織內登入的同盟使用者，都可略過雙步驟驗證。 使用者可使用 Active Directory Federation Services (AD FS) 所發出的宣告來略過驗證。<br/>**特定 IP 位址的範圍**：對於從公司內部網路登入的使用者，管理員會指定允許略過雙步驟驗證的 IP 位址範圍。 |

信任的 IP 只會略過公司內部網路之內的工作。 如果您選取 [所有同盟使用者] 選項，且使用者從公司內部網路之外登入時，使用者必須使用雙步驟驗證來進行驗證。 即使使用者提供 AD FS 宣告，程序還是相同的。 

### <a name="end-user-experience-inside-of-corpnet"></a>公司網路內部的終端使用者體驗

當「信任的 IP」停用時，瀏覽器流程就需要雙步驟驗證。 較舊的豐富型用戶端應用程式需要應用程式密碼。

當「信任的 IP」啟用時，瀏覽器流程就不需要雙步驟驗證。 較舊的豐富型用戶端應用程式「不」需要應用程式密碼，但前提是使用者尚未建立應用程式密碼。 應用程式密碼使用後，以後就都需要密碼。 

### <a name="end-user-experience-outside-corpnet"></a>公司網路外部的終端使用者體驗

不論是否啟用「信任的 IP」，瀏覽器流程都需要雙步驟驗證。 較舊的豐富型用戶端應用程式需要應用程式密碼。

### <a name="enable-named-locations-by-using-conditional-access"></a>使用條件式存取啟用命名位置

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在左側，選取 [ **Azure Active Directory** > **安全性**] > [**條件式存取**] > **名為 [位置**]。
3. 選取 [新增位置]。
4. 輸入位置的名稱。
5. 選取 [標記為受信任的位置]。
6. 輸入 CIDR 表示法中的 IP 範圍，例如 **192.168.1.1/24**。
7. 選取 [建立]。

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>使用條件式存取啟用信任的 Ip 功能

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在左側，選取 [ **Azure Active Directory** > **安全性**] >  [**條件式存取**] > **名為 [位置**]。
3. 選取 [設定 MFA 信任的 IP]。
4. 在 [服務設定] 頁面的 [信任的 IP] 下方，選擇下列兩個選項中的任一項：

   * **適用於從我的內部網路產生的同盟使用者提出的要求**：若要選擇此選項，請選取核取方塊。 所有從公司網路登入的同盟使用者都會使用 AD FS 所發行的宣告來略過雙步驟驗證。 請確定 AD FS 有規則可用於將內部網路宣告新增至適當的流量。 如果規則不存在，請在 AD FS 中建立下列規則：

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **適用於從特定公用 IP 範圍提出的要求**：若要選擇此選項，請在文字方塊中，使用 CIDR 表示法輸入 IP 位址。
      * 對於範圍在 xxx.xxx.xxx.1 到 xxx.xxx.xxx.254 的 IP 位址，使用如 **xxx.xxx.xxx.0/24** 的標記法。
      * 針對單一 IP 位址，使用如 **xxx.xxx.xxx.xxx/32** 標記法。
      * 最多輸入 50 個 IP 位址範圍。 從這些 IP 位址登入的使用者會略過雙步驟驗證。

5. 選取 [儲存]。

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>使用服務設定啟用信任的 IP 功能

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取左邊的 [Azure Active Directory] > [使用者]。
3. 選取 [多重要素驗證]。
4. 在 [Multi-Factor Authentication] 下，選取 [服務設定]。
5. 在 [服務設定] 頁面的 [信任的 IP] 下方，選擇下列兩個選項中的一或兩項：

   * **適用於從我的內部網路上的同盟使用者提出的要求**：若要選擇此選項，請選取核取方塊。 所有從公司網路登入的同盟使用者都會使用 AD FS 所發行的宣告來略過雙步驟驗證。 請確定 AD FS 有規則可用於將內部網路宣告新增至適當的流量。 如果規則不存在，請在 AD FS 中建立下列規則：

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **適用於從特定範圍之 IP 位址子網路提出的要求**：若要選擇此選項，請在文字方塊中，使用 CIDR 表示法輸入 IP 位址。
      * 對於範圍在 xxx.xxx.xxx.1 到 xxx.xxx.xxx.254 的 IP 位址，使用如 **xxx.xxx.xxx.0/24** 的標記法。
      * 針對單一 IP 位址，使用如 **xxx.xxx.xxx.xxx/32** 標記法。
      * 最多輸入 50 個 IP 位址範圍。 從這些 IP 位址登入的使用者會略過雙步驟驗證。

6. 選取 [儲存]。

## <a name="verification-methods"></a>驗證方法

您可以選擇可供使用者運用的驗證方法。 下表提供各種方法的簡短概觀。

使用者將其帳戶註冊進行 Azure Multi-Factor Authentication 時，會在您已啟用的選項中選擇慣用驗證方法。 [對我的帳戶進行雙步驟驗證設定](../user-help/multi-factor-authentication-end-user-first-time.md)中會提供使用者註冊程序的指引。

| 方法 | 描述 |
|:--- |:--- |
| 電話通話 |撥打自動語音電話。 使用者可接聽電話並按電話鍵盤上的 # 進行驗證。 此電話號碼不會同步到內部部署 Active Directory。 |
| 電話簡訊 |傳送包含驗證碼的簡訊。 系統會提示使用者在登入介面中輸入這個驗證碼。 此程序稱為「單向 SMS」。 雙向 SMS 表示使用者必須以簡訊回傳特定驗證碼。 雙向 SMS 已淘汰，且在 2018 年 11 月 14 之後不支援。 系統管理員應該為先前使用雙向 SMS 的使用者啟用另一種方法。|
| 行動應用程式的通知 |將推播通知傳送至您的電話或已註冊的裝置。 使用者會看到通知，然後選取 [驗證] 來完成驗證。 Microsoft Authenticator 應用程式適用於 [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6)、[Android](https://go.microsoft.com/fwlink/?Linkid=825072) 和 [iOS](https://go.microsoft.com/fwlink/?Linkid=825073)。 |
| 來自行動應用程式或硬體 Token 的驗證碼 |Microsoft Authenticator 應用程式每隔 30 秒會產生新的 OATH 驗證碼。 使用者會在登入介面中輸入此驗證碼。 Microsoft Authenticator 應用程式適用於 [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6)、[Android](https://go.microsoft.com/fwlink/?Linkid=825072) 和 [iOS](https://go.microsoft.com/fwlink/?Linkid=825073)。 |

### <a name="enable-and-disable-verification-methods"></a>啟用和停用驗證方法

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取左邊的 [Azure Active Directory] > [使用者]。
3. 選取 [多重要素驗證]。
4. 在 [Multi-Factor Authentication] 下，選取 [服務設定]。
5. 在 [服務設定] 頁面的 [驗證選項] 下，選取/取消選取要提供給使用者的方法。
6. Haga clic en **Guardar**.

其他的驗證方法使用方式詳細資料，可在[驗證的方法](concept-authentication-methods.md)一文中找到。

## <a name="remember-multi-factor-authentication"></a>記住 Multi-Factor Authentication

使用者信任的裝置和瀏覽器的_記住 Multi-Factor Authentication_ 功能是可供所有 Multi-factor Authentication 使用者免費使用的功能。 使用者在使用 Multi-Factor Authentication 成功登入裝置後，可以在指定天數內略過後續驗證。 這項功能會減少使用者必須在相同裝置上執行雙步驟驗證的次數，以提高使用性。

>[!IMPORTANT]
>若帳戶或裝置遭到入侵，請記住受信任裝置的 Multi-Factor Authentication 可能會影響安全性。 如果公司帳戶遭到入侵，或受信任的裝置遺失或遭竊，您應該[撤銷 MFA 會話](howto-mfa-userdevicesettings.md)。
>
>此還原動作會從所有裝置撤銷受信任的狀態，使用者必須再次執行雙步驟驗證。 您也可以使用[管理雙步驟驗證的設定](../user-help/multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device)中的指示，指示使用者在自己的裝置上還原 Multi-Factor Authentication。

### <a name="how-the-feature-works"></a>此功能的運作方式

當使用者在登入時選取 [X 天內不要再問我] 選項時，記住 Multi-Factor Authentication 功能就會設定瀏覽器上的永續性 cookie。 該相同的瀏覽器不會再次提示使用者進行 Multi-Factor Authentication，直到 cookie 到期為止。 如果使用者在相同的裝置上開啟不同的瀏覽器或清除其 cookie，系統會提示他們再次確認。

非瀏覽器應用程式上不會顯示 [X 天內不要再問我] 選項，不論應用程式是否支援新式驗證。 這些應用程式使用每隔一小時會提供新存取權杖的_重新整理權杖_。 當已驗證重新整理權杖時，Azure AD 會檢查在指定天數內發生的最後一次雙步驟驗證。

此功能會減少 web 應用程式上的驗證次數，一般每次皆會提示。 此功能會增加新式驗證用戶端的驗證次數 (一般會每隔 90 天提示一次)。 也可能會在結合條件式存取原則時增加驗證的數目。

>[!IMPORTANT]
>當使用者透過 Azure Multi-Factor Authentication Server 或第三方多因素驗證解決方案執行雙步驟驗證時，[記住 Multi-Factor Authentication] 功能與 AD FS 的 [讓我保持登入] 功能並不相容。
>
>如果您的使用者選取 AD FS 上的 [讓我保持登入]，且將他們的裝置標記為受 Multi-Factor Authentication 信任，在 [記住多重要素驗證] 天數到期之後，並不會自動驗證使用者。 Azure AD 要求重新整理雙步驟驗證，但是 AD FS 會傳回包含原始 Multi-Factor Authentication 宣告及日期的權杖，而不是再次執行雙步驟驗證。 **這個反應會啟動 Azure AD 與 AD FS 之間的驗證迴圈。**
>
>[**記住多重要素驗證**] 功能與 b2b 使用者不相容，且在登入受邀的租使用者時不會對其顯示。
>

### <a name="enable-remember-multi-factor-authentication"></a>啟用記住 Multi-Factor Authentication

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取左邊的 [Azure Active Directory] > [使用者]。
3. 選取 [多重要素驗證]。
4. 在 [Multi-Factor Authentication] 下，選取 [服務設定]。
5. 在 [服務設定] 頁面、[管理記住多重要素驗證]，選取 [允許使用者在其信任的裝置記住多重要素驗證] 選項。
6. 設定要允許受信任裝置略過雙步驟驗證的天數。 預設值為 14 天。
7. 選取 [儲存]。

### <a name="mark-a-device-as-trusted"></a>將裝置標示為受信任

啟用 [記住 Multi-Factor Authentication] 功能之後，使用者可選取 [不要再詢問]，以在他們登入時將裝置標記為信任。

## <a name="next-steps"></a>後續步驟

[修改 Azure AD 登入頁面商標](../fundamentals/customize-branding.md)

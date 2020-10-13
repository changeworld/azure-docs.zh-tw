---
title: 設定 Azure Multi-Factor Authentication - Azure Active Directory
description: 瞭解如何在 Azure 入口網站中設定 Azure Multi-Factor Authentication 的設定
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: 17fbba605f6f20fa384d59a8c89ee536a9b121bf
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91964430"
---
# <a name="configure-azure-multi-factor-authentication-settings"></a>設定 Azure Multi-Factor Authentication 設定

若要自訂 Azure Multi-Factor Authentication 的終端使用者體驗，您可以設定帳戶鎖定閾值或詐騙警示和通知等設定的選項。 某些設定會直接在 Azure 入口網站中，Azure Active Directory (Azure AD) ，有些設定則是在不同的 Azure Multi-Factor Authentication 入口網站中。

下列 Azure Multi-Factor Authentication 設定可在 Azure 入口網站中取得：

| 特徵 | 描述 |
| ------- | ----------- |
| [帳戶鎖定](#account-lockout) | 如果資料列中有太多拒絕的驗證嘗試，則暫時鎖定帳戶以使用 Azure Multi-Factor Authentication。 這項功能僅適用於輸入 PIN 以進行驗證的使用者。 (MFA 伺服器) |
| [封鎖/解除封鎖使用者](#block-and-unblock-users) | 封鎖特定使用者無法接收 Azure Multi-Factor Authentication 要求。 系統會自動拒絕已封鎖使用者的任何驗證嘗試。 使用者在封鎖或手動解除封鎖時，會持續封鎖90天。 |
| [詐騙警示](#fraud-alert) | 設定可讓使用者報告詐騙驗證要求的設定。 |
| [通知](#notifications) | 啟用 MFA 伺服器中事件的通知。 |
| [OATH 權杖](concept-authentication-oath-tokens.md) | 用於雲端式 Azure MFA 環境，以管理使用者的 OATH 權杖。 |
| [通話設定](#phone-call-settings) | 設定雲端和內部部署環境之通話和問候語相關的設定。 |
| 提供者 | 這會顯示任何可能已與您帳戶建立關聯的現有驗證提供者。 自 2018 年 9 月 1 日起，可能無法建立新的驗證提供者 |

![Azure 入口網站 - Azure AD Multi-Factor Authentication 設定](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-portal.png)

## <a name="account-lockout"></a>帳戶鎖定

為了避免在攻擊中重複進行 MFA 嘗試，帳戶鎖定設定可讓您指定帳戶在一段時間內遭到鎖定之前，允許的失敗嘗試次數。 只有在為 MFA 提示輸入 pin 碼時，才會套用帳戶鎖定設定。

可用的設定如下：

* 用來觸發帳戶鎖定的 MFA 拒絕數目
* 重設帳戶鎖定計數器之前的分鐘數
* 帳戶自動解除封鎖之前的分鐘數

若要設定帳戶鎖定設定，請完成下列設定：

1. 以系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
1. 流覽至**Azure Active Directory**  >  **安全性**  >  **MFA**  >  **帳戶鎖定**。
1. 輸入您的環境所需的值，然後選取 [ **儲存**]。

    ![Azure 入口網站中帳戶鎖定設定的螢幕擷取畫面](./media/howto-mfa-mfasettings/account-lockout-settings.png)

## <a name="block-and-unblock-users"></a>封鎖及解除封鎖使用者

如果使用者的裝置遺失或遭竊，您可以封鎖相關聯帳戶的 Azure Multi-Factor Authentication 嘗試。 系統會自動拒絕已封鎖使用者的任何 Azure Multi-Factor Authentication 嘗試。 使用者自封鎖的時刻起，將持續封鎖 90 天。

### <a name="block-a-user"></a>封鎖使用者

若要封鎖使用者，請完成下列步驟：

1. 瀏覽至 [Azure Active Directory] > [安全性] > [MFA] > [封鎖/解除封鎖使用者]。
1. 選取 [新增] 以封鎖使用者。
1. 選取複寫 **群組**，然後選擇 [ *Azure 預設值*]。

    輸入封鎖使用者的使用者名稱 `username\@domain.com` ，然後在 [ *原因* ] 欄位中提供批註。
1. 準備好時，請選取 **[確定]** 來封鎖使用者。

### <a name="unblock-a-user"></a>解除封鎖使用者

若要解除封鎖使用者，請完成下列步驟：

1. 瀏覽至 [Azure Active Directory] > [安全性] > [MFA] > [封鎖/解除封鎖使用者]。
1. 在所需使用者旁的 [ *動作* ] 欄中，選取 [ **解除封鎖**]。
1. 在 [解除封鎖的原因] 欄位中輸入註解。
1. 當您準備好時，選取 **[確定]** 以解除封鎖使用者。

## <a name="fraud-alert"></a>詐騙警示

詐騙警示功能可讓使用者報告詐騙嘗試存取其資源。 收到未知的和可疑的 MFA 提示時，使用者可以使用 Microsoft Authenticator 應用程式或透過電話來報告詐騙嘗試。

有下列詐騙警示設定選項可供使用：

* **自動封鎖回報詐騙的使用者**：如果使用者回報詐騙，使用者帳戶的 Azure MFA 驗證嘗試會封鎖90天，或直到系統管理員解除封鎖其帳戶為止。 系統管理員可以使用登入報告來檢閱登入，並採取適當的動作以避免未來的詐騙。 系統管理員接著可以[解除封鎖](#unblock-a-user)使用者的帳戶。
* **在初始問候語期間回報詐騙**的程式碼：當使用者收到電話以執行多重要素驗證時，通常會按下 **#** 確認其登入。 若要報告詐騙，則需要在按 **#** 之前輸入代碼。 根據預設，此代碼是 **0**，但您可以自訂。

   > [!NOTE]
   > Microsoft 的預設語音問候語會指示使用者按 **0#** 以提交詐騙警示。 如果您要使用 **0** 以外的代碼，則應該記錄下來，並上傳您自己的自訂語音問候語和適當的指示。

若要啟用及設定詐騙警示，請完成下列步驟：

1. 瀏覽至 [Azure Active Directory] > [安全性] > [MFA] > [詐騙警示]。
1. 將 [允許使用者提交詐騙警示] 設定設為 [開啟]。
1. 設定 [ *自動封鎖報告詐騙* 或程式碼的使用者在 *初始問候語設定期間回報詐騙* ] （如有需要）。
1. 在準備就緒時，選取 [儲存]。

### <a name="view-fraud-reports"></a>檢視詐騙報告

選取 [Azure Active Directory] > [登入] > [驗證詳細資料]。 詐騙警示現在已是標準「Azure AD 登入」報告的一部分，且會在 [結果詳細資料] 中顯示為「拒絕 MFA，已輸入詐騙代碼」。
 
## <a name="notifications"></a>通知

當使用者報告詐騙警示時，可以設定電子郵件通知。 這些通知通常會傳送給身分識別系統管理員，因為使用者的帳號憑證可能已遭入侵。 下列範例顯示詐騙警示通知電子郵件的外觀：

![詐騙警示通知電子郵件範例](./media/howto-mfa-mfasettings/multi-factor-authentication-fraud-alert-email.png)

若要設定詐騙警示通知，請完成下列設定：

1. 流覽至**Azure Active Directory**  >  **安全性**  >  **Multi-Factor Authentication**  >  **通知**。
1. 輸入要新增至下一個方塊的電子郵件地址。
1. 若要移除現有的電子郵件地址，請選取所需電子郵件地址旁的 [ **...** ] 選項，然後選取 [ **刪除**]。
1. 在準備就緒時，選取 [儲存]。

## <a name="oath-tokens"></a>OATH 權杖

Azure AD 支援使用 OATH-TOTP SHA-1 權杖，其每隔 30 或 60 秒重新整理一次程式碼。 客戶可以向他們所選的廠商購買這些權杖。

OATH TOTP 硬體權杖通常會隨附在權杖中預先編寫的秘密金鑰或種子。 如下列步驟所述，這些金鑰必須輸入到 Azure AD 中。 祕密金鑰限制為 128 個字元，可能會與所有權杖不相容。 秘密金鑰只能包含字元 *a-z* 或 *A-Z* 和數字 *1-7*，而且必須以 *Base32* 編碼。

在軟體權杖設定流程中也可使用 Azure AD 來設定可重新植入的可程式化 OATH TOTP 硬體權杖。

OATH 硬體權杖已作為公開預覽的一部分支援。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

![將 OATH 權杖上傳到 MFA OATH 權杖刀鋒視窗](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

取得權杖後，必須使用逗號分隔值 (CSV) 檔案格式加以上傳，包括 UPN、序號、祕密金鑰、時間間隔、製造商和模型，如下列範例所示：

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567abcdef1234567abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> 請確定您在 CSV 檔案中包含標頭資料列。

將格式正確設為 CSV 檔案後，系統管理員可接著登入 Azure 入口網站，瀏覽至 [Azure Active Directory] > [安全性] > [MFA] > [OATH 權杖]，並上傳所產生的 CSV 檔案。

視 CSV 檔案的大小而定，可能需要數分鐘的時間來處理。 選取 [重新整理] 按鈕來取得目前的狀態。 如果檔案中有任何錯誤，您可下載 CSV 檔案，其中列出您需要解決的任何錯誤。 所下載 CSV 檔案中的欄位名稱與上傳的版本不同。

一旦解決任何錯誤後，系統管理員可接著針對權杖選取 [啟動] 並輸入權杖上顯示的 OTP，以啟動每個金鑰。

使用者可設定最多 5 個 OATH 硬體權杖或驗證器應用程式 (例如 Microsoft Authenticator 應用程式) 的組合，以在任何時間點使用。

## <a name="phone-call-settings"></a>通話設定

如果使用者收到 MFA 提示的電話，您可以設定其體驗，例如來電者識別碼或語音問候語。

在美國，如果您尚未設定 MFA 呼叫者識別碼，來自 Microsoft 的語音通話會來自下列號碼。 如果您使用垃圾郵件篩選器，請務必排除這些號碼：

* *+ 1 (866) 539 4191*
* *+ 1 (855) 330 8653*
* *+ 1 (877) 668 6536*

> [!NOTE]
> 透過公用電話網絡放置 Azure Multi-Factor Authentication 通話時，有時會透過不支援呼叫者識別碼的貨運公司來傳送通話。 基於這個原因，即使 Azure Multi-Factor Authentication 一律會傳送，也不保證呼叫端識別碼。 這適用于電話和 Azure Multi-Factor Authentication 所提供的文字訊息。 如果您需要驗證文字訊息是否來自 Azure Multi-Factor Authentication，請參閱 [哪些 SMS 簡短程式碼用於傳送訊息？](multi-factor-authentication-faq.md#what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users)

若要設定您自己的呼叫者識別碼，請完成下列步驟：

1. 瀏覽至 [Azure Active Directory] > [安全性] > [MFA] > [通話設定]。
1. 將 **MFA 呼叫者識別碼** 設定為您希望使用者在手機上看到的號碼。 僅允許使用美國的號碼。
1. 在準備就緒時，選取 [儲存]。

### <a name="custom-voice-messages"></a>自訂語音訊息

您可以使用自己的錄製或適用于 Azure Multi-Factor Authentication 的問候語搭配自訂語音訊息功能。 除了或取代預設的 Microsoft 錄製之外，您還可以使用這些訊息。

開始之前，請留意下列限制：

* 支援的檔案格式為 *.wav* 和 *mp3*。
* 檔案大小限制為 1 MB。
* 驗證訊息應該少於 20 秒。 超過 20 秒的訊息可能會導致驗證失敗。 在訊息結束且驗證逾時之前，使用者可能無法回應。

### <a name="custom-message-language-behavior"></a>自訂訊息語言行為

當使用者播放自訂語音訊息時，訊息的語言取決於下列因素：

* 目前使用者的語言。
  * 使用者的瀏覽器偵測到的語言。
  * 其他驗證案例可能會有不同的行為。
* 任何可用自訂訊息的語言。
  * 新增自訂訊息時，系統管理員會選擇此語言。

例如，如果只有一則使用德語的自訂訊息：

* 以德語進行驗證的使用者會聽到自訂德文訊息。
* 以英文進行驗證的使用者會聽到標準英文訊息。

### <a name="custom-voice-message-defaults"></a>自訂語音訊息預設值

您可以使用下列範例腳本來建立您自己的自訂訊息。 如果您未設定自己的自訂訊息，則這些片語是預設值：

| 訊息名稱 | 指令碼 |
| --- | --- |
| 驗證成功 | 已成功驗證您的登入。 再見。 |
| 分機提示 | 感謝您使用 Microsoft 的登入驗證系統。 請按井字鍵以繼續。 |
| 詐騙確認 | 已提交詐騙警示。 若要解除封鎖您的帳戶，請連絡您公司的 IT 技術支援中心。 |
| 詐騙問候語 (標準) | 感謝您使用 Microsoft 的登入驗證系統。 請按井字鍵完成您的驗證。 如果您並未起始此驗證，可能有其他人正在嘗試存取您的帳戶。 請按零，然後按井字鍵以提交詐騙警示。 這將會通知您公司的 IT 小組，並封鎖進一步的驗證嘗試。 |
| 已回報詐騙    已提交詐騙警示。 | 若要解除封鎖您的帳戶，請連絡您公司的 IT 技術支援中心。 |
| 啟用 | 感謝您使用 Microsoft 的登入驗證系統。 請按井字鍵完成您的驗證。 |
| 拒絕驗證重試 | 拒絕驗證。 |
| 重試 (標準) | 感謝您使用 Microsoft 的登入驗證系統。 請按井字鍵完成您的驗證。 |
| 問候語 (標準) | 感謝您使用 Microsoft 的登入驗證系統。 請按井字鍵完成您的驗證。 |
| 問候語 (PIN) | 感謝您使用 Microsoft 的登入驗證系統。 請輸入您的 PIN，然後按井字鍵以完成您的驗證。 |
| 詐騙問候語 (PIN) | 感謝您使用 Microsoft 的登入驗證系統。  請輸入您的 PIN，然後按井字鍵以完成您的驗證。 如果您並未起始此驗證，可能有其他人正在嘗試存取您的帳戶。 請按零，然後按井字鍵以提交詐騙警示。 這將會通知您公司的 IT 小組，並封鎖進一步的驗證嘗試。 |
| 重試 (PIN) | 感謝您使用 Microsoft 的登入驗證系統。 請輸入您的 PIN，然後按井字鍵以完成您的驗證。 |
| 數字後提示分機 | 若已轉接到此分機，請按井字鍵以繼續。 |
| 拒絕驗證 | 抱歉，我們目前無法將您登入。 請稍後再試一次。 |
| 啟用問候語 (標準) | 感謝您使用 Microsoft 的登入驗證系統。 請按井字鍵完成您的驗證。 |
| 啟用重試 (標準) | 感謝您使用 Microsoft 的登入驗證系統。 請按井字鍵完成您的驗證。 |
| 啟用問候語 (PIN 碼) | 感謝您使用 Microsoft 的登入驗證系統。 請輸入您的 PIN，然後按井字鍵以完成您的驗證。 |
| 數字前提示分機 | 感謝您使用 Microsoft 的登入驗證系統。 請將此通來電轉接到分機 … |

### <a name="set-up-a-custom-message"></a>設定自訂訊息

若要使用您自己的自訂訊息，請完成下列步驟：

1. 瀏覽至 [Azure Active Directory] > [安全性] > [MFA] > [通話設定]。
1. 選取 [新增問候語]。
1. 選擇問候語的 **類型** ，例如 *問候語 (標準) * 或  *驗證成功*。
1. 根據[自訂訊息語言行為](#custom-message-language-behavior)的上一節，選取**語言**。
1. 流覽並選取要上傳的 *mp3* 或 *.wav* 音效檔。
1. 準備好時，請選取 [ **新增**]，然後按一下 [ **儲存**]。

## <a name="mfa-service-settings"></a>MFA 服務設定

Azure Multi-Factor Authentication 的應用程式密碼、受信任的 IP、驗證選項和成員多重要素驗證等設定，位於服務設定中。 這是舊版入口網站的一部分，而不是一般 Azure AD 入口網站的一部分。

從 Azure 入口網站瀏覽至 [Azure Active Directory] > [安全性] > [MFA] > [開始使用] > [設定] > [其他雲端式 MFA 設定] 以存取服務設定。 新的視窗或索引標籤隨即開啟，其中包含其他 *服務設定* 選項。

## <a name="trusted-ips"></a>信任的 IP

Azure Multi-Factor Authentication 的「 _信任的 ip_ 」功能可略過從已定義的 IP 位址範圍登入之使用者的多重要素驗證提示。 您可以為您的內部部署環境設定信任的 IP 範圍，以供使用者在其中一個位置時，沒有 Azure Multi-Factor Authentication 提示。

> [!NOTE]
> 只有當您使用 MFA Server 時，信任的 ip 才可包含私人 IP 範圍。 針對雲端式 Azure Multi-Factor Authentication，您只能使用公用 IP 位址範圍。
>
> 只有 [命名位置 (預覽版) ](../conditional-access/location-condition.md#preview-features) 介面支援 IPv6 範圍。

如果您的組織部署 NPS 延伸模組以提供 MFA 給內部部署應用程式，請注意來源 IP 位址一律會顯示為驗證嘗試流經的 NPS 伺服器。

| Azure AD 租用戶類型 | 信任的 IP 功能選項 |
|:--- |:--- |
| 受管理 |**特定範圍的 ip 位址**：系統管理員會指定 ip 位址範圍，以針對從公司內部網路登入的使用者略過多重要素驗證。 最多可設定50個信任的 IP 範圍。|
| 同盟 |**所有同盟使用者**：從組織內部登入的所有同盟使用者都可以略過多重要素驗證。 使用者可使用 Active Directory Federation Services (AD FS) 所發出的宣告來略過驗證。<br/>**特定範圍的 ip 位址**：系統管理員會指定 ip 位址範圍，以針對從公司內部網路登入的使用者略過多重要素驗證。 |

受信任的 IP 略過只能從公司內部網路內部運作。 如果您選取 [ **所有同盟使用者** ] 選項，且使用者從公司內部網路外部登入，則使用者必須使用多重要素驗證進行驗證。 即使使用者提供 AD FS 宣告，程序還是相同的。

### <a name="end-user-experience-inside-of-corpnet"></a>公司網路內部的終端使用者體驗

當「信任的 Ip」功能停用時，瀏覽器流程需要多重要素驗證。 較舊的豐富型用戶端應用程式需要應用程式密碼。

使用信任的 Ip 時，瀏覽器流程不需要多重要素驗證。 較舊的豐富型用戶端應用程式不需要應用程式密碼，但前提是使用者尚未建立應用程式密碼。 應用程式密碼使用後，以後就都需要密碼。

### <a name="end-user-experience-outside-corpnet"></a>公司網路外部的終端使用者體驗

無論是否已定義信任的 IP，瀏覽器流程都需要多重要素驗證。 較舊的豐富型用戶端應用程式需要應用程式密碼。

### <a name="enable-named-locations-by-using-conditional-access"></a>使用條件式存取以啟用具名位置

您可以使用條件式存取規則，使用下列步驟來定義命名位置：

1. 在 [Azure 入口網站中，搜尋並選取 [ **Azure Active Directory**]，然後**Security**流覽至  >  **Conditional Access**  >  **名為 [位置**] 的安全性條件式存取。
1. 選取 [新增位置]。
1. 輸入位置的名稱。
1. 選取 [標記為受信任的位置]。
1. 輸入您環境的 CIDR 標記法中的 IP 範圍，例如 *40.77.182.32/27*。
1. 選取 [建立]。

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>使用條件式存取啟用可信任 IP 功能

若要使用條件式存取原則啟用受信任的 Ip，請完成下列步驟：

1. 在 [Azure 入口網站中，搜尋並選取 [ **Azure Active Directory**]，然後**Security**流覽至  >   **Conditional Access**  >  **名為 [位置**] 的安全性條件式存取。
1. 選取 [設定 MFA 信任的 IP]。
1. 在 [服務設定] 頁面的 [信任的 IP] 下方，選擇下列兩個選項中的任一項：

   * **適用於從我的內部網路產生的同盟使用者提出的要求**：若要選擇此選項，請選取核取方塊。 所有從公司網路登入的同盟使用者，都會使用 AD FS 所發行的宣告來略過多重要素驗證。 請確定 AD FS 有規則可用於將內部網路宣告新增至適當的流量。 如果規則不存在，請在 AD FS 中建立下列規則：

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **適用於從特定公用 IP 範圍提出的要求**：若要選擇此選項，請在文字方塊中，使用 CIDR 表示法輸入 IP 位址。
      * 對於範圍在 xxx.xxx.xxx.1 到 xxx.xxx.xxx.254 的 IP 位址，使用如 **xxx.xxx.xxx.0/24** 的標記法。
      * 針對單一 IP 位址，使用如 **xxx.xxx.xxx.xxx/32** 標記法。
      * 最多輸入 50 個 IP 位址範圍。 從這些 IP 位址登入的使用者會略過多重要素驗證。

1. 選取 [儲存]。

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>使用服務設定啟用信任的 IP 功能

如果您不想要使用條件式存取原則來啟用受信任的 Ip，您可以使用下列步驟來設定 Azure Multi-Factor Authentication 的 *服務設定* ：

1. 在 [Azure 入口網站中，搜尋並選取 [ **Azure Active Directory**]，然後選擇 [ **使用者**]。
1. 選取 [多重要素驗證]。
1. 在 [Multi-Factor Authentication] 下，選取 [服務設定]。
1. 在 [服務設定] 頁面的 [信任的 IP] 下方，選擇下列兩個選項中的一或兩項：

   * **適用於從我的內部網路的同盟使用者提出的要求**：若要選擇此選項，請選取核取方塊。 所有從公司網路登入的同盟使用者，都會使用 AD FS 所發行的宣告來略過多重要素驗證。 請確定 AD FS 有規則可用於將內部網路宣告新增至適當的流量。 如果規則不存在，請在 AD FS 中建立下列規則：

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **適用於從特定 IP 位址子網路範圍提出的要求**：若要選擇此選項，請在文字方塊中，使用 CIDR 表示法輸入 IP 位址。
      * 對於範圍在 xxx.xxx.xxx.1 到 xxx.xxx.xxx.254 的 IP 位址，使用如 **xxx.xxx.xxx.0/24** 的標記法。
      * 針對單一 IP 位址，使用如 **xxx.xxx.xxx.xxx/32** 標記法。
      * 最多輸入 50 個 IP 位址範圍。 從這些 IP 位址登入的使用者會略過多重要素驗證。

1. 選取 [儲存]。

## <a name="verification-methods"></a>驗證方法

您可以在服務設定入口網站中選擇可供使用者使用的驗證方法。 使用者將其帳戶註冊進行 Azure Multi-Factor Authentication 時，會在您已啟用的選項中選擇慣用驗證方法。 [設定我的帳戶進行多重要素驗證](../user-help/multi-factor-authentication-end-user-first-time.md)時，會提供使用者註冊程式的指引。

以下是可用的驗證方法：

| 方法 | 描述 |
|:--- |:--- |
| 電話通話 |撥打自動語音電話。 使用者可接聽電話並按電話鍵盤上的 # 進行驗證。 此電話號碼不會同步到內部部署 Active Directory。 |
| 電話簡訊 |傳送包含驗證碼的簡訊。 系統會提示使用者在登入介面中輸入這個驗證碼。 此程序稱為「單向 SMS」。 雙向 SMS 表示使用者必須以簡訊回傳特定驗證碼。 雙向 SMS 已淘汰，且在 2018 年 11 月 14 之後不支援。 系統管理員應該為先前使用雙向 SMS 的使用者啟用另一種方法。|
| 行動應用程式的通知 |將推播通知傳送至您的電話或已註冊的裝置。 使用者會看到通知，然後選取 [驗證] 來完成驗證。 Microsoft Authenticator 應用程式適用於 [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6)、[Android](https://go.microsoft.com/fwlink/?Linkid=825072) 和 [iOS](https://go.microsoft.com/fwlink/?Linkid=825073)。 |
| 來自行動應用程式或硬體 Token 的驗證碼 |Microsoft Authenticator 應用程式每隔 30 秒會產生新的 OATH 驗證碼。 使用者會在登入介面中輸入此驗證碼。 Microsoft Authenticator 應用程式適用於 [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6)、[Android](https://go.microsoft.com/fwlink/?Linkid=825072) 和 [iOS](https://go.microsoft.com/fwlink/?Linkid=825073)。 |

如需詳細資訊，請參閱 [Azure AD 有哪些驗證和驗證方法？](concept-authentication-methods.md)

### <a name="enable-and-disable-verification-methods"></a>啟用和停用驗證方法

若要啟用或停用驗證方法，請完成下列步驟：

1. 在 [Azure 入口網站中，搜尋並選取 [ **Azure Active Directory**]，然後選擇 [ **使用者**]。
1. 選取 [多重要素驗證]。
1. 在 [Multi-Factor Authentication] 下，選取 [服務設定]。
1. 在 [服務設定] 頁面的 [驗證選項] 下，選取/取消選取要提供給使用者的方法。
1. 按一下 [檔案] 。

## <a name="remember-multi-factor-authentication"></a>記住 Multi-Factor Authentication

_記住 Multi-Factor Authentication_功能可讓使用者在使用 Multi-Factor Authentication 成功登入裝置之後，略過指定天數的後續驗證。 若要增強可用性並將使用者在相同裝置上執行 MFA 的次數降到最低，請選取90天或更多的持續時間。

> [!IMPORTANT]
> 若帳戶或裝置遭到入侵，請記住受信任裝置的 Multi-Factor Authentication 可能會影響安全性。 如果公司帳戶遭入侵或信任的裝置遺失或遭竊，您應該[撤銷 MFA 工作階段](howto-mfa-userdevicesettings.md)。
>
> 還原動作會從所有裝置撤銷受信任的狀態，而使用者必須再次執行多重要素驗證。 您也可以指示使用者在自己的裝置上還原 Multi-Factor Authentication，如 [ [管理您的多重要素驗證設定](../user-help/multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device)] 中所述。

### <a name="how-the-feature-works"></a>此功能的運作方式

當使用者在登入時選取 [X 天內不要再問我] 選項時，記住 Multi-Factor Authentication 功能就會設定瀏覽器上的永續性 cookie。 該相同的瀏覽器不會再次提示使用者進行 Multi-Factor Authentication，直到 cookie 到期為止。 如果使用者在相同的裝置上開啟不同的瀏覽器或清除其 cookie，系統會提示他們再次確認。

非瀏覽器應用程式上不會顯示 [X 天內不要再問我] 選項，不論應用程式是否支援新式驗證。 這些應用程式使用每隔一小時會提供新存取權杖的_重新整理權杖_。 驗證重新整理權杖時，Azure AD 會檢查在指定天數內是否發生最後一個多重要素驗證。

此功能會減少 web 應用程式上的驗證次數，一般每次皆會提示。 如果設定較低的期間，此功能可能會增加通常每隔90天提示的新式驗證用戶端的驗證次數。 若與條件式存取原則結合，也可能可以增加驗證次數。

> [!IMPORTANT]
> 當使用者透過 Azure Multi-Factor Authentication Server 或協力廠商多重要素驗證解決方案執行多重要素 AD FS 驗證時，[ **記住 Multi-Factor Authentication** ] 功能不相容 AD FS 的 [ **讓我保持登入** ] 功能。
>
> 如果您的使用者選取 AD FS 上的 [讓我保持登入]，且將他們的裝置標記為受 Multi-Factor Authentication 信任，在 [記住多重要素驗證] 天數到期之後，並不會自動驗證使用者。 Azure AD 會要求全新的多重要素驗證，但 AD FS 會傳回具有原始 Multi-Factor Authentication 宣告和日期的權杖，而不是重新執行多重要素驗證。 **這個反應會啟動 Azure AD 與 AD FS 之間的驗證迴圈。**
>
> [記住 Multi-Factor Authentication] 功能與 B2B 使用者不相容，且 B2B 使用者在登入受邀租用戶時將無法看到該功能。
>

### <a name="enable-remember-multi-factor-authentication"></a>啟用記住 Multi-Factor Authentication

若要啟用並設定使用者記住其 MFA 狀態和略過提示的選項，請完成下列步驟：

1. 在 [Azure 入口網站中，搜尋並選取 [ **Azure Active Directory**]，然後選擇 [ **使用者**]。
1. 選取 [多重要素驗證]。
1. 在 [Multi-Factor Authentication] 下，選取 [服務設定]。
1. 在 [ **服務設定** ] 頁面的 [ **記住多重要素驗證**] 下，選取 [ **允許使用者在其信任的裝置上記住多重要素驗證** ] 選項。
1. 設定允許受信任的裝置略過多重要素驗證的天數。 為了獲得最佳的使用者體驗，請將持續時間延長至 *90* 或更多天。
1. 選取 [儲存]。

### <a name="mark-a-device-as-trusted"></a>將裝置標示為受信任

啟用「記住 Multi-Factor Authentication」功能之後，使用者可以選取 [ **不要再詢問**] 選項，將裝置標示為受信任。

## <a name="next-steps"></a>後續步驟

若要深入瞭解在 Azure Multi-Factor Authentication 中使用的可用方法，請參閱 [Azure Active Directory 中有哪些驗證和驗證方法？](concept-authentication-methods.md)

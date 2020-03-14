---
title: Azure MFA 的存取和使用方式報表-Azure Active Directory
description: 說明如何使用 Azure Multi-Factor Authentication 功能 - 報告。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 52d9f7a0b2a7cebefdb5ade8e16417043c5c83d3
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79263747"
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication 中的報告

Azure Multi-Factor Authentication 提供數個報告，可供您和貴組織透過 Azure 入口網站使用。 下表列出可用的報告：

| Report | Location | 描述 |
|:--- |:--- |:--- |
| 已封鎖的使用者歷程記錄 | Azure AD > 安全性 > MFA > 封鎖/解除封鎖使用者 | 顯示使用者封鎖或解除封鎖要求的歷程記錄。 |
| 使用方式和詐騙警示 | Azure AD > 登入 | 提供整體使用量、使用者摘要和使用者詳細資料的相關資訊；以及在指定的日期範圍期間所提交的詐騙警示歷程記錄。 |
| 內部部署元件的使用方式 | Azure AD > 安全性 > MFA > 活動報告 | 透過 NPS 擴充功能、ADFS 和 MFA 伺服器提供 MFA 整體使用量的相關資訊。 |
| 已略過的使用者歷程記錄 | Azure AD > 安全性 > MFA > 一次性略過 | 提供針對使用者許可 Multi-Factor Authentication 之要求的歷程記錄。 |
| 伺服器狀態 | Azure AD > 安全性 > MFA > 伺服器狀態 | 顯示與帳戶相關聯之 Multi-Factor Authentication Server 的狀態。 |

## <a name="view-mfa-reports"></a>檢視 MFA 報告

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在左側，選取 [ **Azure Active Directory** > **安全性**] > [ **MFA**]。
3. 選取您要檢視的報告。

   ![Azure 入口網站中的 MFA Server 伺服器狀態報表](./media/howto-mfa-reporting/report.png)

## <a name="azure-ad-sign-ins-report"></a>Azure AD 登入報告

透過 **Azure 入口網站**中的[登入活動報告](https://portal.azure.com)，您可以取得判斷環境執行狀況所需的資訊。

登入報告可為您提供受控應用程式使用方式和使用者登入活動的相關資訊，包括 Multi-Factor Authentication (MFA) 使用方式的相關資訊。 MFA 資料可讓您深入了解 MFA 如何在您的組織中運作。 它可讓您回答問題，例如：

- 是否以 MFA 挑戰登入？
- 使用者如何完成 MFA？
- 為何使用者無法完成 MFA？
- 有多少使用者經過 MFA 挑戰？
- 有多少使用者無法完成 MFA 挑戰？
- 使用者會遇到的常見 MFA 問題是什麼？

此資料可透過 [Azure 入口網站](https://portal.azure.com)和[報告 API](../reports-monitoring/concept-reporting-api.md) 取得。

![Azure AD Azure 入口網站中的登入報告](./media/howto-mfa-reporting/sign-in-report.png)

### <a name="sign-ins-report-structure"></a>登入報告結構

MFA 的登入活動報告可讓您存取下列資訊：

**需要 MFA：** 登入是否需要 MFA。 由於每個使用者的 MFA、條件式存取或其他原因，可能需要 MFA。 可能的值為 [是] 或 [否]。

**MFA 結果：** MFA 是否已滿足或拒絕的詳細資訊：

- 如果已滿足 MFA，此資料行提供如何滿足 MFA 的詳細資訊。
   - Azure Multi-Factor Authentication
      - 在雲端中完成
      - 由於租用戶上設定的原則所以已過期
      - 註冊提示
      - 因為在權杖中宣告而滿足
      - 因為外部提供者提供的宣告而滿足
      - 因為強式驗證而滿足
      - 因為運用的流程是 Windows 訊息代理程式登入流程而略過
      - 因為應用程式密碼而略過
      - 因為位置而略過
      - 因為已註冊的裝置而略過
      - 因為已記住的裝置而略過
      - 已成功完成
   - 重新導向至外部提供者以進行多重要素驗證

- 如果已拒絕 MFA，此資料行會提供拒絕的原因。
   - Azure Multi-Factor Authentication 遭到拒絕；
      - 驗證進行中
      - 重複的驗證嘗試
      - 輸入太多次不正確的代碼
      - 無效的驗證
      - 無效的行動應用程式驗證碼
      - 設定錯誤
      - 撥打電話轉到語音信箱
      - 電話號碼的格式無效
      - 服務錯誤
      - 無法接通使用者的電話
      - 無法將行動應用程式通知傳送到裝置
      - 無法傳送行動應用程式通知
      - 使用者拒絕驗證
      - 使用者未回應行動應用程式通知
      - 使用者沒有任何已註冊的驗證方法
      - 使用者輸入不正確的代碼
      - 使用者輸入不正確的 PIN
      - 使用者未成功驗證即掛斷電話
      - 使用者遭到封鎖
      - 使用者從未輸入驗證碼
      - 找不到使用者
      - 驗證碼已使用過一次

**MFA 驗證方法：** 使用者用來完成 MFA 的驗證方法。 可能的值包括：

- 簡訊
- 行動應用程式通知
- 撥打電話 (驗證電話)
- 行動應用程式驗證碼
- 撥打電話 (辦公室電話)
- 撥打電話 (替代驗證電話)

**MFA 驗證詳細資料：** 電話號碼的清除版本，例如：+X XXXXXXXX64。

**條件式存取**尋找影響登入嘗試之條件式存取原則的相關資訊，包括：

- 原則名稱
- 授與控制
- 工作階段控制項
- 結果

## <a name="powershell-reporting-on-users-registered-for-mfa"></a>已針對 MFA 註冊的使用者進行 PowerShell 報告

首先，請確定您已安裝[MSOnline V1 PowerShell 模組](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0)。

識別已使用下列 Powershell 註冊 MFA 的使用者。

```Get-MsolUser -All | Where-Object {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

識別尚未使用下列 Powershell 註冊 MFA 的使用者。

```Get-MsolUser -All | Where-Object {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

識別已註冊的使用者和輸出方法。 

```PowerShell
Get-MsolUser -All | Select-Object @{N='UserPrincipalName';E={$_.UserPrincipalName}},

@{N='MFA Status';E={if ($_.StrongAuthenticationRequirements.State){$_.StrongAuthenticationRequirements.State} else {"Disabled"}}},

@{N='MFA Methods';E={$_.StrongAuthenticationMethods.methodtype}} | Export-Csv -Path c:\MFA_Report.csv -NoTypeInformation
```

## <a name="possible-results-in-activity-reports"></a>活動報告中的可能結果

下表可用於使用下載的多因素驗證活動報告版本進行多重要素驗證疑難排解。 它們不會直接出現在 Azure 入口網站中。

| 呼叫結果 | 描述 | 廣泛描述 |
| --- | --- | --- |
| SUCCESS_WITH_PIN | 已輸入 PIN | 使用者已輸入 PIN。  如果驗證成功，表示使用者已輸入正確的 PIN。  如果驗證遭到拒絕，則他們輸入了不正確的 PIN，或使用者設定為標準模式。 |
| SUCCESS_NO_PIN | 僅輸入 # | 如果使用者設定為 PIN 模式且驗證遭到拒絕，這表示使用者未輸入 PIN，只輸入 #。  如果使用者設定為標準模式且驗證成功，這表示使用者只輸入 #，這是在標準模式中要執行的正確動作。 |
| SUCCESS_WITH_PIN_BUT_TIMEOUT | 輸入後未按 # | 使用者未傳送任何 DTMF 數字，因為並未輸入 #。  除非輸入 # 表示已完成輸入，否則不會傳送其他數字。 |
|SUCCESS_NO_PIN_BUT_TIMEOUT | 無電話輸入 - 已逾時 | 已接聽電話，但沒有回應。  這通常表示電話已轉到語音信箱。 |
| SUCCESS_PIN_EXPIRED | PIN 已到期且未變更 | 使用者的 PIN 已到期且使用者已收到變更提示，但是 PIN 變更未順利完成。 |
| SUCCESS_USED_CACHE | 已使用快取 | 驗證成功，沒有多重要素驗證呼叫，因為在設定的快取時間範圍內發生相同使用者名稱的先前驗證成功。 |
| SUCCESS_BYPASSED_AUTH | 已略過驗證 | 使用針對使用者起始之單次許可的驗證已成功。  如需許可的詳細資訊，請參閱略過的使用者歷程記錄報告。 |
| SUCCESS_USED_IP_BASED_CACHE | 使用以 IP 為基礎的快取 | 驗證成功，沒有多重要素驗證呼叫，因為在設定的快取時間範圍內發生了相同使用者名稱、驗證類型、應用程式名稱和 IP 的先前驗證成功。 |
| SUCCESS_USED_APP_BASED_CACHE | 使用以應用程式為基礎的快取 | 驗證成功，沒有多重要素驗證呼叫，因為先前在設定的快取時間範圍內，有相同使用者名稱、驗證類型和應用程式名稱的成功驗證。 |
| SUCCESS_INVALID_INPUT | 電話輸入無效 | 電話傳來的回應無效。  這可能來自傳真電腦或數據機，或使用者可能已在 PIN 中輸入 *。 |
| SUCCESS_USER_BLOCKED | 已封鎖使用者 | 已封鎖使用者的電話號碼。  使用者可以在驗證呼叫期間或由使用 Azure 入口網站的系統管理員，起始封鎖的號碼。 <br> 注意：封鎖的數位也是詐騙警示的副產品。 |
| SUCCESS_SMS_AUTHENTICATED | 已驗證簡訊 | 使用者已使用其單次密碼 (OTP) 或 OTP + PIN，正確回覆雙向簡訊。 |
| SUCCESS_SMS_SENT | 已傳送簡訊 | 針對簡訊，已成功傳送包含單次密碼 (OTP) 的簡訊。  使用者將在應用程式中輸入 OTP 或 OTP + PIN，以完成驗證。 |
| SUCCESS_PHONE_APP_AUTHENTICATED | 行動裝置應用程式已驗證 | 使用者已透過行動裝置應用程式驗證成功。 |
| SUCCESS_OATH_CODE_PENDING | OATH 代碼擱置中 | 使用者已收到提供 OATH 代碼的提示但並未回應。 |
| SUCCESS_OATH_CODE_VERIFIED | 已驗證 OATH 代碼 | 使用者已在收到提示時輸入有效的 OATH 代碼。 |
| SUCCESS_FALLBACK_OATH_CODE_VERIFIED | 已驗證遞補 OATH 代碼 | 使用者使用其主要 Multi-Factor Authentication 方法驗證遭到拒絕，隨後提供遞補用的有效 OATH 代碼。 |
| SUCCESS_FALLBACK_SECURITY_QUESTIONS_ANSWERED | 已回答遞補安全性問題 | 使用者使用其主要 Multi-Factor Authentication 方法驗證遭到拒絕，隨後正確回答遞補用的安全性問題。 |
| FAILED_PHONE_BUSY | 驗證進行中 | Multi-Factor Authentication 已在為此使用者處理驗證。  這通常是因為 RADIUS 用戶端在相同的登入期間傳送多個驗證要求所致。 |
| CONFIG_ISSUE | 無法接通電話 | 已嘗試呼叫，但無法放置或未回答。  這包括忙碌信號、快速忙碌信號（已中斷連線）、三色調（不在服務中的數位）、響鈴一段時間等。 |
| FAILED_INVALID_PHONENUMBER | 電話號碼格式無效 | 電話號碼中有無效的格式。  電話號碼必須是數位，而且國家/地區代碼必須是10位數 + 1 （美國 & 加拿大）。 |
| FAILED_USER_HUNGUP_ON_US | 使用者已掛斷電話 | 使用者已接聽電，但隨後未按下任何按鍵即掛斷。 |
| FAILED_INVALID_EXTENSION | 分機號碼無效 | 分機號碼包含無效的字元。  允許的字元僅限數字、逗號、* 及 #。  前面還可以加上 @。 |
| FAILED_FRAUD_CODE_ENTERED | 已輸入詐騙代碼 | 在導致系統拒絕驗證並封鎖電話號碼的通話期間，使用者已選擇回報詐騙。| 
| FAILED_SERVER_ERROR | 無法撥打電話 | 多重要素驗證服務無法放置呼叫。 |
| FAILED_SMS_NOT_SENT | 無法傳送簡訊 | 無法傳送文字訊息。  驗證遭到拒絕。 |
| FAILED_SMS_OTP_INCORRECT | 簡訊 OTP 不正確 | 使用者從收到的文字訊息輸入了不正確的單次密碼（OTP）。  驗證遭到拒絕。 |
| FAILED_SMS_OTP_PIN_INCORRECT | 簡訊 OTP + PIN 不正確 | 使用者輸入了不正確的單次密碼（OTP）和/或不正確的使用者 PIN。  驗證遭到拒絕。 |
| FAILED_SMS_MAX_OTP_RETRY_REACHED | 超過文字訊息 OTP 嘗試次數上限 | 使用者已超過單次密碼（OTP）嘗試次數的上限。 |
| FAILED_PHONE_APP_DENIED | 拒絕行動裝置應用程式 | 使用者已按下 [拒絕] 按鈕，在行動裝置應用程式中拒絕驗證。 |
| FAILED_PHONE_APP_INVALID_PIN | 行動裝置應用程式 PIN 無效 | 在行動裝置應用程式中進行驗證時，使用者輸入無效的 PIN。 |
| FAILED_PHONE_APP_PIN_NOT_CHANGED | 行動裝置應用程式 PIN 未變更 | 使用者未在行動裝置應用程式中順利完成必要的 PIN 變更。 |
| FAILED_FRAUD_REPORTED | 回報詐騙 | 使用者已在行動裝置應用程式中回報詐騙。 |
| FAILED_PHONE_APP_NO_RESPONSE | 行動裝置應用程式沒有回應 | 使用者未回應行動應用程式驗證要求。 |
| FAILED_PHONE_APP_ALL_DEVICES_BLOCKED | 已封鎖行動裝置應用程式的所有裝置 | 這位使用者的行動裝置應用程式裝置已經不再回應通知且已遭封鎖。 |
| FAILED_PHONE_APP_NOTIFICATION_FAILED | 行動裝置應用程式通知失敗 | 嘗試傳送通知到使用者裝置上的行動裝置應用程式時失敗。 |
| FAILED_PHONE_APP_INVALID_RESULT | 行動裝置應用程式結果無效 | 行動裝置應用程式傳回無效的結果。 |
| FAILED_OATH_CODE_INCORRECT | OATH 代碼不正確 | 使用者輸入不正確的 OATH 代碼。  驗證遭到拒絕。 |
| FAILED_OATH_CODE_PIN_INCORRECT | OATH 代碼 + PIN 不正確 | 使用者輸入了不正確的 OATH 代碼及/或不正確的使用者 PIN。  驗證遭到拒絕。 |
| FAILED_OATH_CODE_DUPLICATE | 重複的 OATH 代碼 | 使用者輸入之前使用過的 OATH 代碼。  驗證遭到拒絕。 |
| FAILED_OATH_CODE_OLD | OATH 代碼已過期 | 使用者輸入之前使用過的 OATH 代碼之前的 OATH 代碼。  驗證遭到拒絕。 |
| FAILED_OATH_TOKEN_TIMEOUT | OATH 代碼結果超時 | 使用者花費太多時間來輸入 OATH 代碼，而多重要素驗證嘗試已超時。 |
| FAILED_SECURITY_QUESTIONS_TIMEOUT | 安全性問題結果的時間 | 使用者花太多時間輸入安全性問題的答案，而多重要素驗證嘗試已超時。 |
| FAILED_AUTH_RESULT_TIMEOUT | 驗證結果超時 | 使用者花費太多時間來完成多重要素驗證嘗試。 |
| FAILED_AUTHENTICATION_THROTTLED | 已節流驗證 | 服務已對多重要素驗證嘗試進行節流。 |

## <a name="next-steps"></a>後續步驟

* [SSPR 和 MFA 使用方式和深入解析報告](howto-authentication-methods-usage-insights.md)
* [適用於使用者](../user-help/multi-factor-authentication-end-user.md)
* [部署的位置](concept-mfa-whichversion.md)

---
title: Azure Multi-Factor Authentication 的登入事件詳細資料 - Azure Active Directory
description: 了解如何檢視 Azure Multi-Factor Authentication 事件的登入活動和狀態訊息。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 05/15/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c9bf76729c3b5844918659283a65eeb347c4237d
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83639856"
---
# <a name="use-the-sign-ins-report-to-review-azure-multi-factor-authentication-events"></a>使用登入報告來檢閱 Azure Multi-Factor Authentication 事件

若要檢閱並了解 Azure Multi-Factor Authentication 事件，您可使用 Azure Active Directory (Azure AD) 登入報告。 當系統提示使用者進行多重要素驗證時，以及如果有任何條件式存取原則正在使用，則此報告可顯示事件的驗證詳細資料。 如需登入報告的詳細資訊，請參閱 [Azure AD 中的登入活動報告概觀](../reports-monitoring/concept-sign-ins.md)。

本文說明如何在 Azure 入口網站中檢視 Azure AD 登入報告，然後進行 MSOnline V1 PowerShell 模組。

## <a name="view-the-azure-ad-sign-ins-report"></a>檢視 Azure AD 登入報告

登入報告提供受控應用程式使用方式和使用者登入活動的資訊，包括 Multi-Factor Authentication (MFA) 使用方式的資訊。 MFA 資料可讓您深入了解 MFA 如何在您的組織中運作。 其可供回答如下的問題：

- 是否以 MFA 挑戰登入？
- 使用者如何完成 MFA？
- 為何使用者無法完成 MFA？
- 有多少使用者經過 MFA 挑戰？
- 有多少使用者無法完成 MFA 挑戰？
- 使用者會遇到的常見 MFA 問題是什麼？

若要在 [Azure 入口網站](https://portal.azure.com)中檢視登入活動報告，請完成下列步驟。 您也可以使用[報告 API](../reports-monitoring/concept-reporting-api.md) 來查詢資料。

1. 使用具備「全域系統管理員」權限的帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋並選取 [Azure Active Directory]，然後從左側功能表中選擇 [使用者]。
1. 在左側功能表中的 [活動] 下，選取 [登入]。
1. 登入事件清單會隨即顯示，其中包含狀態。 您可選取事件來檢視更多詳細資料。

    事件詳細資料的 [驗證詳細資料] 或 [條件式存取] 索引標籤會顯示狀態碼或觸發 MFA 提示的原則。

    [![](media/howto-mfa-reporting/sign-in-report-cropped.png "Screenshot of example Azure Active Directory sign-ins report in the Azure portal")](media/howto-mfa-reporting/sign-in-report.png#lightbox)

在適用的情況下，會顯示驗證方式，例如簡訊、Microsoft Authenticator 應用程式通知或撥打電話。

下列詳細資料會顯示在登入事件的 [驗證詳細資料] 視窗中，指出已滿足或拒絕 MFA 要求：

* 如果已滿足 MFA，此資料行提供如何滿足 MFA 的詳細資訊。
   * 在雲端中完成
   * 由於租用戶上設定的原則所以已過期
   * 註冊提示
   * 因為在權杖中宣告而滿足
   * 因為外部提供者提供的宣告而滿足
   * 因為強式驗證而滿足
   * 因為運用的流程是 Windows 訊息代理程式登入流程而略過
   * 因為應用程式密碼而略過
   * 因為位置而略過
   * 因為已註冊的裝置而略過
   * 因為已記住的裝置而略過
   * 已成功完成

* 如果已拒絕 MFA，此資料行會提供拒絕的原因。
   * 驗證進行中
   * 重複的驗證嘗試
   * 輸入太多次不正確的代碼
   * 無效的驗證
   * 無效的行動應用程式驗證碼
   * 設定錯誤
   * 撥打電話轉到語音信箱
   * 電話號碼的格式無效
   * 服務錯誤
   * 無法接通使用者的電話
   * 無法將行動應用程式通知傳送到裝置
   * 無法傳送行動應用程式通知
   * 使用者拒絕驗證
   * 使用者未回應行動應用程式通知
   * 使用者沒有任何已註冊的驗證方法
   * 使用者輸入不正確的代碼
   * 使用者輸入不正確的 PIN
   * 使用者未成功驗證即掛斷電話
   * 使用者遭到封鎖
   * 使用者從未輸入驗證碼
   * 找不到使用者
   * 驗證碼已使用過一次

## <a name="powershell-reporting-on-users-registered-for-mfa"></a>PowerShell 報告已註冊 MFA 的使用者

首先，請確定已安裝 [MSOnline V1 PowerShell 模組](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0)。

識別已使用下列 Powershell 註冊 MFA 的使用者。 這組命令會排除已停用的使用者，因為這些帳戶無法針對 Azure AD 進行驗證：

```powershell
Get-MsolUser -All | Where-Object {$_.StrongAuthenticationMethods -ne $null -and $_.BlockCredential -eq $False} | Select-Object -Property UserPrincipalName
```

識別尚未使用下列 Powershell 註冊 MFA 的使用者。 這組命令會排除已停用的使用者，因為這些帳戶無法針對 Azure AD 進行驗證：

```powershell
Get-MsolUser -All | Where-Object {$_.StrongAuthenticationMethods.Count -eq 0 -and $_.BlockCredential -eq $False} | Select-Object -Property UserPrincipalName
```

識別已註冊的使用者和輸出方法：

```powershell
Get-MsolUser -All | Select-Object @{N='UserPrincipalName';E={$_.UserPrincipalName}},

@{N='MFA Status';E={if ($_.StrongAuthenticationRequirements.State){$_.StrongAuthenticationRequirements.State} else {"Disabled"}}},

@{N='MFA Methods';E={$_.StrongAuthenticationMethods.methodtype}} | Export-Csv -Path c:\MFA_Report.csv -NoTypeInformation
```

## <a name="downloaded-activity-reports-result-codes"></a>下載的活動報告結果碼

下表使用透過先前入口網站步驟或 PowerShell 命令下載的活動報告版本，以協助針對事件進行疑難排解。 這些結果碼不會直接出現在 Azure 入口網站中。

| 通話結果 | 描述 | 廣泛的描述 |
| --- | --- | --- |
| SUCCESS_WITH_PIN | 已輸入 PIN | 使用者已輸入 PIN。  如果驗證成功，這表示使用者已輸入正確的 PIN。  如果驗證遭到拒絕，這表示使用者輸入不正確的 PIN，或使用者未設定為標準模式。 |
| SUCCESS_NO_PIN | 只輸入 # | 如果使用者設定為 PIN 模式且驗證遭到拒絕，這表示使用者未輸入其 PIN，只輸入 #。  如果使用者設定為標準模式且驗證成功，這表示使用者只輸入 #，而在標準模式下這是正確的動作。 |
| SUCCESS_WITH_PIN_BUT_TIMEOUT | 輸入後未按 # | 使用者未傳送任何 DTMF 數字，因為並未輸入 #。  除非輸入 # 表示已完成輸入，否則不會傳送其他數字。 |
|SUCCESS_NO_PIN_BUT_TIMEOUT | 無電話輸入 - 已逾時 | 已接聽電話，但沒有回應。  這通常表示電話已轉到語音信箱。 |
| SUCCESS_PIN_EXPIRED | PIN 已到期且未變更 | 使用者的 PIN 已到期且使用者已收到變更提示，但 PIN 變更未順利完成。 |
| SUCCESS_USED_CACHE | 已使用快取 | 無需進行 Multi-Factor Authentication 通話即驗證成功，因為先前針對所設定快取時間範圍內發生的相同使用者名稱驗證已成功。 |
| SUCCESS_BYPASSED_AUTH | 許可的驗證 | 使用針對使用者起始的單次許可驗證已成功。  如需許可的詳細資料，請參閱＜許可的使用者歷程記錄報告＞。 |
| SUCCESS_USED_IP_BASED_CACHE | 使用了以 IP 為主的快取 | 無需進行 Multi-Factor Authentication 通話即驗證成功，因為先前針對所設定快取時間範圍內發生的相同使用者名稱、驗證類型、驗證名稱和 IP 驗證已成功。 |
| SUCCESS_USED_APP_BASED_CACHE | 使用了以應用程式為基礎的快取 | 無需進行 Multi-Factor Authentication 通話即驗證成功，因為先前針對所設定快取時間範圍內的相同使用者名稱、驗證類型和驗證名稱驗證已成功。 |
| SUCCESS_INVALID_INPUT | 電話輸入無效 | 電話傳來的回應無效。  回應可能來自傳真機或數據機，或使用者輸入的 PIN 可能包含 *。 |
| SUCCESS_USER_BLOCKED | 使用者遭到封鎖 | 已封鎖使用者的電話號碼。  封鎖的號碼可由使用者在驗證通話期間加以起始，或由系統管理員使用 Azure 入口網站加以起始。 <br> 注意：  封鎖的號碼也是詐騙警示副產品。 |
| SUCCESS_SMS_AUTHENTICATED | 已驗證簡訊 | 針對雙向簡訊，使用者已使用其一次性密碼 (OTP) 或 OTP + PIN 來正確回覆。 |
| SUCCESS_SMS_SENT | 已傳送簡訊 | 針對簡訊，已成功傳送包含一次性密碼 (OTP) 的簡訊。  使用者將在應用程式中輸入 OTP 或 OTP + PIN，以完成驗證。 |
| SUCCESS_PHONE_APP_AUTHENTICATED | 行動裝置應用程式已驗證 | 使用者已透過行動裝置應用程式驗證成功。 |
| SUCCESS_OATH_CODE_PENDING | OATH 代碼擱置中 | 使用者已收到提供 OATH 代碼的提示但並未回應。 |
| SUCCESS_OATH_CODE_VERIFIED | 已驗證 OATH 代碼 | 使用者已在收到提示時輸入有效的 OATH 代碼。 |
| SUCCESS_FALLBACK_OATH_CODE_VERIFIED | 已驗證遞補 OATH 代碼 | 使用者以其主要 Multi-Factor Authentication 方法來驗證遭到拒絕，且隨後提供遞補用的有效 OATH 代碼。 |
| SUCCESS_FALLBACK_SECURITY_QUESTIONS_ANSWERED | 已回答遞補安全性問題 | 使用者以其主要 Multi-Factor Authentication 方法來驗證遭到拒絕，且隨後正確回答遞補用的安全性問題。 |
| FAILED_PHONE_BUSY | 驗證已在進行中 | Multi-Factor Authentication 已在為此使用者處理驗證。  這種情況通常是因為 RADIUS 用戶端在相同登入期間傳送多個驗證要求所造成。 |
| CONFIG_ISSUE | 無法接通電話 | 已嘗試通話，但無法撥通或沒有接聽。  這包括忙線訊號、快速忙線訊號 (斷線)、三音調 (空號)、鈴響時逾時等。 |
| FAILED_INVALID_PHONENUMBER | 電話號碼格式無效 | 電話號碼中有無效的格式。  電話號碼必須是數字且必須是 10 位數，如需國碼 (地區碼)，請加 1 (美國與加拿大)。 |
| FAILED_USER_HUNGUP_ON_US | 使用者已掛斷電話 | 使用者已接聽電話，但隨後未按下任何按鍵即掛斷。 |
| FAILED_INVALID_EXTENSION | 分機號碼無效 | 分機號碼包含無效的字元。  允許的字元僅限數字、逗號、* 及 #。  前面還可以加上 @。 |
| FAILED_FRAUD_CODE_ENTERED | 已輸入詐騙代碼 | 在導致系統拒絕驗證並封鎖電話號碼的通話期間，使用者已選擇回報詐騙。| 
| FAILED_SERVER_ERROR | 無法撥打電話 | Multi-Factor Authentication 服務無法撥打電話。 |
| FAILED_SMS_NOT_SENT | 無法傳送簡訊 | 無法傳送簡訊。  驗證遭到拒絕。 |
| FAILED_SMS_OTP_INCORRECT | 簡訊 OTP 不正確 | 使用者從收到的簡訊所輸入一次性密碼 (OTP) 不正確。  驗證遭到拒絕。 |
| FAILED_SMS_OTP_PIN_INCORRECT | 簡訊 OTP + PIN 不正確 | 使用者輸入的一次性密碼 (OTP) 及/或使用者 PIN 不正確。  驗證遭到拒絕。 |
| FAILED_SMS_MAX_OTP_RETRY_REACHED | 已超過簡訊 OTP 嘗試次數上限 | 使用者已超過一次性密碼 (OTP) 嘗試次數上限。 |
| FAILED_PHONE_APP_DENIED | 拒絕行動裝置應用程式 | 使用者已按下 [拒絕] 按鈕，在行動裝置應用程式中拒絕驗證。 |
| FAILED_PHONE_APP_INVALID_PIN | 行動裝置應用程式 PIN 無效 | 在行動裝置應用程式中進行驗證時，使用者輸入無效的 PIN。 |
| FAILED_PHONE_APP_PIN_NOT_CHANGED | 行動裝置應用程式 PIN 未變更 | 使用者未在行動裝置應用程式中順利完成必要的 PIN 變更。 |
| FAILED_FRAUD_REPORTED | 回報詐騙 | 使用者已在行動裝置應用程式中回報詐騙。 |
| FAILED_PHONE_APP_NO_RESPONSE | 行動裝置應用程式沒有回應 | 使用者未回應行動裝置應用程式驗證要求。 |
| FAILED_PHONE_APP_ALL_DEVICES_BLOCKED | 已封鎖行動裝置應用程式的所有裝置 | 這位使用者的行動裝置應用程式裝置已經不再回應通知且已遭封鎖。 |
| FAILED_PHONE_APP_NOTIFICATION_FAILED | 行動裝置應用程式通知失敗 | 嘗試傳送通知到使用者裝置上的行動裝置應用程式時失敗。 |
| FAILED_PHONE_APP_INVALID_RESULT | 行動裝置應用程式結果無效 | 行動裝置應用程式傳回無效的結果。 |
| FAILED_OATH_CODE_INCORRECT | OATH 代碼不正確 | 使用者輸入不正確的 OATH 代碼。  驗證遭到拒絕。 |
| FAILED_OATH_CODE_PIN_INCORRECT | OATH 代碼 + PIN 不正確 | 使用者輸入的 OATH 代碼及/或使用者 PIN 不正確。  驗證遭到拒絕。 |
| FAILED_OATH_CODE_DUPLICATE | 重複的 OATH 代碼 | 使用者輸入之前使用過的 OATH 代碼。  驗證遭到拒絕。 |
| FAILED_OATH_CODE_OLD | OATH 代碼已過期 | 使用者輸入先前所使用 OATH 代碼之前的 OATH 代碼。  驗證遭到拒絕。 |
| FAILED_OATH_TOKEN_TIMEOUT | OATH 代碼結果逾時 | 使用者花在輸入 OATH 代碼的時間太長，導致 Multi-Factor Authentication 嘗試已逾時。 |
| FAILED_SECURITY_QUESTIONS_TIMEOUT | 安全性問題結果逾時 | 使用者花在輸入安全性問題答案的時間太長，導致 Multi-Factor Authentication 嘗試已逾時。 |
| FAILED_AUTH_RESULT_TIMEOUT | 驗證結果逾時 | 使用者花在完成 Multi-Factor Authentication 嘗試的時間太長。 |
| FAILED_AUTHENTICATION_THROTTLED | 驗證已節流 | 服務已針對 Multi-Factor Authentication 嘗試進行節流處理。 |

## <a name="additional-mfa-reports"></a>其他 MFA 報告

下列為針對 MFA 事件 (包括 MFA 伺服器的事件) 提供的其他資訊和報告：

| Report | Location | 描述 |
|:--- |:--- |:--- |
| 已封鎖的使用者歷程記錄 | Azure AD > 安全性 > MFA > 封鎖/解除封鎖使用者 | 顯示使用者封鎖或解除封鎖要求的歷程記錄。 |
| 內部部署元件的使用方式 | Azure AD > 安全性 > MFA > 活動報告 | 透過 NPS 延伸模組、ADFS 和 MFA 伺服器提供 MFA 伺服器整體使用情況的相關資訊。 |
| 已略過的使用者歷程記錄 | Azure AD > 安全性 > MFA > 單次許可 | 提供針對使用者許可 MFA 的 MFA 伺服器要求歷程記錄。 |
| 伺服器狀態 | Azure AD > 安全性 > MFA > 伺服器狀態 | 顯示與帳戶建立關聯的 MFA 伺服器狀態。 |

## <a name="next-steps"></a>後續步驟

本文提供了登入活動報告概觀。 如需此報告所包含內容的詳細資訊，以及若要了解資料，請參閱 [Azure AD 中的登入活動報告](../reports-monitoring/concept-sign-ins.md)。

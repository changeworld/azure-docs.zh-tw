---
title: Azure AD 中的連續存取評估
description: 使用 Azure AD 中的持續存取評估，更快速地回應使用者狀態的變更
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu
ms.collection: M365-identity-device-management
ms.openlocfilehash: daa3f3b1687b01005f32cbd2665c84b933c993b8
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94837612"
---
# <a name="continuous-access-evaluation"></a>持續存取評估

權杖到期和重新整理是產業中的標準機制。 當用戶端應用程式（如 Outlook）連接到 Exchange Online 之類的服務時，會使用 OAuth 2.0 存取權杖授權 API 要求。 根據預設，這些存取權杖的有效時間為一小時，當它們到期時，用戶端會重新導向回到 Azure AD 重新整理。 該重新整理期間提供重新評估原則以進行使用者存取的機會。 例如：由於條件式存取原則或使用者已在目錄中停用，因此我們可能會選擇不重新整理權杖。 

客戶對於使用者的條件變更（例如網路位置或認證遭竊），以及可以強制執行與該變更相關的原則之間的延遲表示相關考慮。 我們實驗了「最差物件」的方法來降低權杖存留期，但發現它們可能會降低使用者體驗和可靠性，而不會降低風險。

及時回應原則違規或安全性問題，其實需要權杖簽發者（例如 Azure AD）和信賴憑證者（例如 Exchange Online）之間的「交談」。 這種雙向對話提供兩項重要的功能。 信賴憑證者可以注意到專案變更時（例如來自新位置的用戶端），以及告知權杖簽發者。 此外，它也會提供一個方法，告訴信賴憑證者因為帳戶入侵、停用或其他考慮，而停止對指定使用者的權杖。 這項交談的機制是連續存取評估 (CAE) 。 其目標是要以接近即時的方式回應，但在某些情況下，可能會因為事件傳播時間而觀察到最多15分鐘的延遲時間。

連續存取評估的初始實行著重于 Exchange、小組和 SharePoint Online。 

### <a name="key-benefits"></a>主要權益

- 使用者終止或密碼變更/重設：將會以近乎即時的方式強制執行使用者會話撤銷。
- 網路位置變更：將會以近乎即時的方式強制執行條件式存取位置原則。
- 您可以使用條件式存取位置原則來防止將權杖匯出至受信任網路之外的電腦。

## <a name="scenarios"></a>案例 

有兩種案例可進行連續存取評估、重大事件評估以及條件式存取原則評估。

### <a name="critical-event-evaluation"></a>重大事件評估

持續存取評估是藉由啟用服務（例如 Exchange Online、SharePoint Online 和團隊）來執行，以訂閱 Azure AD 中的重大事件，讓您可以近乎即時地評估和強制執行這些事件。 重大事件評估不依賴條件式存取原則，因此可在任何租使用者中使用。 目前已評估下列事件：

- 已刪除或停用使用者帳戶
- 變更或重設使用者的密碼
- 已為使用者啟用多重要素驗證
- 系統管理員明確撤銷使用者的所有重新整理權杖
- Azure AD Identity Protection 偵測到提升的使用者風險

此程式可讓使用者在其中一個重大事件之後，從 Microsoft 365 用戶端應用程式中失去組織 SharePoint Online 檔案、電子郵件、行事曆或工作的存取權，以及小組的存取權。 

### <a name="conditional-access-policy-evaluation-preview"></a>條件式存取原則評估 (預覽) 

Exchange 和 SharePoint 可以同步處理重要的條件式存取原則，以便在服務本身內進行評估。

此程式可讓使用者在網路位置變更後立即失去 Microsoft 365 用戶端應用程式或 SharePoint Online 的組織檔案、電子郵件、行事曆或工作的存取權。

> [!NOTE]
> 並非所有的應用程式和資源提供者組合都受到支援。 請參閱下表。 Office 指的是 Word、Excel 和 PowerPoint

| | Outlook Web | Outlook Win32 | Outlook iOS | Outlook Android | Outlook Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **SharePoint Online** | 支援 | 支援 | 不支援 | 不支援 | 支援 |
| **Exchange Online** | 支援 | 支援 | 支援 | 支援 | 支援 |

| | Office web 應用程式 | Office Win32 應用程式 | IOS 版 Office | Android 版 Office | Mac 版 Office |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **SharePoint Online** | 不支援 | 支援 | 支援 | 支援 | 支援 |
| **Exchange Online** | 不支援 | 支援 | 支援 | 支援 | 支援 |

### <a name="client-side-claim-challenge"></a>用戶端宣告挑戰

在連續存取評估之前，用戶端一律會嘗試從其快取中重新執行存取權杖（只要未過期）。 有了 CAE，我們會引進一個新案例，讓資源提供者可以拒絕權杖（即使未過期）。 為了告知用戶端即使快取的權杖尚未過期，也會略過其快取，我們引進了一種稱為「宣告 **挑戰** 」的機制，以指出權杖已遭到拒絕，且必須由 Azure AD 發行新的存取權杖。 CAE 需要用戶端更新才能瞭解宣告挑戰。 下列應用程式的最新版本支援索取挑戰：

- Outlook 視窗
- Outlook iOS
- Outlook Android
- Outlook Mac
- Outlook Web App
- Windows (團隊資源) 的團隊
- 小組的 iOS (僅適用于小組資源) 
- 小組的 Android (僅適用于小組資源) 
- 小組 Mac (僅適用于小組資源) 
- 適用于 Windows 的 Word/Excel/PowerPoint
- 適用于 iOS 的 Word/Excel/PowerPoint
- 適用于 Android 的 Word/Excel/PowerPoint
- 適用于 Mac 的 Word/Excel/PowerPoint

### <a name="token-lifetime"></a>權杖存留期

由於風險和原則會即時評估，因此協調連續存取評估感知會話的用戶端會依賴 CAE，而不是現有的靜態存取權杖存留期原則，這表示可設定的權杖存留期原則將不再針對可協調 CAE 感知會話的支援 CAE 的用戶端進行。

在 CAE 會話中，權杖存留期會延長為長期存留，最多28小時。 撤銷是由重大事件和原則評估所驅動，而不只是任意時間週期。 這項變更可提高應用程式的穩定性，而不會影響到安全性狀態。 

如果您不是使用支援 CAE 的用戶端，除非您已使用可設定的權杖存留期設定存取權杖存留期，否則您的預設存取權杖存留期將會保留1小時， [ (CTL) ](../develop/active-directory-configurable-token-lifetimes.md) 預覽功能。

## <a name="example-flows"></a>範例流程

### <a name="user-revocation-event-flow"></a>使用者撤銷事件流程：

![使用者撤銷事件流程](./media/concept-continuous-access-evaluation/user-revocation-event-flow.png)

1. 具有 CAE 功能的用戶端會出示認證或重新整理權杖，以 Azure AD 要求某些資源的存取權杖。
1. 存取權杖連同其他成品一起傳回至用戶端。
1. 系統管理員明確地 [撤銷使用者的所有](/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0)重新整理權杖。 撤銷事件將會從 Azure AD 傳送到資源提供者。
1. 存取權杖會呈現給資源提供者。 資源提供者會評估權杖的有效性，並檢查是否有使用者的任何撤銷事件。 資源提供者會使用此資訊來決定是否授與資源的存取權。
1. 在此情況下，資源提供者會拒絕存取，並將 401 + 宣告挑戰傳送回用戶端。
1. 支援 CAE 的用戶端瞭解 401 + 索取挑戰。 它會略過快取並回到步驟1，將其重新整理權杖連同宣告挑戰傳送回 Azure AD。 Azure AD 接著會重新評估所有的條件，並提示使用者在此情況下重新驗證。

### <a name="user-condition-change-flow-preview"></a>使用者條件變更流程 (預覽) ：

在下列範例中，條件式存取系統管理員已設定以位置為基礎的條件式存取原則，只允許來自特定 IP 範圍的存取：

![使用者條件事件流程](./media/concept-continuous-access-evaluation/user-condition-change-flow.png)

1. 具有 CAE 功能的用戶端會出示認證或重新整理權杖，以 Azure AD 要求某些資源的存取權杖。
1. Azure AD 會評估所有條件式存取原則，以查看使用者和用戶端是否符合條件。
1. 存取權杖連同其他成品一起傳回至用戶端。
1. 使用者移出允許的 IP 範圍
1. 用戶端會向允許的 IP 範圍以外的資源提供者呈現存取權杖。
1. 資源提供者會評估權杖的有效性，並檢查從 Azure AD 同步處理的位置原則。
1. 在此情況下，資源提供者會拒絕存取，並將 401 + 索取挑戰傳送回用戶端，因為它不是來自允許的 IP 範圍。
1. 支援 CAE 的用戶端瞭解 401 + 索取挑戰。 它會略過快取並回到步驟1，將其重新整理權杖連同宣告挑戰傳送回 Azure AD。 Azure AD 重新評估所有條件，在此情況下將拒絕存取。

## <a name="enable-or-disable-cae-preview"></a>啟用或停用 CAE (預覽版) 

1. 以條件式存取系統管理員、安全性系統管理員或全域管理員身分登入 **Azure 入口網站**
1. 流覽至 **Azure Active Directory**  >  **安全性**  >  **持續存取評估**。
1. 選擇 [ **啟用預覽**]。

從這個頁面，您可以選擇性地限制將受預覽的使用者和群組。

![在 Azure 入口網站中啟用 CAE 預覽](./media/concept-continuous-access-evaluation/enable-cae-preview.png)

## <a name="troubleshooting"></a>疑難排解

### <a name="supported-location-policies"></a>支援的位置原則

針對 CAE，我們只能深入解析以 IP 為基礎的命名位置。 我們無法深入瞭解其他位置設定，例如 [MFA 信任的 ip](../authentication/howto-mfa-mfasettings.md#trusted-ips) 或國家/地區的位置。 當使用者來自 MFA 信任 IP 或包含 MFA 受信任 IP 或國家/地區位置的受信任位置時，將不會在使用者移至不同的位置之後強制執行 CAE。 在這些情況下，我們將發出1小時的 CAE 權杖，而不需要立即 IP 強制執行檢查。

> [!IMPORTANT]
> 設定持續存取評估的位置時，請只使用以 [IP 為基礎的條件式存取位置條件](../conditional-access/location-condition.md#preview-features) ，並設定您的身分識別提供者和資源提供者可以看到的所有 IP 位址（ **包括 IPv4 和 IPv6**）。 請勿使用 Azure AD Multi-Factor Authentication 的 [服務設定] 頁面中提供的國家/地區位置或信任的 ip 功能。

### <a name="ip-address-configuration"></a>IP 位址組態

您的身分識別提供者和資源提供者可能會看到不同的 IP 位址。 這種不符的原因可能是您組織中的網路 proxy 執行，或您的身分識別提供者與資源提供者之間的 IPv4/IPv6 設定不正確。 例如：

- 您的身分識別提供者會從用戶端看到一個 IP 位址。
- 傳遞 proxy 之後，您的資源提供者會看到用戶端的不同 IP 位址。
- 您的身分識別提供者所看到的 IP 位址是原則中允許的 IP 範圍的一部分，但是資源提供者的 IP 位址不是。

如果此案例存在於您的環境中以避免無限迴圈，Azure AD 將發出一小時 CAE 權杖，且不會強制執行用戶端位置變更。 即使在此情況下，相較于傳統的一小時權杖，安全性也會獲得改善，因為我們仍在評估用戶端位置變更事件 [以外的其他事件](#critical-event-evaluation) 。

### <a name="office-and-web-account-manager-settings"></a>Office 和 Web 帳戶管理員設定

| Office 更新頻道 | DisableADALatopWAMOverride | DisableAADWAM |
| --- | --- | --- |
| 半年企業通道 | 如果設定為 [已啟用] 或1，則不支援 CAE。 | 如果設定為 [已啟用] 或1，則不支援 CAE。 |
| 目前通道 <br> 或 <br> 每月企業通道 | 無論設定為何，都支援 CAE | 無論設定為何，都支援 CAE |

如需 office update 通道的說明，請參閱 [Microsoft 365 應用程式的更新通道總覽](/deployoffice/overview-update-channels)。 建議組織不要停用 Web 帳戶管理員 (WAM) 。

### <a name="policy-change-timing"></a>原則變更時間

由於 Azure AD 與資源提供者之間可能會發生複寫延遲，因此系統管理員所做的原則變更最多可能需要2小時才會生效，以進行 Exchange Online。

範例：系統管理員新增原則來封鎖某範圍的 IP 位址，使其無法在上午11:00 存取電子郵件，而該 IP 範圍之前的使用者可能會繼續存取電子郵件，直到 1:00 PM 為止。

### <a name="coauthoring-in-office-apps"></a>在 Office 應用程式中共同撰寫

當多個使用者同時在同一份檔上共同作業時，CAE 可能不會根據使用者撤銷或原則變更事件來立即撤銷使用者對檔的存取權。 在此情況下，使用者會完全失去存取權、關閉檔、關閉 Word、Excel 或 PowerPoint，或在10小時之後。

若要減少這段時間，SharePoint 系統管理員可以透過 [在 Sharepoint online 中設定網路位置原則](/sharepoint/control-access-based-on-network-location)，選擇性地減少針對儲存在 sharepoint Online 和商務用 OneDrive 中之檔的共同工作會話最長存留期。 變更此設定之後，即會將共同會話的最長存留期縮減為15分鐘，並且可以使用 SharePoint Online PowerShell 命令 "Set-Set-spotenant – IPAddressWACTokenLifetime" 進一步調整。

### <a name="enable-after-a-user-is-disabled"></a>停用使用者之後啟用

如果您在停用之後啟用使用者權限。 在可以啟用帳戶之前，將會有一些延遲。 SPO 和團隊會有15分鐘的延遲。 EXO 的延遲為35-40 分鐘。

## <a name="faqs"></a>常見問題集

### <a name="how-will-cae-work-with-sign-in-frequency"></a>CAE 會如何搭配登入頻率運作？

登入頻率將會接受或不使用 CAE。

## <a name="next-steps"></a>後續步驟

[宣佈持續存取評估](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)
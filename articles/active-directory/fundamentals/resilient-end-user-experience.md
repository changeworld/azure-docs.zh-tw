---
title: 使用 Azure AD B2C 的復原終端使用者體驗 |Microsoft Docs
description: 使用 Azure AD B2C 在終端使用者體驗中建立恢復能力的方法
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 057599cc92e27e423d25d528d5d84b978ff4a911
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919399"
---
# <a name="resilient-end-user-experience"></a>復原的終端使用者體驗

註冊和登入使用者體驗是由下列元素所組成：

- 使用者互動的介面（例如 CSS、HTML 和 JavaScript）

- 您所建立的使用者流程和自訂原則（例如註冊、登入和設定檔編輯）

- 身分識別提供者 (Idp 應用程式) ，例如本機帳戶使用者名稱/密碼、Outlook、Facebook 和 Google

![影像顯示終端使用者體驗元件](media/resilient-end-user-experiences/end-user-experience-architecture.png)

## <a name="choose-between-user-flow-and-custom-policy"></a>在使用者流程和自訂原則之間進行選擇  

為了協助您設定最常見的身分識別工作，Azure AD B2C 提供內建的可設定 [使用者流程](https://docs.microsoft.com/azure/active-directory-b2c/user-flow-overview)。 您也可以建立自己的 [自訂原則](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)，以提供最大的彈性。 不過，建議您只使用自訂原則來處理複雜的案例。

### <a name="how-to-decide-between-user-flow-and-custom-policy"></a>如何在使用者流程和自訂原則之間做決定

如果您的業務需求符合您的需求，請選擇內建的使用者流程。 自從經過 Microsoft 廣泛測試之後，您可以將驗證這些身分識別使用者流程的原則層級功能、效能或規模的測試降至最低。 您仍然需要測試應用程式的功能、效能和規模。

如果您因為商務需求而 [選擇自訂原則](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started) ，請務必針對功能、效能或規模調整執行原則層級的測試，以及應用層級的測試。

請參閱 [比較使用者流程和自訂](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview#comparing-user-flows-and-custom-policies) 原則的文章，以協助您決定。

## <a name="choose-multiple-idps"></a>選擇多個 Idp

使用外部身分 [識別提供者](https://docs.microsoft.com/azure/active-directory-b2c/technical-overview#external-identity-providers) （例如 Facebook）時，請務必在外部提供者變成無法使用時，擁有回溯方案。

### <a name="how-to-set-up-multiple-idps"></a>如何設定多個 Idp

作為外部身分識別提供者註冊程式的一部分，請包含已驗證的身分識別宣告，例如使用者的行動電話號碼或電子郵件地址。 將已驗證的宣告認可到基礎 Azure AD B2C 目錄實例。 如果外部提供者無法使用，請還原為已驗證的身分識別宣告，並切換回作為驗證方法的電話號碼。 另一個選項是將單次密碼傳送給使用者，以允許使用者登入。

 遵循下列步驟來 [建立替代的驗證路徑](https://github.com/azure-ad-b2c/samples/tree/master/policies/idps-filter)：

 1. 設定您的註冊原則，以允許本機帳戶和外部 Idp 註冊。

 2. 設定設定檔原則，以允許使用者在登入後將 [其他身分識別連結到其帳戶](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/account-linking) 。

 3. 通知並允許使用者在中斷期間 [切換至替代 IDP](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-ui-customization#configure-dynamic-custom-page-content-uri) 。

## <a name="availability-of-multi-factor-authentication"></a>多重要素驗證的可用性

使用 [電話語音進行多重要素驗證 (MFA) ](https://docs.microsoft.com/azure/active-directory-b2c/phone-authentication)時，請務必考慮使用替代服務提供者。 當地的電訊或電話服務提供者可能會在服務中遇到中斷。

### <a name="how-to-choose-an-alternate-mfa"></a>如何選擇替代 MFA  

Azure AD B2C 服務使用內建的電話型 MFA 提供者，提供以時間為基礎的一次性密碼 (Otp) 。 它會以語音通話和文字訊息的形式呈現給使用者預先註冊的電話號碼。 下列替代方法適用于各種案例：

使用 **使用者流程** 時，有兩種方法可以建立復原：

- **變更使用者流程** 設定：偵測到以電話為基礎的 OTP 傳遞發生中斷時，請將 OTP 傳遞方法從電話改為以電子郵件為基礎並重新部署使用者流程，讓應用程式保持不變。

![螢幕擷取畫面：顯示登入註冊](media/resilient-end-user-experiences/create-sign-in.png)

- **變更應用程式**：針對每個身分識別工作（例如註冊和登入），定義兩組使用者流程。 設定第一組是使用以電話為基礎的 OTP，第二組則是以電子郵件為基礎的 OTP。 偵測到以電話為基礎的 OTP 傳遞中斷時，請變更並重新部署應用程式，以從第一組使用者流程切換至第二組，讓使用者流程保持不變。  

使用 **自訂原則** 時，有四種方法可建立恢復功能。 以下列出的是複雜性的順序，您將需要重新部署更新的原則。

- **允許使用者選取以電話為基礎的 otp 或以電子郵件為基礎的 otp**：對使用者公開這兩個選項，並讓使用者自行選取其中一個選項。 不需要對原則或應用程式進行變更。

- 在以 **電話為基礎的 otp 與以電子郵件為基礎的 otp 之間動態切換**：在註冊時收集電話和電子郵件資訊。 預先定義自訂原則，以在電話中斷期間有條件地切換，從電話到以電子郵件為基礎的 OTP 傳遞。 不需要對原則或應用程式進行變更。

- **使用驗證器應用程式**：更新自訂原則以使用 [驗證器應用程式](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-mfa-totp)。 如果您的一般 MFA 是以電話為基礎或以電子郵件為基礎的 OTP，則請重新部署自訂原則，以切換為使用驗證器應用程式。

>[!Note]
>使用者必須在註冊期間設定驗證器應用程式整合。

- **使用安全性問題**：如果上述方法都不適用，請將安全性問題做為備份來執行。 在登入或設定檔編輯期間設定使用者的安全性問題，並將答案儲存在目錄以外的另一個資料庫中。 此方法不符合「您所擁有的任何專案」的 MFA 需求（例如電話），但會提供次要「您知道的東西」。

## <a name="use-a-content-delivery-network"></a>使用內容傳遞網路

內容傳遞網路 (Cdn) 效能較佳，而且比 blob 存放區更便宜，可用於儲存自訂的使用者流程 UI。 網頁內容的傳遞速度，會比高可用性伺服器的分散網路更快。  

透過端對端案例和負載測試，定期測試您的 CDN 可用性和內容發佈效能。 如果您正在規劃即將推出的激增（因為促銷或假日流量），請修改您的負載測試估計值。
  
## <a name="next-steps"></a>後續步驟

- [Azure AD B2C 開發人員的復原能力資源](resilience-b2c.md)
  
  - [具有外部進程的復原介面](resilient-external-processes.md)
  - [透過開發人員最佳作法的復原能力](resilience-b2c-developer-best-practices.md)
  - [透過監視及分析的復原能力](resilience-with-monitoring-alerting.md)
- [在您的驗證基礎結構中建立恢復功能](resilience-in-infrastructure.md)
- [提高應用程式中驗證和授權的復原能力](resilience-app-development-overview.md)

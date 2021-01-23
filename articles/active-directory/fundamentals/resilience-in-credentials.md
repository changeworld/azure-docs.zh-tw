---
title: Azure Active Directory 中的認證管理建立恢復功能
description: 建立彈性認證策略的架構設計人員和 IT 系統管理員指南。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 399d2f71fa20d63dce89cf3be5c12ffd63264895
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98724705"
---
# <a name="build-resilience-with-credential-management"></a>使用認證管理建立恢復功能

在權杖要求中出示認證 Azure AD 時，有多個必須可供驗證的相依性。 第一個驗證因素依賴 Azure AD authentication，在某些情況下則是在內部部署基礎結構上。 如需混合式驗證架構的詳細資訊，請參閱 [混合式基礎結構中的組建恢復](resilience-in-hybrid.md)功能。 

如果您執行第二個因素，第二個因素的相依性會新增至第一個因素的相依性。 例如，如果您的第一個因素是透過 PTA，而第二個因素是 SMS，則您的相依性如下：

* Azure AD authentication 服務

* Azure MFA 服務

* 內部部署基礎結構

* 電話貨運公司

* 使用者的裝置 (未) 

 
![驗證方法和相依性的影像](./media/resilience-in-credentials/admin-resilience-credentials.png)

您的認證策略應考慮每種驗證類型的相依性，並布建可避免單一失敗點的方法。 

由於驗證方法具有不同的相依性，因此最好讓使用者盡可能註冊最多的第二個因素選項。 如果可能的話，請務必包含具有不同相依性的第二個因素。 例如，語音通話和 SMS 作為第二個因素會共用相同的相依性，因此將其視為唯一的選項並不會降低風險。

最具彈性的認證策略是使用無密碼 authentication。 Windows Hello 企業版和 FIDO 2.0 安全性金鑰比強式驗證的相依性更少，但有兩個不同的因素。 Microsoft Authenticator 的應用程式、Windows Hello 企業版和 Fido 2.0 安全性金鑰都是最安全的。 

針對第二個因素，使用以時間為基礎之單次密碼的 Microsoft Authenticator 應用程式或其他驗證器應用程式 (TOTP) 或 OATH 硬體權杖的相依性最少，因此更具彈性。

## <a name="how-do-multiple-credentials-help-resilience"></a>多個認證如何協助復原？

布建多個認證類型可提供使用者選項來容納其喜好設定和環境限制。 如此一來，在要求時，系統會提示使用者輸入多重要素驗證的互動式驗證，將可獲得特定相依性無法使用的彈性。 您可以 [優化多重要素驗證的重新驗證提示](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md)。

除了上述的個別使用者復原功能之外，企業應針對大規模中斷（例如造成設定不正確的操作錯誤、自然災害或內部部署 federation service 的全企業資源中斷）規劃應變措施 (特別是用於多重要素驗證) 時。 

## <a name="how-do-i-implement-resilient-credentials"></a>如何? 執行復原認證？

* 部署 [無密碼](../authentication/howto-authentication-passwordless-deployment.md) 認證，例如 Windows Hello 企業版、電話驗證及 FIDO2 安全性金鑰，以減少相依性。

* 以第二個因素的形式部署 [Microsoft Authenticator 應用程式](../user-help/user-help-auth-app-overview.md) 。

* 針對從 Windows Server Active Directory 同步處理的混合式帳戶，開啟 [密碼雜湊同步](../hybrid/whatis-phs.md) 處理。 這個選項可以與同盟服務（例如 AD FS）一起啟用，並且在 federation service 失敗時提供回復。

* [分析多重要素驗證方法的使用方式](/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/) ，以改善使用者的體驗。

* [實行彈性的存取控制策略](../authentication/concept-resilient-controls.md)

## <a name="next-steps"></a>後續步驟
系統管理員和架構設計人員的復原能力資源
 
* [以裝置狀態建立恢復功能](resilience-with-device-states.md)

* [使用連續存取評估來建立恢復功能 (CAE) ](resilience-with-continuous-access-evaluation.md)

* [在外部使用者驗證中建立恢復功能](resilience-b2b-authentication.md)

* [打造混合式驗證的復原能力](resilience-in-hybrid.md)

* [使用應用程式 Proxy 來建立應用程式存取的復原能力](resilience-on-premises-access.md)

開發人員的復原能力資源

* [在您的應用程式中建立 IAM 復原](resilience-app-development-overview.md)

* [在您的 CIAM 系統中打造復原能力](resilience-b2c.md)
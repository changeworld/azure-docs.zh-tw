---
title: Azure Active Directory 報告是什麼？ | Microsoft Docs
description: 提供 Azure Active Directory 報告的一般概觀。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 6141a333-38db-478a-927e-526f1e7614f4
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 09/30/2020
ms.author: markvi
ms.reviewer: sarbar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f9a51c10a4f390e5627bccf35ab5dc74689e9c6
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91566817"
---
# <a name="what-are-azure-active-directory-reports"></a>Azure Active Directory 報告是什麼？

Azure Active Directory (Azure AD) 報告可提供環境中活動的全面性檢視。 提供的資料可讓您：

- 判斷使用者如何利用您的應用程式和服務
- 偵測會影響環境健康情況的潛在風險
- 針對會阻止使用者完成其工作的問題進行疑難排解  

報告架構依賴兩大主要支柱：

- [安全性報告](#security-reports)
- [活動報告](#activity-reports)

![報告](./media/overview-reports/01.png)


## <a name="security-reports"></a>安全性報告

安全性報告可協助您保護貴組織的身分識別。 安全性報告有兩種類型：

- **標幟為有風險的使用者** - 從[有風險的使用者安全性報告](../identity-protection/overview-identity-protection.md)，取得可能受危害之使用者帳戶的概觀。

- **有風險的登入** - 透過[有風險的登入安全性報告](../identity-protection/overview-identity-protection.md)，取得非使用者帳戶合法擁有者的某人嘗試登入的指示器。 

### <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>您需要哪項 Azure AD 授權才能存取安全性報告？  

所有 Azure AD 版本都可提供標幟為有風險的使用者和有風險的登入報告。 不過，報告細微性層級因版本而異： 

- 在 [Azure Active Directory Free 和 Basic 版本] 中，您會取得標幟為有風險的使用者和有風險的登入清單。 

- **Azure Active Directory Premium 1** 版本也可讓您檢查每份報告部分已偵測到的基礎風險偵測，藉此擴充此模型。 

- **Azure Active Directory Premium 2** 版本可提供有關基礎風險偵測的最詳細資訊，也可讓您設定安全性原則，自動回應已設定的風險層級。


## <a name="activity-reports"></a>活動報告

活動報告可協助您了解組織中使用者的行為。 Azure AD 中有兩種活動報告：

- **稽核記錄** - [稽核記錄活動報告](concept-audit-logs.md)可供您存取在租用戶中執行之每個工作的歷程記錄。

- **登入** - 透過[登入活動報告](concept-sign-ins.md)，您可以判斷已執行稽核記錄報告所報告之工作的人員。



> [!VIDEO https://www.youtube.com/embed/ACVpH6C_NL8]




### <a name="audit-logs-report"></a>稽核記錄報告 

[稽核記錄報告](concept-audit-logs.md)會提供系統活動記錄以達到合規性。 此資料可讓您解決常見的案例，例如：

- 我的租用戶中有人已取得系統管理員群組的存取權。 誰提供存取權給他們？ 

- 我想要知道登入特定應用程式的使用者清單，因為我最近將應用程式上架而且想知道其運作情況

- 我想要知道我的租用戶中發生多少次密碼重設


#### <a name="what-azure-ad-license-do-you-need-to-access-the-audit-logs-report"></a>您需要哪項 Azure AD 授權才能存取稽核記錄報告？  

稽核記錄報告可用於您擁有授權的功能。 如果您有特定功能的授權，也可以存取其稽核記錄資訊。 依據[不同類型授權](../fundamentals/active-directory-whatis.md#what-are-the-azure-ad-licenses)的詳細功能比較，可以在 [Azure Active Directory 定價頁面](https://azure.microsoft.com/pricing/details/active-directory/)上看到。 如需詳細資訊，請參閱 [Azure Active Directory 功能和功用](../fundamentals/active-directory-whatis.md#which-features-work-in-azure-ad)。

### <a name="sign-ins-report"></a>登入報告

[登入報告](concept-sign-ins.md)可讓您尋找以下問題的解答，例如：

- 使用者的登入模式為何？
- 一週內有多少使用者登入？
- 這些登入的狀態為何？

#### <a name="what-azure-ad-license-do-you-need-to-access-the-sign-ins-activity-report"></a>您需要哪項 Azure AD 授權才能存取登入活動報告？  

若要存取登入活動報告，租用戶必須有相關聯的 Azure AD Premium 授權。

## <a name="programmatic-access"></a>以程式設計方式存取

除了使用者介面，Azure AD 報告也可讓您透過一組 REST 型 API 而[以程式設計方式存取](concept-reporting-api.md)報告資料。 您可以從各種程式設計語言和工具呼叫這些 API。 

## <a name="next-steps"></a>後續步驟

- [有風險的登入報告](../identity-protection/overview-identity-protection.md)
- [稽核記錄報告](concept-audit-logs.md)
- [登入記錄報告](concept-sign-ins.md)
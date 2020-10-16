---
title: Azure Active Directory 的 SCIM 同步處理
description: 達到此同步處理模式的架構指引
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: be8dbd4d8deccd42d6fcc391eab1e57df7514401
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114118"
---
# <a name="scim-synchronization-with-azure-active-directory"></a>Azure Active Directory 的 SCIM 同步處理

跨網域身分識別管理的系統 (SCIM) 是一種開放式標準通訊協定，可將身分識別網域和 IT 系統之間的使用者身分識別資訊交換自動化。 SCIM 可確保新增至人力資本管理 (HCM) 系統的員工會自動擁有在 Azure Active Directory (Azure AD) 或 Windows Server Active Directory 中建立的帳戶。 系統會在兩個系統之間同步處理使用者屬性和設定檔，並根據使用者狀態或角色變更來更新移除使用者。

SCIM 是兩個端點的標準化定義：/Users 的端點和/Groups 端點。 它會使用常見的 REST 動詞來建立、更新及刪除物件。 它也會針對常見屬性使用預先定義的架構，例如組名、使用者名稱、名字、姓氏和電子郵件。 提供 SCIM 2.0 REST API 的應用程式可以減少或消除使用專屬使用者管理 Api 或產品的難題。 例如，任何符合 SCIM 規範的用戶端都可以將 JSON 物件的 HTTP POST 發出至/Users 端點，以建立新的使用者專案。 符合 SCIM 標準的應用程式可以立即運用既有的用戶端、工具和程式碼，而無須使用略為不同的 API 來執行相同的基本動作。 

## <a name="use-when"></a>使用時機： 

您想要自動將使用者資訊從 HCM 系統布建到 Azure AD 和 Windows Server Active Directory，然後在必要時，將其設為目標系統。 

![架構圖](./media/authentication-patterns/scim-auth.png)


## <a name="components-ofsystem"></a>系統的元件 

* **HCM system**：應用程式和技術，可在整個員工生命週期中，提供可支援及自動化 HR 流程的人力資本管理程式和作法。 

* **Azure AD**布建服務：使用 SCIM 2.0 通訊協定進行自動布建。 此服務會連接到應用程式的 SCIM 端點，並使用 SCIM 使用者物件架構和 REST Api 來自動布建和解除布建使用者和群組。  

* **Azure AD**：用來管理身分識別和其權利之生命週期的使用者存放庫。 

* **目標系統**：具有 SCIM 端點的應用程式或系統，並可搭配 Azure AD 布建來啟用自動布建使用者和群組。  

## <a name="implementscimwith-azure-ad"></a>使用 Azure AD 來執行 SCIM 

* [布建在 Azure AD 中的運作方式 ](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works)

* [在 Azure 入口網站中管理企業應用程式的使用者帳戶布建 ](https://docs.microsoft.com/azure/active-directory/app-provisioning/configure-automatic-user-provisioning-portal)

* [使用 Azure AD 建立 SCIM 端點並設定使用者布建  ](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups)

* [Azure AD 布建服務的 SCIM 2.0 通訊協定相容性](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groupsapplication-provisioning-config-problem-scim-compatibility)


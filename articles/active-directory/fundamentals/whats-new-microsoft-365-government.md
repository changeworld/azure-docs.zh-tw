---
title: Microsoft 365 政府版 Azure AD 有哪些新功能？ | Microsoft Docs
description: 瞭解 Microsoft 365 政府雲端實例中 Azure Active Directory (Azure AD) 的一些變更，這可能會對您造成影響。
services: active-directory
author: eross-msft
manager: daveba
ms.author: lizross
ms.reviewer: sumitp
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0efc4bc8f89b0fbefbba171d80a3f8a1ed5e7f6
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89318925"
---
# <a name="whats-new-for-azure-active-directory-in-microsoft-365-government"></a>Microsoft 365 政府 Azure Active Directory 的新功能

我們在 Microsoft 365 政府雲端實例中進行了 Azure Active Directory (Azure AD) 的變更，適用于使用下列服務的客戶：

- Microsoft Azure Government

- Microsoft 365 政府-GCC High

- Microsoft 365 政府-DoD

本文不適用於 Microsoft 365 政府– GCC 客戶。

## <a name="changes-to-the-initial-domain-name"></a>初始功能變數名稱的變更

在貴組織的 Microsoft 365 政府線上服務初始註冊期間，系統會要求您選擇您組織的功能變數名稱 `<your-domain-name>.onmicrosoft.com` 。 如果您已經有一個具有 .com 尾碼的功能變數名稱，將不會有任何變更。

但是，如果您要註冊新的 Microsoft 365 政府服務，系統會要求您使用尾碼來選擇功能變數名稱 `.us` 。 這就是 `<your-domain-name>.onmicrosoft.us` 。

>[!Note]
>這項變更不適用於由雲端服務提供者 (Csp) 管理的任何客戶。

## <a name="changes-to-portal-access"></a>變更入口網站存取

我們已更新 Microsoft Azure Government、Microsoft 365 政府-GCC High 和 Microsoft 365 政府-DoD 的入口網站端點，如 [端點對應表](#endpoint-mapping)所示。

先前的客戶可以使用全球 Azure (portal.azure.com) 和 Office 365 (portal.office.com) 入口網站來登入。 在這項更新中，客戶現在必須使用特定的 Microsoft Azure Government、Microsoft 365 政府 GCC High 和 Microsoft 365 政府 DoD 入口網站登入。

## <a name="endpoint-mapping"></a>端點對應

下表顯示所有客戶的端點：

| 名稱 | 端點詳細資料 |
|------|------------------|
| 入口網站 |Microsoft Azure Government： https://portal.azure.us<p>Microsoft 365 政府-GCC High： https://portal.office365.us<p>Microsoft 365 政府-DoD： https://portal.apps.mil |
| Azure Active Directory 授權單位端點 | https://login.microsoftonline.us |
| 適用于 Microsoft 365 政府的 Microsoft Graph API-GCC High | https://graph.microsoft.us |
| 適用于 Microsoft 365 政府的 Microsoft Graph API-DoD | https://dod-graph.microsoft.us |
| Azure Government 服務端點 | 如需詳細資訊，請參閱 [Azure Government 開發人員指南](../../azure-government/documentation-government-developer-guide.md) |
| Microsoft 365 政府 GCC High 端點 | 如需詳細資訊，請參閱 [Office 365 美國政府 GCC High 端點](/office365/enterprise/office-365-u-s-government-gcc-high-endpoints) |
| Microsoft 365 政府-DoD | 如需詳細資訊，請參閱 [Office 365 美國政府 DoD 端點](/office365/enterprise/office-365-u-s-government-dod-endpoints) |

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章：

- [什麼是 Azure Government？](../../azure-government/documentation-government-welcome.md)

- [Azure Government AAD 授權單位端點更新](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)

- [美國政府雲端中的 Microsoft Graph 端點](https://developer.microsoft.com/graph/blogs/new-microsoft-graph-endpoints-in-us-government-cloud/)

- [Office 365 美國政府 GCC High 和 DoD](/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc-high-and-dod)
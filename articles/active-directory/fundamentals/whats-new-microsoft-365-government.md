---
title: Microsoft 365 政府版中的 Azure AD 有哪些新功能？ | Microsoft Docs
description: 瞭解 Microsoft 365 政府雲端實例中 Azure Active Directory （Azure AD）的一些變更，這可能會對您造成影響。
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
ms.openlocfilehash: 48a7e5e810e509a97b6010218532f6e7dff0b73f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77425539"
---
# <a name="whats-new-for-azure-active-directory-in-microsoft-365-government"></a>Microsoft 365 政府中的 Azure Active Directory 新功能

我們已對 Microsoft 365 政府雲端實例中的 Azure Active Directory （Azure AD）進行一些變更，這適用于使用下列服務的客戶：

- Microsoft Azure Government

- Microsoft 365 政府-GCC High

- Microsoft 365 政府– DoD

本文不適用於 Microsoft 365 政府– GCC 客戶。

## <a name="changes-to-the-initial-domain-name"></a>初始功能變數名稱的變更

在貴組織初次註冊 Microsoft 365 政府線上服務時，系統會要求您選擇組織的功能變數名稱 `<your-domain-name>.onmicrosoft.com` 。 如果您已經有具有 .com 尾碼的功能變數名稱，將不會變更任何內容。

不過，如果您要註冊新的 Microsoft 365 政府服務，系統會要求您選擇使用尾碼的功能變數名稱 `.us` 。 因此，它會是 `<your-domain-name>.onmicrosoft.us` 。

>[!Note]
>這項變更不適用於雲端服務提供者（Csp）所管理的任何客戶。

## <a name="changes-to-portal-access"></a>入口網站存取的變更

我們已更新 Microsoft Azure Government、Microsoft 365 政府-GCC High 和 Microsoft 365 政府– DoD 的入口網站端點，如[端點對應表](#endpoint-mapping)所示。

先前的客戶可以使用全球 Azure （portal.azure.com）和 Office 365 （portal.office.com）入口網站進行登入。 透過這項更新，客戶現在必須使用特定 Microsoft Azure Government、Microsoft 365 政府-GCC High 和 Microsoft 365 政府 DoD 入口網站登入。

## <a name="endpoint-mapping"></a>端點對應

下表顯示所有客戶的端點：

| Name | 端點詳細資料 |
|------|------------------|
| 入口網站 |Microsoft Azure Government：https://portal.azure.us<p>Microsoft 365 政府– GCC High：https://portal.office365.us<p>Microsoft 365 政府– DoD：https://portal.apps.mil |
| Azure Active Directory 授權單位端點 | https://login.microsoftonline.us |
| 適用于 Microsoft 365 政府的 Microsoft Graph API-GCC High | https://graph.microsoft.us |
| 適用于 Microsoft 365 政府的 Microsoft Graph API-DoD | https://dod-graph.microsoft.us |
| Azure Government 服務端點 | 如需詳細資訊，請參閱[Azure Government 開發人員指南](https://docs.microsoft.com/azure/azure-government/documentation-government-developer-guide) |
| Microsoft 365 政府-GCC 高階端點 | 如需詳細資訊，請參閱[Office 365 美國政府 GCC 高階端點](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-gcc-high-endpoints) |
| Microsoft 365 政府-DoD | 如需詳細資訊，請參閱[Office 365 美國政府 DoD 端點](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) |

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章：

- [什麼是 Azure Government？](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)

- [Azure Government AAD 授權單位端點更新](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)

- [在美國政府雲端中 Microsoft Graph 端點](https://developer.microsoft.com/graph/blogs/new-microsoft-graph-endpoints-in-us-government-cloud/)

- [Office 365 美國政府 GCC 高和 DoD](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc-high-and-dod)

---
title: Microsoft 365 政府中的 Azure AD 有哪些新增功能？ | Microsoft Docs
description: 瞭解 Microsoft 365 政府雲實例中對 Azure 活動目錄 （Azure AD） 的一些更改，這些更改可能會影響您。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425539"
---
# <a name="whats-new-for-azure-active-directory-in-microsoft-365-government"></a>Microsoft 365 政府中的 Azure 活動目錄有哪些新增功能

我們在 Microsoft 365 政府雲實例中對 Azure 活動目錄 （Azure AD） 進行了一些更改，適用于使用以下服務的客戶：

- Microsoft Azure Government

- 微軟 365 政府 – 海灣合作委員會高

- 微軟 365 政府 + 國防部

本文不適用於 Microsoft 365 政府 – GCC 客戶。

## <a name="changes-to-the-initial-domain-name"></a>對初始功能變數名稱的更改

在組織首次註冊 Microsoft 365 政府線上服務期間，系統要求您選擇組織的功能變數名稱`<your-domain-name>.onmicrosoft.com`。 如果您已經有一個帶有 .com 尾碼的功能變數名稱，則不會更改任何內容。

但是，如果您註冊了新的 Microsoft 365 政府服務，系統將要求您使用`.us`尾碼選擇功能變數名稱。 因此，這將是`<your-domain-name>.onmicrosoft.us`。

>[!Note]
>此更改不適用於由雲服務提供者 （CSP） 管理的任何客戶。

## <a name="changes-to-portal-access"></a>對門戶訪問的更改

我們更新了 Microsoft Azure 政府、微軟 365 政府 + GCC 高和 Microsoft 365 政府 + DoD 的門戶終結點，如[端點映射表中](#endpoint-mapping)所示。

以前，客戶可以使用全球 Azure （portal.azure.com） 和 Office 365 （portal.office.com） 門戶登錄。 使用此更新，客戶現在必須使用特定的 Microsoft Azure 政府、微軟 365 政府 - GCC High 和 Microsoft 365 政府 - DoD 門戶登錄。

## <a name="endpoint-mapping"></a>端點對應

下表顯示了所有客戶的終結點：

| 名稱 | 端點詳細資訊 |
|------|------------------|
| 入口網站 |微軟 Azure 政府：https://portal.azure.us<p>微軟 365 政府 – GCC 高：https://portal.office365.us<p>微軟 365 政府 + 國防部：https://portal.apps.mil |
| Azure 活動目錄許可權終結點 | https://login.microsoftonline.us |
| 微軟圖形API為微軟365政府 - GCC高 | https://graph.microsoft.us |
| 微軟圖形 API 為微軟 365 政府 - DoD | https://dod-graph.microsoft.us |
| Azure 政府服務終結點 | 有關詳細資訊，請參閱[Azure 政府開發人員指南](https://docs.microsoft.com/azure/azure-government/documentation-government-developer-guide) |
| 微軟 365 政府 - GCC 高端端點 | 有關詳細資訊，請參閱[Office 365](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-gcc-high-endpoints)美國政府 GCC 高端終結點 |
| 微軟 365 政府 - 國防部 | 有關詳細資訊，請參閱[Office 365](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints)美國政府 DoD 終結點 |

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱這些文章：

- [什麼是 Azure Government？](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)

- [Azure 政府 AAD 授權終結點更新](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)

- [美國政府雲中的微軟圖形終結點](https://developer.microsoft.com/graph/blogs/new-microsoft-graph-endpoints-in-us-government-cloud/)

- [辦公室 365 美國政府海灣合作委員會高和國防部](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc-high-and-dod)

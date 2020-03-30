---
title: 針對 Azure Active Directory 報告 API 中的錯誤進行疑難排解 | Microsoft Docs
description: 為您提供呼叫 Azure Active Directory 報告 API 時所發生錯誤的解決方式。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 0030c5a4-16f0-46f4-ad30-782e7fea7e40
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d1fb4f49e4f9ad41f971d869873200e6180b5cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399286"
---
# <a name="troubleshoot-errors-in-azure-active-directory-reporting-api"></a>針對 Azure Active Directory 報告 API 中的錯誤進行疑難排解

本文列出了在使用 Microsoft Graph API 訪問活動報告時可能會遇到的常見錯誤訊息及其解決方法的步驟。

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>存取 Microsoft Graph V2 端點時發生 500 HTTP 內部伺服器錯誤

我們目前不支援 Microsoft Graph v2 端點 - 請務必使用 Microsoft Graph v1 端點來存取活動記錄。

### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>錯誤：沒有任何 B2C 租用戶，或租用戶沒有進階授權

存取登入報表需要 Azure Active Directory 進階 1 (P1) 授權。 如果您在存取登入時看到這個錯誤訊息，請確定您的租用戶已獲得 Azure AD P1 授權。

### <a name="error-user-is-not-in-the-allowed-roles"></a>錯誤：使用者不是允許的角色 

如果您在嘗試使用 API 存取稽核記錄或登入時，看到這個錯誤訊息，請確定您的帳戶屬於 Azure Active Directory 租用戶中的**安全性讀者**或**報告讀者**角色。 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>錯誤：應用程式遺漏 AAD「讀取目錄資料」權限 

請遵循[存取 Azure Active Directory 報告 API 的必要條件](howto-configure-prerequisites-for-reporting-api.md)中的步驟，以確保應用程式是以正確的權限集執行。 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>錯誤：應用程式缺少 Microsoft 圖形 API"讀取所有稽核記錄資料"許可權

請遵循[存取 Azure Active Directory 報告 API 的必要條件](howto-configure-prerequisites-for-reporting-api.md)中的步驟，以確保應用程式是以正確的權限集執行。 

## <a name="next-steps"></a>後續步驟

[使用審核 API 引用](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit)
[使用登錄活動報表 API 引用](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)

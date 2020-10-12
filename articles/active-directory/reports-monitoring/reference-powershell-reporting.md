---
title: 適用于報告的 Azure AD PowerShell Cmdlet |Microsoft Docs
description: 適用于報告的 Azure AD PowerShell Cmdlet 參考。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 08/07/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4353ed5413f76b13425a59d31bb6108542e3bd23
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89231124"
---
# <a name="azure-ad-powershell-cmdlets-for-reporting"></a>適用於報表的 Azure AD PowerShell Cmdlet

> [!NOTE] 
> 這些 PowerShell Cmdlet 目前只能搭配 [Azure AD Preview](/powershell/module/azuread/?view=azureadps-2.0-preview#directory_auditing) 模組使用。 請注意，預覽模組不建議用於實際執行環境。 

若要安裝公開預覽版本，請使用下列各項。 

```powershell
Install-module AzureADPreview
```

如需如何使用 PowerShell 連接到 Azure AD 的詳細資訊，請參閱 [Azure AD PowerShell For Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)文章。  

使用 Azure Active Directory (Azure AD) 報表，您可以在 (audit 記錄) 和驗證資料 (登入記錄) 的方向，取得有關活動的詳細資料。 雖然使用 MS 圖形 API 可以取得資訊，但現在您可以使用 Azure AD PowerShell Cmdlet 來取得相同的資料，以進行報告。

本文概述要用於 audit 記錄和登入記錄的 PowerShell Cmdlet。

## <a name="audit-logs"></a>稽核記錄

[Audit 記錄](concept-audit-logs.md) 檔可讓您透過記錄來追蹤 Azure AD 內各種功能所完成的所有變更。 Audit 記錄檔的範例包括對 Azure AD 內的任何資源所做的變更，例如新增或移除使用者、應用程式、群組、角色和原則。

您可以使用 ' AzureADAuditDirectoryLogs Cmdlet 取得審核記錄的存取權。


| 狀況                      | PowerShell 命令 |
| :--                           | :--                |
| 應用程式顯示名稱      | Get-AzureADAuditDirectoryLogs-Filter "initiatedBy/app/displayName eq ' Azure AD Cloud Sync '" |
| 類別                      | Get-AzureADAuditDirectoryLogs 篩選 "category eq ' ApplicationManagement '" |
| 活動日期時間            | Get-AzureADAuditDirectoryLogs-Filter "activityDateTime gt 2019-04-18" |
| 以上皆是              | Get-AzureADAuditDirectoryLogs-Filter "initiatedBy/app/displayName eq ' Azure AD Cloud Sync ' 和 category eq ' ApplicationManagement ' 和 activityDateTime gt 2019-04-18"|


下圖顯示此命令的範例。 

![[資料摘要] 按鈕](./media/reference-powershell-reporting/get-azureadauditdirectorylogs.png)



## <a name="sign-in-logs"></a>登入記錄

登 [入](concept-sign-ins.md) 記錄會提供受控應用程式使用方式和使用者登入活動的相關資訊。

您可以使用 ' AzureADAuditSignInLogs Cmdlet 取得登入記錄的存取權。


| 狀況                      | PowerShell 命令 |
| :--                           | :--                |
| 使用者顯示名稱             | Get-AzureADAuditSignInLogs-Filter "userDisplayName eq ' Timothy Perkins '" |
| 建立日期時間              | Get-AzureADAuditSignInLogs 篩選 "createdDateTime gt 2019-04-18T17：30： 00.0 Z" (4/18) 上下午5:30 之後的所有專案 |
| 狀態                        | Get-AzureADAuditSignInLogs-Filter "status/errorCode eq 50105" |
| 應用程式顯示名稱      | Get-AzureADAuditSignInLogs-Filter "appDisplayName eq ' StoreFrontStudio [wsfed enabled] '" |
| 以上皆是              | Get-AzureADAuditSignInLogs-Filter "userDisplayName eq ' Timothy Perkins ' and status/errorCode ne 0 and appDisplayName eq ' StoreFrontStudio [wsfed enabled] '" |


下圖顯示此命令的範例。 

![[資料摘要] 按鈕](./media/reference-powershell-reporting/get-azureadauditsigninlogs.png)



## <a name="next-steps"></a>後續步驟

- [Azure AD 報告概觀](overview-reports.md)。
- [審核記錄報告](concept-audit-logs.md)。 
- [以程式設計方式存取 Azure AD 報告](concept-reporting-api.md)
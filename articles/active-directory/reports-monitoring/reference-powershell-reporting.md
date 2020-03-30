---
title: 用於報告的 Azure AD 電源外殼 Cmdlet |微軟文檔
description: 用於報告的 Azure AD PowerShell Cmdlet 的參考。
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
ms.date: 07/12/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2192c472e00d123780ec6bc5574e7b9fe326258b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75495317"
---
# <a name="azure-ad-powershell-cmdlets-for-reporting"></a>適用於報表的 Azure AD PowerShell Cmdlet

> [!NOTE] 
> 這些電源外殼 Cmdlet 目前僅適用于[Azure AD 預覽](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#directory_auditing)模組。 請注意，不建議使用預覽模組進行生產。 

要安裝公共預覽版本，請使用以下內容。 

```powershell
Install-module AzureADPreview
```
有關如何使用電源shell 連接到 Azure AD 的更多介紹，請參閱圖表的 Azure [AD 電源殼](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)。  

使用 Azure 活動目錄 （Azure AD） 報表，可以獲取有關方向的所有寫入操作（稽核記錄）和身份驗證資料（登錄日誌）活動的詳細資訊。 儘管可以使用 MS Graph API 獲得該資訊，但現在您可以使用 Azure AD PowerShell Cmdlet 來檢索相同的資料進行報告。

本文概述了用於稽核記錄和登錄日誌的 PowerShell Cmdlet。

## <a name="audit-logs"></a>稽核記錄

[稽核記錄](concept-audit-logs.md)通過日誌提供 Azure AD 中各種功能執行的所有更改的可追溯性。 稽核記錄的範例包括對 Azure AD 中任何資源所做的變更，像是新增或移除使用者、應用程式、群組、角色和原則。

您可以使用"獲取-AzureAUDITDirectoryLogLogs Cmdlet"訪問稽核記錄。


| 狀況                      | PowerShell 命令 |
| :--                           | :--                |
| 應用程式顯示名稱      | 獲取 AzureADAuditDirectoryLogLogs - 篩選"啟動由/應用/顯示名稱 eq 'Azure AD 雲同步'" |
| 類別                      | 獲取 AzureADAuditDirectoryLogLog - 篩選"類別 eq '應用程式管理'" |
| 活動日期時間            | 獲取 AzureADAuditDirectoryLogLog - 篩選"活動日期時間 gt 2019-04-18" |
| 以上皆是              | 獲取 AzureADAuditDirectoryLogLog - 篩選"啟動由/應用/顯示名稱 eq 'Azure AD 雲同步'和類別 eq '應用程式管理' 和活動日期時間 gt 2019-04-18"|


下圖顯示了此命令的示例。 

![[資料摘要] 按鈕](./media/reference-powershell-reporting/get-azureadauditdirectorylogs.png)



## <a name="sign-in-logs"></a>登入記錄

[登錄](concept-sign-ins.md)日誌提供有關託管應用程式和使用者登錄活動使用方式的資訊。

您可以使用"獲取-AzureAUDITSignInLogs Cmdlet"訪問登錄日誌。


| 狀況                      | PowerShell 命令 |
| :--                           | :--                |
| 使用者顯示名稱             | 獲取 AzureADAuditSign 在日誌 - 篩選"使用者顯示名稱 eq '蒂莫西·珀金斯'" |
| 創建日期時間              | 獲取 AzureADAuditSignInLogs - 篩檢程式"創建日期時間 gt 2019-04-18T17：30：00.0Z" （自 4 月 18 日下午 5：30 起的所有內容） |
| 狀態                        | 獲取 AzureADAuditSignInLogs - 篩選"狀態/錯誤代碼 eq 50105" |
| 應用程式顯示名稱      | 獲取 AzureADAuditSign 在日誌 - 篩選"應用程式顯示名稱 eq 'StoreFrontStudio [已啟用的"' |
| 以上皆是              | 獲取 AzureADAuditSign 在日誌 - 篩選"使用者顯示名稱 eq 'Timothy Perkins' 和狀態/錯誤代碼 ne 0 和應用程式顯示名稱 eq 'StoreFrontStudio [已啟用的訂閱]" |


下圖顯示了此命令的示例。 

![[資料摘要] 按鈕](./media/reference-powershell-reporting/get-azureadauditsigninlogs.png)



## <a name="next-steps"></a>後續步驟

- [Azure AD 報告概觀](overview-reports.md)。
- [稽核記錄報告](concept-audit-logs.md)。 
- [以程式設計方式存取 Azure AD 報告](concept-reporting-api.md)

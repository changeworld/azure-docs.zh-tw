---
title: 使用 Azure AD 連線時關閉 PTA「不設定」 |微軟文件
description: 本文介紹如何使用 Azure AD 連接"不配置"功能禁用 PTA。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 04/20/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa1046dc64fed3edb6c9d04f76a96f488769ff42
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726796"
---
# <a name="disable-pta-when-using-azure-ad-connect-do-not-configure"></a>使用 Azure AD 連接時禁用 PTA"不配置"

如果使用 Azure AD Connect 的直通身份驗證並將其設置為「不設定」,則可以禁用它。 禁用 PTA 可以使用以下 cmdlet 完成。 

## <a name="prerequisites"></a>Prerequisites
需要下列必要條件：
- 安裝了 PTA 代理的任何視窗電腦。 
- 代理必須位於版本 1.5.1742.0 或更高版本。 
- Azure 全域管理員帳戶,以便運行 PowerShell cmdlet 以禁用 PTA。

>[!NOTE]
> 如果您的代理較舊,則可能沒有完成此操作所需的 cmdlet。 可以從 Azure 門戶獲取新代理,將其安裝在任何視窗電腦上並提供管理員認證。 (安裝代理不會影響雲端的 PTA 狀態)

> [!IMPORTANT]
> 如果使用 Azure 政府雲,則必須使用以下值傳遞環境名稱參數。 
>
>| 環境名稱 | 雲端 |
>| - | - |
>| AzureUSGovernment | US Gov|


## <a name="to-disable-pta"></a>關閉 PTA
在 PowerShell 工作階段中,使用以下內容關閉 PTA:
1. PS C:\程式檔案\微軟 Azure AD 連線身份驗證代理>`Import-Module .\Modules\PassthroughAuthPSModule`
2. `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth` 或 `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth -EnvironmentName <identifier>`
3. `Disable-PassthroughAuthentication  -Feature PassthroughAuth` 或 `Disable-PassthroughAuthentication -Feature PassthroughAuth -EnvironmentName <identifier>`

## <a name="if-you-dont-have-access-to-an-agent"></a>如果您無法存取代理程式

如果沒有代理電腦,則可以使用以下命令安裝代理。

1. 從portal.azure.com下載最新的 Auth 代理。
2. 安裝功能:`.\AADConnectAuthAgentSetup.exe`或`.\AADConnectAuthAgentSetup.exe ENVIRONMENTNAME=<identifier>`


## <a name="next-steps"></a>後續步驟

- [使用 Azure Active Directory 傳遞驗證來進行使用者登入](how-to-connect-pta.md)
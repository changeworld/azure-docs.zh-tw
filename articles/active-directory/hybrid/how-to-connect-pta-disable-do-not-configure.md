---
title: 使用 Azure AD Connect 「請勿設定」時停用 PTA |Microsoft Docs
description: 本文說明如何使用 Azure AD Connect 「請勿設定」功能來停用 PTA。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 04/20/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26112b1e799cbde3145e7137c686b4b336db4bab
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919930"
---
# <a name="disable-pta-when-using-azure-ad-connect"></a>使用 Azure AD Connect 時停用 PTA

如果您搭配 Azure AD Connect 使用傳遞驗證，且您已將它設定為「 **不要** 設定」，則可以將它停用。 

>[!NOTE]
>如果您已啟用 PHS，則停用 PTA 將會導致租使用者回復至 PHS。

您可以使用下列 Cmdlet 來停用 PTA。 

## <a name="prerequisites"></a>必要條件
需要下列必要條件：
- 已安裝 PTA 代理程式的任何 windows 電腦。 
- 代理程式必須是1.5.1742.0 版或更新版本。 
- Azure 全域管理員帳戶，以便執行 PowerShell Cmdlet 以停用 PTA。

>[!NOTE]
> 如果您的代理程式較舊，則可能沒有完成此作業所需的 Cmdlet。 您可以從 Azure 入口網站取得新的代理程式，將它安裝在任何 windows 電腦上，並提供系統管理員認證。  (安裝代理程式不會影響雲端中的 PTA 狀態) 

> [!IMPORTANT]
> 如果您使用 Azure Government 雲端，則必須以下列值傳入 ENVIRONMENTNAME 參數。 
>
>| 環境名稱 | Cloud |
>| - | - |
>| AzureUSGovernment | US Gov|


## <a name="to-disable-pta"></a>停用 PTA
在 PowerShell 會話中，使用下列各項來停用 PTA：
1. PS C:\Program Files\Microsoft Azure AD Connect Authentication 代理程式> `Import-Module .\Modules\PassthroughAuthPSModule`
2. `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth` 或 `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth -EnvironmentName <identifier>`
3. `Disable-PassthroughAuthentication  -Feature PassthroughAuth` 或 `Disable-PassthroughAuthentication -Feature PassthroughAuth -EnvironmentName <identifier>`

## <a name="if-you-dont-have-access-to-an-agent"></a>如果您沒有代理程式的存取權

如果您沒有代理程式機器，您可以使用下列命令來安裝代理程式。

1. 從 portal.azure.com 下載最新的驗證代理程式。
2. 安裝此功能： `.\AADConnectAuthAgentSetup.exe` 或 `.\AADConnectAuthAgentSetup.exe ENVIRONMENTNAME=<identifier>`


## <a name="next-steps"></a>後續步驟

- [使用 Azure Active Directory 傳遞驗證來進行使用者登入](how-to-connect-pta.md)

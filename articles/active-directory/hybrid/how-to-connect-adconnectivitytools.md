---
title: Azure AD Connect：什麼是 ADConnectivityTool PowerShell 模組 | Microsoft Docs
description: 本檔介紹新的 ADConnectivity PowerShell 模組，以及如何使用它來協助進行疑難排解。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 4/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7eed3e06ab42671d9674ad3893a88dfe9817e22
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85360175"
---
# <a name="troubleshoot-azure-ad-connectivity-with-the-adconnectivitytool-powershell-module"></a>使用 ADConnectivityTool PowerShell 模組對 Azure AD 連線進行疑難排解

ADConnectivity 工具是 PowerShell 模組，可在下列其中一種情況使用：

- 當網路連線問題無法成功驗證使用者在 Wizard 中提供的 Active Directory 認證時，就會在安裝期間執行。
- 由從 PowerShell 工作階段呼叫函式的使用者進行後續安裝。

此工具位於：**C:\Program Files\Microsoft Azure Active Directory Connect\Tools\ ADConnectivityTool.psm1** 

## <a name="adconnectivitytool-during-installation"></a>在安裝期間使用 ADConnectivityTool

在 Azure AD Connect 精靈的 [連線您的目錄]**** 頁面上，如果發生網路問題，ADConnectivityTool 會自動使用其函式之一，判斷發生什麼狀況。  下列任一項都可視為網路問題：

- 使用者提供的樹系名稱輸入錯誤，或該樹系並不存在 
- 與使用者所提供樹系相關聯的網域控制站已關閉 UDP 連接埠 389
- 在 [AD 樹系帳戶] 視窗中提供的認證沒有權限，無法擷取與目標樹系相關聯的網域控制站
- 與使用者所提供樹系相關聯的網域控制站已關閉以下任一 TCP 連接埠：53、88 或 389 
- UDP 389 和 TCP 連接埠都已關閉
- 無法為提供的樹系和/或其相關聯的網域控制站解析 DNS

每當發現上述的任何問題，都會在 AADConnect 精靈中顯示相關的錯誤訊息：


![錯誤](media/how-to-connect-adconnectivitytools/error1.png)

例如，當我們嘗試在 [連線您的目錄]**** 畫面上新增目錄時，Azure AD Connect 需要加以驗證，並預期能夠透過連接埠 389 和網域控制站通訊。  如果不行，就會看到上述螢幕擷取畫面所示的錯誤。  

背後實際的情況是，Azure AD Connect 正在呼叫 `Start-NetworkConnectivityDiagnosisTools` 函式。  由於網路連線問題，導致認證驗證失敗時，會呼叫此函式。

最後，每當從精靈呼叫此工具時，都會產生詳細的記錄檔。 記錄檔位於 C:\ProgramData\AADConnect\ADConnectivityTool-中** \<date> - \<time> 。**

## <a name="adconnectivitytools-post-installation"></a>ADConnectivityTools 後續安裝
在安裝 Azure AD Connect 之後，可以使用 ADConnectivityTools PowerShell 模組中的任何函式。  

您可以在 [ADConnectivityTools 參考](reference-connect-adconnectivitytools.md)中找到函式的參考資訊

### <a name="start-connectivityvalidation"></a>Start-ConnectivityValidation

因為在將 ADConnectivityTool.psm1 匯入 PowerShell 之後，此函式**只**能手動呼叫一次，所以我們即將呼叫此函式。 

此函式會執行相同的邏輯，Azure AD Connect 精靈會執行以驗證提供的 AD 認證。  不過，它會提供有關問題和建議解決方案的更詳細說明。 

連線驗證包含下列步驟：
-   取得網域 FQDN (完整網域名稱) 物件
-   驗證使用者是否選取 [建立新的 AD 帳戶]、這些認證是否屬於企業系統管理員群組
-   取得樹系 FQDN 物件
-   確認可觸達至少一個與先前所取得樹系 FQDN 物件相關聯的網域
-   確認樹系功能等級是 Windows Server 2003 或更新版本。

如果這些動作已全部成功執行，使用者就能夠新增目錄。

如果使用者在問題解決 (或完全沒有問題) 之後執行此函式，輸出會指出使用者必須返回 Azure AD Connect 精靈，並再次嘗試插入認證。



## <a name="next-steps"></a>後續步驟
- [Azure AD Connect：帳戶和權限](reference-connect-accounts-permissions.md)
- [快速安裝](how-to-connect-install-express.md)
- [自訂安裝](how-to-connect-install-custom.md)
- [ADConnectivityTools 參考](reference-connect-adconnectivitytools.md)


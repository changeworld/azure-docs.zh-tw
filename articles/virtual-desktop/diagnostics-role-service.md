---
title: Windows 虛擬桌面診斷問題 - Azure
description: 如何使用 Windows 虛擬桌面診斷功能來診斷問題。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 2ead16c655d4790e81931371e67da8106dabf83e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85200540"
---
# <a name="identify-and-diagnose-issues"></a>識別並診斷問題

>[!IMPORTANT]
>此內容適用於具有 Azure Resource Manager Windows 虛擬桌面物件的 2020 年春季更新版。 如果您使用不含 Azure Resource Manager 物件的 Windows 虛擬桌面 2019 年秋季版，請參閱[這篇文章](./virtual-desktop-fall-2019/diagnostics-role-service-2019.md)。
>
> Windows 虛擬桌面 2020 年春季更新版目前為公開預覽狀態。 此預覽版本是在沒有服務等級協定的情況下提供，不建議您將其用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Windows 虛擬桌面提供診斷功能，可讓系統管理員透過單一介面識別問題。 若要深入瞭解 Windows 虛擬桌面的診斷功能，請參閱[針對診斷功能使用 Log Analytics](diagnostics-log-analytics.md)。

因為診斷角色服務本身是 Windows 虛擬桌面的一部分，所以在診斷結果中不會顯示未觸達 Windows 虛擬桌面的連線。 當使用者遇到網路連線問題時，可能是發生了 Windows 虛擬桌面連線問題。

## <a name="common-error-scenarios"></a>常見錯誤案例

錯誤案例分成服務內部以及 Windows 虛擬桌面外部。

* 內部問題：指定客戶無法緩和的案例，而且需要解決為支援問題。 透過[Windows 虛擬桌面技術社區](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)提供意見反應時，請包含問題發生時的相互關聯識別碼和大約時間範圍。
* 外部問題：與客戶可以減輕的案例相關。 這些是在 Windows 虛擬桌面的外部。

下表列出系統管理員可能遇到的常見錯誤。

>[!NOTE]
>這份清單包含最常見的錯誤，並且會定期更新。 為確保您擁有最新的資訊，請務必至少每個月重看一次本文。

## <a name="management-errors"></a>管理錯誤

|錯誤訊息|建議的解決辦法|
|---|---|
|無法建立註冊金鑰 |無法建立註冊權杖。 請嘗試使用較短的到期時間（介於1小時到1個月之間）重新建立。 |
|無法刪除註冊金鑰|無法刪除註冊權杖。 請嘗試再次將它刪除。 如果仍然無法解決問題，請使用 PowerShell 來檢查權杖是否仍然存在。 如果存在，請使用 PowerShell 將它刪除。|
|無法變更工作階段主機清空模式 |無法變更 VM 上的清空模式。 檢查 VM 狀態。 如果 VM 無法使用，則無法變更清空模式。|
|無法中斷使用者會話的連線 |無法中斷使用者與 VM 的連線。 檢查 VM 狀態。 如果 VM 無法使用，使用者會話將無法中斷連線。 如果 VM 可以使用，請檢查使用者會話狀態以查看是否已中斷連線。 |
|無法登出工作階段主機內的所有使用者 |無法將使用者從 VM 登出。 檢查 VM 狀態。 如果無法使用，使用者就無法登出。檢查使用者會話狀態，查看是否已登出。您可以使用 PowerShell 強制登出。 |
|無法從應用程式群組取消指派使用者|無法解除發佈使用者的應用程式群組。 查看 Azure AD 上是否有使用者可用。 檢查使用者是否屬於應用程式群組發佈的使用者群組。 |
|抓取可用的位置時發生錯誤 |檢查 [建立主機集區嚮導] 中使用的 VM 位置。 如果該位置無法使用 [映射]，請在該位置新增影像，或選擇不同的 VM 位置。 |

### <a name="external-connection-error-codes"></a>外部連線錯誤碼

|數字碼|錯誤碼|建議的解決辦法|
|---|---|---|
|-2147467259|ConnectionFailedAdTrustedRelationshipFailure|工作階段主機未正確聯結至 Active Directory。|
|-2146233088|ConnectionFailedUserHasValidSessionButRdshIsUnhealthy|連線失敗，因為無法使用工作階段主機。 檢查工作階段主機的健康情況。|
|-2146233088|ConnectionFailedClientDisconnect|如果您經常看到此錯誤，請確定使用者的電腦已連線到網路。|
|-2146233088|ConnectionFailedNoHealthyRdshAvailable|主機使用者嘗試連線的工作階段健康情況不良。 對虛擬機器進行偵錯。|
|-2146233088|ConnectionFailedUserNotAuthorized|使用者沒有權限可存取已發佈的應用程式或桌面。 此錯誤可能會在系統管理員移除已發佈的資源之後出現。 要求使用者重新整理遠端桌面應用程式中的摘要。|
|2|FileNotFound|使用者嘗試存取的應用程式未正確安裝或設定為不正確的路徑。<br><br>當使用者有作用中的會話時，發佈新的應用程式時，使用者將無法存取此應用程式。 必須先關閉會話並重新啟動，使用者才能存取應用程式。 |
|3|InvalidCredentials|使用者輸入的使用者名稱或密碼不符合任何現有的使用者名稱或密碼。 請檢查登入資訊是否有輸入錯誤，然後再試一次。|
|8|ConnectionBroken|用戶端與閘道或伺服器之間的連線已中斷。 除非是意外發生，否則不需要採取任何動作。|
|14|UnexpectedNetworkDisconnect|網路連線已中斷。 要求使用者再次連線。|
|24|ReverseConnectFailed|主機虛擬機器無法直接看到 RD 閘道。 請確定可以解析閘道 IP 位址。|

## <a name="next-steps"></a>後續步驟

若要深入瞭解 Windows 虛擬桌面內的角色，請參閱 [Windows 虛擬桌面環境](environment-setup.md)。

若要查看 Windows 虛擬桌面可用的 PowerShell Cmdlet 清單，請參閱 [PowerShell 參考資訊](/powershell/windows-virtual-desktop/overview)。

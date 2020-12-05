---
title: Windows 虛擬桌面需要 URL 清單-Azure
description: 您必須解除封鎖的 Url 清單，以確保您的 Windows 虛擬桌面部署如預期運作。
author: Heidilohr
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 70b7042e4006cc59419d0ea6798fe7626a82c086
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2020
ms.locfileid: "96621071"
---
# <a name="required-url-list"></a>必要的 URL 清單

若要部署和使用 Windows 虛擬桌面，您必須解除封鎖某些 Url，讓虛擬機器 (Vm) 可隨時存取這些 Url。 本文列出您必須解除封鎖才能讓 Windows 虛擬桌面正常運作所需的 Url。 

>[!IMPORTANT]
>Windows 虛擬桌面不支援封鎖本文所列 Url 的部署。

## <a name="virtual-machines"></a>虛擬機器

您為 Windows 虛擬桌面建立的 Azure 虛擬機器必須能夠存取 Azure 商業雲端中的下列 Url：

|位址|傳出 TCP 連接埠|目的|服務標記|
|---|---|---|---|
|*.wvd.microsoft.com|443|服務流量|WindowsVirtualDesktop|
|gcs.prod.monitoring.core.windows.net|443|代理程式流量|AzureCloud|
|production.diagnostics.monitoring.core.windows.net|443|代理程式流量|AzureCloud|
|* xt.blob.core.windows.net|443|代理程式流量|AzureCloud|
|* eh.servicebus.windows.net|443|代理程式流量|AzureCloud|
|* xt.table.core.windows.net|443|代理程式流量|AzureCloud|
|catalogartifact.azureedge.net|443|Azure Marketplace|AzureCloud|
|kms.core.windows.net|1688|Windows 啟用|Internet|
|mrsglobalsteus2prod.blob.core.windows.net|443|代理程式和 SXS 堆疊更新|AzureCloud|
|wvdportalstorageblob.blob.core.windows.net|443|Azure 入口網站支援|AzureCloud|
| 169.254.169.254 | 80 | [Azure 實例中繼資料服務端點](../virtual-machines/windows/instance-metadata-service.md) | N/A |
| 168.63.129.16 | 80 | [工作階段主機健全狀況監視](../virtual-network/network-security-groups-overview.md#azure-platform-considerations) | N/A |

>[!IMPORTANT]
>Windows 虛擬桌面現在支援 FQDN 標記。 如需詳細資訊，請參閱[使用 Azure 防火牆來保護 Windows 虛擬桌面部署](../firewall/protect-windows-virtual-desktop.md)。
>
>建議您使用 FQDN 標籤，而不要使用 URL，以避免產生服務問題。 列出的 URL 和標籤僅對應於 Windows 虛擬桌面網站和資源。 其中不包含其他服務 (例如 Azure Active Directory) 的 URL。

您為 Windows 虛擬桌面建立的 Azure 虛擬機器必須能夠存取 Azure Government 雲端中的下列 Url：

|位址|傳出 TCP 連接埠|目的|服務標記|
|---|---|---|---|
|*. wvd.microsoft.us|443|服務流量|WindowsVirtualDesktop|
|gcs.monitoring.core.usgovcloudapi.net|443|代理程式流量|AzureCloud|
|monitoring.core.usgovcloudapi.net|443|代理程式流量|AzureCloud|
|fairfax.warmpath.usgovcloudapi.net|443|代理程式流量|AzureCloud|
|* xt.blob.core.usgovcloudapi.net|443|代理程式流量|AzureCloud|
|*.servicebus.usgovcloudapi.net|443|代理程式流量|AzureCloud|
|* xt.table.core.usgovcloudapi.net|443|代理程式流量|AzureCloud|
|Kms.core.usgovcloudapi.net|1688|Windows 啟用|Internet|
|mrsglobalstugviffx.blob.core.usgovcloudapi.net|443|代理程式和 SXS 堆疊更新|AzureCloud|
|wvdportalstorageblob.blob.core.usgovcloudapi.net|443|Azure 入口網站支援|AzureCloud|
| 169.254.169.254 | 80 | [Azure 實例中繼資料服務端點](../virtual-machines/windows/instance-metadata-service.md) | N/A |
| 168.63.129.16 | 80 | [工作階段主機健全狀況監視](../virtual-network/network-security-groups-overview.md#azure-platform-considerations) | N/A |

下表列出您的 Azure 虛擬機器可存取的選用 URL：

|位址|傳出 TCP 連接埠|目的|Azure Gov|
|---|---|---|---|
|*.microsoftonline.com|443|向 Microsoft Online Services 進行驗證|login.microsoftonline.us|
|*.events.data.microsoft.com|443|遙測服務|None|
|www.msftconnecttest.com|443|偵測 OS 是否已連線到網際網路|None|
|*.prod.do.dsp.mp.microsoft.com|443|Windows Update|None|
|login.windows.net|443|登入 Microsoft Online Services、Microsoft 365|login.microsoftonline.us|
|*.sfx.ms|443|OneDrive 用戶端軟體的更新|oneclient.sfx.ms|
|*.digicert.com|443|憑證撤銷檢查|None|
|*. azure-dns.com|443|Azure DNS 解析|None|
|*. azure-dns.net|443|Azure DNS 解析|None|

>[!NOTE]
>Windows 虛擬桌面目前沒有 IP 位址範圍的清單，您可以將其解除封鎖以允許網路流量。 我們目前僅支援解除封鎖特定的 Url。
>
>如果您使用新一代防火牆 (NGFW) ，您必須使用專為 Azure Ip 所建立的動態清單，以確保您可以連線。
>
>如需安全的 Office 相關 Url 清單，包括必要的 Azure Active Directory 相關 Url，請參閱 [office 365 url 和 IP 位址範圍](/office365/enterprise/urls-and-ip-address-ranges)。
>
>對於涉及服務流量的 URL，必須使用萬用字元 (*)。 如果您不想使用 * 來表示代理程式相關流量，下列方法可讓您不使用萬用字元來尋找 URL：
>
>1. 向 Windows 虛擬桌面主機集區註冊您的虛擬機器。
>2. 開啟 [**事件檢視器]**，然後移至 [ **Windows 記錄**  >  **應用程式**  >  **WVD-代理** 程式]，然後尋找事件識別碼3701。
>3. 將您在事件識別碼3701下找到的 Url 解除封鎖。 事件識別碼3701下的 Url 是區域特定的。 您必須針對想要在其中部署虛擬機器的每個區域，使用相關的 Url 重複解除封鎖程式。

## <a name="remote-desktop-clients"></a>遠端桌面用戶端

您使用的任何遠端桌面用戶端都必須具有下列 Url 的存取權：

|位址|傳出 TCP 連接埠|目的|用戶端|Azure Gov|
|---|---|---|---|---|
|*.wvd.microsoft.com|443|服務流量|全部|*. wvd.microsoft.us|
|*.servicebus.windows.net|443|疑難排解資料|全部|*.servicebus.usgovcloudapi.net|
|go.microsoft.com|443|Microsoft FWLinks|全部|None|
|aka.ms|443|Microsoft URL 縮短器|全部|None|
|docs.microsoft.com|443|文件|全部|None|
|privacy.microsoft.com|443|隱私權聲明|全部|None|
|query.prod.cms.rt.microsoft.com|443|用戶端更新|Windows 桌面|None|

>[!IMPORTANT]
>開啟這些 URL 是擁有可靠用戶端體驗所必需的。 不支援封鎖這些 URL 的存取，而且會影響服務功能。
>
>這些 Url 僅對應至用戶端網站與資源。 這份清單不包含其他服務（例如 Azure Active Directory）的 Url。 您可以在 [Office 365 url 和 IP 位址範圍](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online)的識別碼56下找到 Azure Active Directory 的 url。
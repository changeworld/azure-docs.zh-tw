---
title: 什麼是 Windows 虛擬桌面？ - Azure
description: Windows 虛擬桌面的概觀。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 05/07/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 289cc463732ee6b612b67f6c408d9d7260016137
ms.sourcegitcommit: 398fecceba133d90aa8f6f1f2af58899f613d1e3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/21/2020
ms.locfileid: "85125799"
---
# <a name="what-is-windows-virtual-desktop"></a>什麼是 Windows 虛擬桌面？ 

Windows 虛擬桌面是可以在雲端執行的桌面與應用程式虛擬化服務。

以下是您在 Azure 上執行 Windows 虛擬桌面時可以執行的作業：

* 設定多工作階段的 Windows 10 部署，以提供具有延展性的完整 Windows 10
* 將 Microsoft 365 Apps 企業版虛擬化，並將其最佳化以在多使用者的虛擬案例中執行
* 提供具有免費擴充安全性更新的 Windows 7 虛擬桌面
* 將您現有的遠端桌面服務 (RDS) 和 Windows Server 桌面和應用程式帶到任何電腦
* 將桌面和應用程式虛擬化
* 透過統一的管理體驗管理 Windows 10、Windows Server 和 Windows 7 桌面和應用程式

## <a name="introductory-video"></a>簡介影片

了解 Windows 虛擬桌面、其獨特性，以及這部影片中的新增內容：

<br></br><iframe src="https://www.youtube.com/embed/NQFtI3JLtaU" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>

如需更多有關於 Windows 虛擬桌面的影片，請參閱[我們的播放清單](https://www.youtube.com/watch?v=NQFtI3JLtaU&list=PLXtHYVsvn_b8KAKw44YUpghpD6lg-EHev)。

## <a name="key-capabilities"></a>主要功能

透過 Windows 虛擬桌面，您可以設定可調整且具有彈性的環境：

* 在您的 Azure 訂用帳戶中建立完整桌面虛擬化環境，而不必執行任何額外的閘道伺服器。
* 發佈您所需數量的主機集區，以因應您的各種工作負載。
* 自備用於生產工作負載的映像，或從 Azure 資源庫進行測試。
* 利用集區式、多重工作階段的資源降低成本。 使用 Windows 伺服器上 Windows 虛擬桌面和遠端桌面工作階段主機 (RDSH) 角色專用的新 Windows 10 企業版多重工作階段功能，可以大幅降低虛擬機器數目和作業系統 (OS) 額外負荷，同時仍為使用者提供相同的資源。
* 透過個人 (持續性) 桌面提供個別擁有權。

您可以部署及管理虛擬桌面：

* 使用 Windows 虛擬桌面 PowerShell 和 REST 介面，設定主機集區、建立應用程式群組、指派使用者，以及發佈資源。
* 從單一主機集區發佈完整桌面或個別遠端應用程式、為不同的使用者集合建立個別的應用程式群組，或甚至將使用者指派給多個應用程式群組，以減少映像數目。
* 如同您管理您的環境，使用內建委派的存取權來指派角色和收集診斷，以了解各種組態或使用者錯誤。
* 使用新的診斷服務來進行錯誤疑難排解。
* 只要管理映像和虛擬機器，而不管理基礎結構。 您不需要像處理遠端桌面服務一樣，親自管理遠端桌面角色，只要管理您 Azure 訂用帳戶中的虛擬機器。

您也可以指派使用者並將其連線到您的虛擬桌面：

* 指派之後，使用者即可啟動任何 Windows 虛擬桌面用戶端，以將使用者連線到其發佈的 Windows 桌面和應用程式。 從任何裝置透過裝置上的原生應用程式或 Windows 虛擬桌面 HTML5 web 用戶端進行連線。
* 透過服務反向連線安全地建立使用者，因此您永遠不必讓任何輸入連接埠保持開啟。

## <a name="requirements"></a>需求

您需要進行 Windows 虛擬桌面的一些設定，才能讓使用者成功連線到其 Windows 桌面和應用程式。

我們計畫為下列作業系統新增支援，因此，針對您打算部署的桌面和應用程式，請確定您的使用者有[適當的授權](https://azure.microsoft.com/pricing/details/virtual-desktop/)：

|OS|必要授權|
|---|---|
|Windows 10 企業版的多重工作階段或 Windows 10 企業版|Microsoft 365 E3、E5、A3、A5、F3、Business、Premium<br>Windows E3、E5、A3、A5|
|Windows 7 企業版 |Microsoft 365 E3、E5、A3、A5、F3、Business、Premium<br>Windows E3、E5、A3、A5|
|Windows Server 2012 R2、2016、2019|具有軟體保證的 RDS 用戶端存取使用權 (CAL)|

您的基礎結構需要下列項目才能支援 Windows 虛擬桌面：

* [Azure Active Directory](/azure/active-directory/)
* 與 Azure Active Directory 同步的 Windows Server Active Directory。 您可以使用下列其中一項來進行設定：
  * Azure AD Connect (適用於混合式組織)
  * Azure AD Domain Services (適用於混合式雲端組織)
* Azure 訂用帳戶，含有包含或已連線到 Windows Server Active Directory 的虛擬網路
  
您為 Windows 虛擬桌面建立的 Azure 虛擬機器必須：

* [已加入標準網域](../active-directory-domain-services/active-directory-ds-comparison.md)或[已加入混合式 AD](../active-directory/devices/hybrid-azuread-join-plan.md)。 虛擬機器無法加入 Azure AD。
* 執行下列其中一個[支援的 OS 映像](#supported-virtual-machine-os-images)。

>[!NOTE]
>如果需要 Azure 訂用帳戶，您可以[註冊一個月的免費試用](https://azure.microsoft.com/free/)。 如果您使用 Azure 免費試用版，您應該使用 Azure AD Domain Services 讓您的 Windows Server Active Directory 與 Azure Active Directory 保持同步。

您為 Windows 虛擬桌面建立的 Azure 虛擬機器必須具有下列 URL 的存取權：

|位址|傳出 TCP 連接埠|目的|服務標記|
|---|---|---|---|
|*.wvd.microsoft.com|443|服務流量|WindowsVirtualDesktop|
|mrsglobalsteus2prod.blob.core.windows.net|443|代理程式和 SXS 堆疊更新|AzureCloud|
|*.core.windows.net|443|代理程式流量|AzureCloud|
|*.servicebus.windows.net|443|代理程式流量|AzureCloud|
|prod.warmpath.msftcloudes.com|443|代理程式流量|AzureCloud|
|catalogartifact.azureedge.net|443|Azure Marketplace|AzureCloud|
|kms.core.windows.net|1688|Windows 啟用|Internet|
|wvdportalstorageblob.blob.core.windows.net|443|Azure 入口網站支援|AzureCloud|

>[!IMPORTANT]
>Windows 虛擬桌面現在支援 FQDN 標記。 如需詳細資訊，請參閱[使用 Azure 防火牆來保護 Windows 虛擬桌面部署](../firewall/protect-windows-virtual-desktop.md)。
>
>建議您使用 FQDN 標籤，而不要使用 URL，以避免產生服務問題。 列出的 URL 和標籤僅對應於 Windows 虛擬桌面網站和資源。 其中不包含其他服務 (例如 Azure Active Directory) 的 URL。

下表列出您的 Azure 虛擬機器可存取的選用 URL：

|位址|傳出 TCP 連接埠|目的|服務標記|
|---|---|---|---|
|*.microsoftonline.com|443|向 Microsoft Online Services 進行驗證|None|
|*.events.data.microsoft.com|443|遙測服務|None|
|www.msftconnecttest.com|443|偵測 OS 是否已連線到網際網路|None|
|*.prod.do.dsp.mp.microsoft.com|443|Windows Update|None|
|login.windows.net|443|登入 Microsoft Online Services、Microsoft 365|None|
|*.sfx.ms|443|OneDrive 用戶端軟體的更新|None|
|*.digicert.com|443|憑證撤銷檢查|None|


>[!NOTE]
>Windows 虛擬桌面目前沒有 IP 位址範圍清單可供您列入允許網路流量的白名單。 我們目前僅支援將特定 URL 列入白名單。
>
>如需 Office 相關 URL 的清單，包括必要的 Azure Active Directory 相關 URL，請參閱 [Office 365 URL 和 IP 位址範圍](/office365/enterprise/urls-and-ip-address-ranges)。
>
>對於涉及服務流量的 URL，必須使用萬用字元 (*)。 如果您不想使用 * 來表示代理程式相關流量，下列方法可讓您不使用萬用字元來尋找 URL：
>
>1. 向 Windows 虛擬桌面主機集區註冊您的虛擬機器。
>2. 開啟 [事件檢視器]，並瀏覽至 [Windows 記錄] > [應用程式] > [WVD-Agent]，然後尋找事件識別碼 3702。
>3. 將您在事件識別碼 3702 下找到的 URL 列入白名單中。 事件識別碼 3702 下的 URL 會隨區域而不同。 您必須針對要在其中部署虛擬機器的每個區域，重複執行相關 URL 的白名單程序。

Windows 虛擬桌面包含您交付給使用者的 Windows 桌面與應用程式，以及由 Microsoft 在 Azure 上裝載為服務的管理解決方案。 桌面和應用程式都可以部署在任何 Azure 區域的虛擬機器 (VM) 中，而這些 VM 的管理解決方案和資料都會位於美國。 這可能會導致資料轉送到美國。

為了達到最佳效能，請確定您的網路符合下列需求：

* 從用戶端的網路到主機集區部署所在 Azure 區域的來回行程 (RTT) 延遲應少於 150 毫秒。
* 當裝載桌面和應用程式的 VM 連線至管理服務時，網路流量可能會送到國家/地區以外。
* 若要將網路效能最佳化，我們建議讓工作階段主機的 VM 共置在與管理服務相同的 Azure 區域中。

## <a name="supported-remote-desktop-clients"></a>支援的遠端桌面用戶端

下列遠端桌面用戶端支援 Windows 虛擬桌面：

* [Windows 桌面](connect-windows-7-and-10.md)
* [Web](connect-web.md)
* [macOS](connect-macos.md)
* [iOS](connect-ios.md)
* [Android (預覽)](connect-android.md)

> [!IMPORTANT]
> Windows 虛擬桌面不支援 RemoteApp 和桌面連線 (RADC) 用戶端或遠端桌面連線 (MSTSC) 用戶端。

> [!IMPORTANT]
> Windows 虛擬桌面目前不支援來自 Windows Store 的遠端桌面用戶端。 未來版本將增加此用戶端的支援。

遠端桌面用戶端必須能夠存取下列 URL：

|位址|傳出 TCP 連接埠|目的|用戶端|
|---|---|---|---|
|*.wvd.microsoft.com|443|服務流量|全部|
|*.servicebus.windows.net|443|疑難排解資料|全部|
|go.microsoft.com|443|Microsoft FWLinks|全部|
|aka.ms|443|Microsoft URL 縮短器|全部|
|docs.microsoft.com|443|文件|全部|
|privacy.microsoft.com|443|隱私權聲明|全部|
|query.prod.cms.rt.microsoft.com|443|用戶端更新|Windows 桌面|

>[!IMPORTANT]
>開啟這些 URL 是擁有可靠用戶端體驗所必需的。 不支援封鎖這些 URL 的存取，而且會影響服務功能。 這些 URL 僅對應於用戶端網站和資源，並不包含其他服務 (例如 Azure Active Directory) 的 URL。

## <a name="supported-virtual-machine-os-images"></a>支援的虛擬機器 OS 映像

Windows 虛擬桌面支援下列 x64 作業系統映像：

* Windows 10 企業版的多重工作階段，1809 版或更新版本
* Windows 10 企業版，1809 版或更新版本
* Windows 7 企業版
* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2

Windows 虛擬桌面不支援 x86 (32 位元)、Windows 10 Enterprise N 或 Windows 10 Enterprise KN 作業系統映像。 由於磁區大小的限制，Windows 7 也不支援裝載於受控 Azure 儲存體上的任何 VHD 或 VHDX 型設定檔解決方案。

可用的自動化和部署選項取決於您所選的作業系統和版本，如下表所示： 

|作業系統|Azure 映像庫|手動 VM 部署|Azure Resource Manager 範本整合|在 Azure Marketplace 上佈建主機集區|
|--------------------------------------|:------:|:------:|:------:|:------:|
|Windows 10 多工作階段，版本1903|是|是|是|是|
|Windows 10 多工作階段，版本1809|是|是|否|否|
|Windows 10 企業版，版本1903|是|是|是|是|
|Windows 10 企業版，版本1809|是|是|否|否|
|Windows 7 企業版|是|是|否|否|
|Windows Server 2019|是|是|否|否|
|Windows Server 2016|是|是|是|是|
|Windows Server 2012 R2|是|是|否|否|

## <a name="next-steps"></a>後續步驟

如果您使用 Windows 虛擬桌面 2019 年秋季版，您可以從[在 Windows 虛擬桌面中建立租用戶](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)的教學課程開始著手。

如果您使用的是 Windows 虛擬桌面 2020 年春季版，您必須改為建立主機集區。 請前往下列教學課程以開始著手。

> [!div class="nextstepaction"]
> [透過 Azure 入口網站建立主機集區](create-host-pools-azure-marketplace.md)

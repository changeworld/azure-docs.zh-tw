---
title: Microsoft Defender 進階威脅防護-Azure 資訊安全中心
description: 本檔介紹 Azure 資訊安全中心與 Microsoft Defender 進階威脅防護之間的整合。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2020
ms.author: memildin
ms.openlocfilehash: 28242341ddd21adea33e56c3e1f35f0677e5921a
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90907367"
---
# <a name="microsoft-defender-advanced-threat-protection-with-azure-security-center"></a>使用 Azure 資訊安全中心 Microsoft Defender 進階威脅防護

Azure 資訊安全中心與 [Microsoft Defender 進階威脅防護 (ATP) ](https://www.microsoft.com/microsoft-365/windows/microsoft-defender-atp) 整合，以提供完整的端點偵測和回應 (EDR) 功能。


## <a name="availability"></a>可用性

|層面|詳細資料|
|----|:----|
|釋放狀態：|正式上市 (GA)|
|定價：|需要 [Azure Defender](security-center-pricing.md)|
|支援的電腦：|![Yes](./media/icons/yes-icon.png) 執行 Windows 的 Azure 機器<br>![Yes](./media/icons/yes-icon.png) 執行 Windows 的 Azure Arc 電腦|
|必要的角色和許可權：|若要啟用/停用整合：**安全性系統管理員**或**擁有**者<br>若要在安全性中心內查看 MDATP 警示： **安全性讀取者**、 **讀者**、 **資源群組參與者**、 **資源群組擁有**者、 **安全性系統管理員**、訂用帳戶 **擁有**者或訂用帳戶 **參與者**|
|雲端：|![Yes](./media/icons/yes-icon.png) 商業雲端。<br>![No](./media/icons/no-icon.png) 在公用 Azure 雲端中執行工作負載的 GCC 客戶<br>![Yes](./media/icons/yes-icon.png) US Gov<br>![No](./media/icons/no-icon.png) 中國 Gov，其他 Gov|
|||


## <a name="microsoft-defender-atp-features-in-security-center"></a>安全性中心的 Microsoft Defender ATP 功能

Microsoft Defender ATP 提供：

- **Advanced 入侵後偵測感應器**：適用于 Windows 伺服器的 MICROSOFT Defender ATP 感應器會收集大量的行為信號。

- 以**分析為基礎、以雲端為基礎的 post 缺口偵測**： MICROSOFT Defender ATP 可快速適應不斷變化的威脅。 並使用進階分析與巨量資料。 Microsoft Defender ATP 利用 Intelligent Security Graph 的強大功能，在 Windows、Azure 和 Office 之間的信號偵測到未知的威脅。 它提供採取行動警示，讓您得以快速回應。

- **威脅情報**：當 MICROSOFT Defender ATP 識別攻擊者的工具、技術和程式時，會產生警示。 它會使用由 Microsoft 威脅獵人和安全小組提供的資料，利用合作夥伴提供的情報變得更強大。


藉由整合 Defender ATP 與 Azure 資訊安全中心，您也可以從下列額外功能獲益：

- **自動**上線：整合會自動啟用受 Azure 資訊安全中心 (監視之 windows 伺服器的 MICROSOFT Defender ATP 感應器，除非它們執行的是 windows Server 2019) 。

- **單一窗格**： Azure 資訊安全中心主控台會顯示 MICROSOFT Defender ATP 警示。 若要進一步調查，請使用 Microsoft Defender ATP。 Microsoft Defender ATP 提供其他資訊，例如警示程式樹狀結構和事件圖形。 您也可以查看詳細的機器時間軸，其中會顯示最多六個月歷程記錄期間的每項行為。

    ![具有警示詳細資訊的 Microsoft Defender ATP 頁面](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>平台支援

資訊安全中心的 Microsoft Defender ATP 支援 Windows Server 2016、2012 R2 和 2008 R2 SP1 的偵測。 針對 Azure Vm，您需要在您的訂用帳戶上啟用 Azure Defender，而針對非 Azure Vm，您只需要在工作區層級啟用 Azure Defender。

Office 365 GCC 客戶已停用使用此整合的伺服器端點監視。

## <a name="data-storage-location"></a>資料儲存位置

當您使用 Azure 資訊安全中心來監視伺服器時，系統會自動建立 Microsoft Defender ATP 租使用者。 Microsoft Defender ATP 所收集的資料會儲存在租使用者的地理位置中，如布建期間所識別。 匿名化表單中的客戶資料也可以儲存在美國中部儲存和處理系統中。 

設定好之後，您便無法變更資料的儲存位置。 如果您需要將資料移到另一個位置，請聯絡 Microsoft 支援服務，以重設租使用者。


## <a name="onboard-servers-to-security-center"></a>將伺服器上架到安全性中心 

若要將伺服器上架到「安全性中心」，請按一下 [ **移至 Azure 資訊安全中心** ]，將伺服器從 MICROSOFT Defender ATP 伺服器上線上架。

1. **在 [上線**] 區域中，選取或建立用來儲存資料的工作區。

2. 如果您看不到所有的工作區，可能是因為許可權不足，請確定您的工作區受到 Azure Defender 的保護。
    
3. 選取 [ **新增伺服器** ]，以查看如何安裝 Log Analytics 代理程式的指示。 

4. 上架之後，您可以監視 [資產清查](asset-inventory.md)中的機器。

   ![上線的電腦](media/security-center-wdatp/onboard-computers.png)

## <a name="enable-microsoft-defender-atp-integration"></a>啟用 Microsoft Defender ATP 整合

若要查看是否已啟用 Microsoft Defender ATP 整合，請選取 [**安全中心**  >  **定價 & 設定**] > 選取您的訂用帳戶。

您可以在這裡查看目前已啟用的整合內容。

  ![已啟用 Microsoft Defender ATP 整合的 Azure 資訊安全中心威脅偵測設定頁面](media/security-center-wdatp/enable-integrations.png)

- 如果您已啟用 Azure Defender，則不需要採取任何進一步的動作。 Azure 資訊安全中心會自動將伺服器上架到 Microsoft Defender ATP。 上架最多可能需要24小時的時間。

- 如果您從未上線要 Azure 資訊安全中心的伺服器，請將其上架至 Azure 資訊安全中心，並照常啟用 Azure Defender。

- 如果您已透過 Microsoft Defender ATP 上線伺服器：
  - 參閱文件，了解[如何下架伺服器機器](https://go.microsoft.com/fwlink/p/?linkid=852906)的指南。
  - 將伺服器上架到 Azure 資訊安全中心。

## <a name="access-to-the-microsoft-defender-atp-portal"></a>存取 Microsoft Defender ATP 入口網站

1. 請依照指示[將使用者存取權指派到入口網站](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access)。

1. 檢查您是否擁有封鎖匿名流量的 proxy 或防火牆。 Defender ATP 感應器會從系統內容連接，因此必須允許匿名流量。 若要確保不受妨礙存取 Microsoft Defender ATP 入口網站，請遵循在 [proxy 伺服器中啟用存取 Microsoft DEFENDER atp 服務 url](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server)的指示。

## <a name="test-the-feature"></a>測試功能

若要產生良性 Microsoft Defender ATP 測試警示：

1. 建立資料夾 ' C:\test-MDATP-test '。

1. 您可以使用遠端桌面來存取 Windows Server 2012 R2 VM 或 Windows Server 2016 VM。 開啟命令列視窗。

1. 在提示中，複製並執行下列命令： 命令提示字元視窗將會自動關閉。

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```

   ![內含上述命令的命令提示字元視窗](media/security-center-wdatp/image4.jpeg)

1. 如果命令成功，您會在 Azure 資訊安全中心儀表板和 Microsoft Defender ATP 入口網站上看到新的警示。 此警示可能需要幾分鐘才會顯示。

1. 若要在「安全性中心」內檢查警示，請前往**安全性警示**  >  **可疑的 PowerShell 命令列**。

1. 從 [調查] 視窗中，選取要移至 Microsoft Defender ATP 入口網站的連結。

## <a name="next-steps"></a>下一步

- [Azure 資訊安全中心所支援的功能和平台](security-center-os-coverage.md)
- [在 Azure 資訊安全中心設定安全性原則](tutorial-security-policy.md)：了解如何為您的 Azure 訂用帳戶及資源群組設定安全性原則。
- [管理 Azure 資訊安全中心的安全性建議](security-center-recommendations.md)：了解建議如何協助保護您的 Azure 資源。
- [Azure 資訊安全中心的安全性健全狀況監視](security-center-monitoring.md)：了解如何監視 Azure 資源的健全狀況。
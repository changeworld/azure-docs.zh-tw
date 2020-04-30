---
title: Microsoft Defender Advanced 威脅防護-Azure 資訊安全中心
description: 本檔介紹 Azure 資訊安全中心與 Microsoft Defender Advanced 威脅防護之間的整合。
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
ms.openlocfilehash: a741fb76827327c1231890d71ee1da79e052ed50
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82232405"
---
# <a name="microsoft-defender-advanced-threat-protection-with-azure-security-center"></a>使用 Azure 資訊安全中心的 Microsoft Defender Advanced 威脅防護

Azure 資訊安全中心藉由與[Microsoft Defender Advanced 威脅防護](https://www.microsoft.com/microsoft-365/windows/microsoft-defender-atp)（ATP）整合，來擴充其雲端工作負載保護平臺供應專案。
這項變革可讓端點偵測和回應 (EDR) 功能更臻完善。 透過 Microsoft Defender ATP 整合，您可以找出異常狀況。 您也可以偵測及回應 Azure 資訊安全中心監視的伺服器端點上的進階攻擊。

## <a name="microsoft-defender-atp-features-in-security-center"></a>資訊安全中心中的 Microsoft Defender ATP 功能

當您使用 Microsoft Defender ATP 時，您會取得：

- **先進的入侵後偵測感應器**：適用于 Windows 伺服器的 MICROSOFT Defender ATP 感應器可收集大量行為信號。

- 以**分析為基礎、雲端式的後置缺口偵測**： MICROSOFT Defender ATP 可快速適應不斷變化的威脅。 並使用進階分析與巨量資料。 Microsoft Defender ATP 透過 Windows、Azure 和 Office 等信號的 Intelligent Security Graph 功能來擴大，以偵測不明的威脅。 它提供採取行動警示，讓您得以快速回應。

- **威脅情報**：當 MICROSOFT Defender ATP 識別攻擊者的工具、技術和程式時，會產生警示。 它會使用由 Microsoft 威脅獵人和安全小組提供的資料，利用合作夥伴提供的情報變得更強大。

Azure 資訊安全中心現在提供下列功能：

- **自動**上線： MICROSOFT Defender ATP 感應器會針對上架為 Azure 資訊安全中心的 windows 伺服器自動啟用（執行 windows Server 2019 的系統除外）。

- **單一玻璃**： Azure 資訊安全中心主控台會顯示 MICROSOFT Defender ATP 警示。

若要進一步調查，請使用 Microsoft Defender ATP。 Microsoft Defender ATP 會提供其他資訊，例如警示進程樹狀結構和事件圖形。 您也可以查看詳細的機器時間軸，其中會顯示最多六個月歷程記錄期間的每項行為。

![包含警示詳細資訊的 Microsoft Defender ATP 頁面](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>平台支援

資訊安全中心中的 Microsoft Defender ATP 支援在 Windows Server 2016、2012 R2 和 2008 R2 SP1 上偵測，針對 Azure Vm，您需要標準層訂用帳戶，而非 Azure Vm 則只需要在工作區層級中使用標準層。

> [!NOTE]
> 當您使用 Azure 資訊安全中心來監視伺服器時，系統會自動建立 Microsoft Defender ATP 租使用者，而且 Microsoft Defender ATP 資料預設會儲存在歐洲。 如果您需要將資料移至另一個位置，您必須聯絡 Microsoft 支援服務以重設租使用者。 已針對 Office 365 GCC 客戶停用利用此整合的伺服器端點監視。

## <a name="onboarding-servers-to-security-center"></a>讓伺服器在資訊安全中心上線 

若要將伺服器上架到資訊安全中心，請按一下 [**移至**]，Azure 資訊安全中心從 MICROSOFT Defender ATP 伺服器上線中將伺服器上架。

1. **在 [上線**] 區域中，選取或建立用來儲存資料的工作區。 <br>
2. 如果您看不到所有的工作區，可能是因為許可權不足，請確定您的工作區已設定為 Azure 安全性標準層。 如需詳細資訊，請參閱[升級為資訊安全中心的標準層以增強安全性](security-center-pricing.md)。
    
3. 選取 [**新增伺服器**] 以查看如何安裝 Log Analytics 代理程式的指示。 

4. 上線之後，您可以在 [**計算與應用程式**] 底下監視機器。

   ![上線的電腦](media/security-center-wdatp/onboard-computers.png)

## <a name="enable-microsoft-defender-atp-integration"></a>啟用 Microsoft Defender ATP 整合

若要查看 Microsoft Defender ATP 整合是否已啟用，請選取 [資訊**安全中心** > **定價 & 設定**] > 按一下您的訂用帳戶。
您可以在這裡查看目前已啟用的整合內容。

  ![已啟用 Microsoft Defender ATP 整合的 Azure 資訊安全中心威脅偵測設定頁面](media/security-center-wdatp/enable-integrations.png)

- 如果您已在 Azure 資訊安全中心標準層推出伺服器，您不必採取進一步的動作。 Azure 資訊安全中心會自動將伺服器上架到 Microsoft Defender ATP。 上架最多可能需要24小時的時間。

- 如果您從未將伺服器上架到 Azure 資訊安全中心標準層，則可以照常將伺服器上架到 Azure 資訊安全中心。

- 如果您已透過 Microsoft Defender ATP 上架伺服器：
  - 參閱文件，了解[如何下架伺服器機器](https://go.microsoft.com/fwlink/p/?linkid=852906)的指南。
  - 將伺服器上架到 Azure 資訊安全中心。

## <a name="access-to-the-microsoft-defender-atp-portal"></a>存取 Microsoft Defender ATP 入口網站

請依照指示[將使用者存取權指派到入口網站](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access)。

## <a name="set-the-firewall-configuration"></a>設置防火牆設定

如果您的 proxy 或防火牆封鎖了匿名流量，因為 Microsoft Defender ATP 感應器是從系統內容連接，請確定允許匿名流量。 請遵循[啟用 proxy 伺服器中的 Microsoft DEFENDER ATP 服務 url 的存取權](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server)中的指示。

## <a name="test-the-feature"></a>測試功能

若要產生良性的 Microsoft Defender ATP 測試警示：

1. 建立資料夾 ' C:\test-MDATP-test '。

1. 您可以使用遠端桌面來存取 Windows Server 2012 R2 VM 或 Windows Server 2016 VM。 開啟命令列視窗。

1. 在提示中，複製並執行下列命令： 命令提示字元視窗將會自動關閉。

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```

   ![內含上述命令的命令提示字元視窗](media/security-center-wdatp/image4.jpeg)

1. 如果命令成功，您會在 Azure 資訊安全中心儀表板和 Microsoft Defender ATP 入口網站上看到新的警示。 此警示可能需要幾分鐘才會顯示。

1. 若要在資訊安全中心中查看警示，請前往**安全性警示** > **可疑的 PowerShell 命令列**。

1. 從 [調查] 視窗中，選取 [移至 Microsoft Defender ATP 入口網站] 連結。

## <a name="next-steps"></a>後續步驟

- [Azure 資訊安全中心所支援的功能和平台](security-center-os-coverage.md)
- [在 Azure 資訊安全中心設定安全性原則](tutorial-security-policy.md)：了解如何為您的 Azure 訂用帳戶及資源群組設定安全性原則。
- [管理 Azure 資訊安全中心的安全性建議](security-center-recommendations.md)：了解建議如何協助保護您的 Azure 資源。
- [Azure 資訊安全中心的安全性健全狀況監視](security-center-monitoring.md)：了解如何監視 Azure 資源的健全狀況。
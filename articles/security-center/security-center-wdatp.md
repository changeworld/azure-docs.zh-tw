---
title: 微軟防禦者高級威脅保護 - Azure 安全中心
description: 本文檔介紹了 Azure 安全中心和 Microsoft 防禦者高級威脅保護之間的集成。
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
ms.openlocfilehash: a502541a5a01ec3304338054239fc3b50f4d3c61
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "80810302"
---
# <a name="microsoft-defender-advanced-threat-protection-with-azure-security-center"></a>微軟防禦者高級威脅防護與 Azure 安全中心

Azure 安全中心正在通過與[Microsoft 防禦者高級威脅防護](https://www.microsoft.com/microsoft-365/windows/microsoft-defender-atp)(ATP) 整合來擴展其雲工作負載保護平台產品。
這項變革可讓端點偵測和回應 (EDR) 功能更臻完善。 通過微軟後衛ATP集成,您可以發現異常。 您也可以偵測及回應 Azure 資訊安全中心監視的伺服器端點上的進階攻擊。

## <a name="microsoft-defender-atp-features-in-security-center"></a>微軟後衛ATP功能在安全中心

當您使用微軟後衛ATP時,您將獲得:

- **先進的漏洞後檢測感測器**:微軟衛士ATP感測器為Windows伺服器收集大量的行為信號。

- **基於分析的、雲驅動的漏洞檢測**:微軟防禦者ATP快速適應不斷變化的威脅。 並使用進階分析與巨量資料。 Microsoft Defender ATP 被智慧安全圖的強大功能放大了,該圖具有跨 Windows、Azure 和 Office 的信號來檢測未知威脅。 它提供採取行動警示，讓您得以快速回應。

- **威脅情報**:微軟防禦者 ATP 在識別攻擊者的工具、技術和過程時生成警報。 它會使用由 Microsoft 威脅獵人和安全小組提供的資料，利用合作夥伴提供的情報變得更強大。

以下功能現在可在 Azure 安全中心中提供:

- **自動載入**:Microsoft Defender ATP 感測器自動啟用已載入 Azure 安全中心的 Windows 伺服器(運行 Windows Server 2019 的伺服器除外)。

- **單個窗格**:Azure 安全中心控制台顯示 Microsoft Defender ATP 警報。

- **詳細的機器調查**:Azure 安全中心客戶可以使用 Microsoft Defender ATP 控制台進行詳細調查,以發現違規範圍。

![Azure 資訊安全中心會顯示一份警示和每個警示的一般資訊的清單](media/security-center-wdatp/image1.png)

要進一步調查,請使用微軟後衛ATP。 Microsoft Defender ATP 提供了其他資訊,如警報過程樹和事件圖。 您也可以查看詳細的機器時間軸，其中會顯示最多六個月歷程記錄期間的每項行為。

![微軟後衛ATP頁面,包含有關警報的詳細資訊](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>平台支援

安全中心的 Microsoft Defender ATP 支援在 Windows Server 2016、2012 R2 和 2008 R2 SP1 上進行檢測,對於需要標準層訂閱的 Azure VM,對於僅需要在工作區級別中使用標準層的非 Azure VM。

> [!NOTE]
> 當您使用 Azure 安全中心監視伺服器時,將自動創建 Microsoft Defender ATP 租戶,預設情況下,Microsoft Defender ATP 資料將存儲在歐洲。 如果需要將數據移到其他位置,則需要聯繫Microsoft支援以重置租戶。 已為 Office 365 GCC 客戶禁用了使用此整合的伺服器終結點監視。

## <a name="onboarding-servers-to-security-center"></a>讓伺服器在資訊安全中心上線 

要將伺服器載入安全中心,請按一下 **「轉到 Azure 安全中心」,從**Microsoft Defender ATP 伺服器載入板載伺服器。

1. 在 **「入職」** 區域中,選擇或創建一個工作區,用於存儲數據。 <br>
2. 如果無法查看所有工作區,可能是由於許可權不足,請確保工作區已設置為 Azure 安全標準層。 有關詳細資訊,請參閱[升級到安全中心的標準層,以提高安全性](security-center-pricing.md)。
    
3. 選擇 **「新增伺服器**」以檢視有關如何安裝日誌分析代理的說明。 

4. 入職後,您可以在 **「計算」和「應用程式**」下監視電腦。

   ![上線的電腦](media/security-center-wdatp/onboard-computers.png)

## <a name="enable-microsoft-defender-atp-integration"></a>開啟微軟後衛ATP整合

要查看是否啟用了 Microsoft Defender ATP 整合,請選擇**安全中心** > **定價&設置**>单击您的订阅。
您可以在這裡查看目前已啟用的整合內容。

  ![啟用 Microsoft 防禦者 ATP 整合的 Azure 安全中心威脅偵測設定頁面](media/security-center-wdatp/enable-integrations.png)

- 如果您已在 Azure 資訊安全中心標準層推出伺服器，您不必採取進一步的動作。 Azure 安全中心將自動將伺服器上載到 Microsoft 防禦者 ATP。 入職可能需要長達 24 小時。

- 如果您從未將伺服器上架到 Azure 資訊安全中心標準層，則可以照常將伺服器上架到 Azure 資訊安全中心。

- 如果您已透過 Microsoft Defender ATP 登上伺服器:
  - 參閱文件，了解[如何下架伺服器機器](https://go.microsoft.com/fwlink/p/?linkid=852906)的指南。
  - 將伺服器上架到 Azure 資訊安全中心。

## <a name="access-to-the-microsoft-defender-atp-portal"></a>存取微軟後衛 ATP 門戶

請依照指示[將使用者存取權指派到入口網站](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access)。

## <a name="set-the-firewall-configuration"></a>設置防火牆設定

如果您有阻止匿名流量的代理或防火牆,因為 Microsoft Defender ATP 感測器正在從系統上下文進行連接,請確保允許匿名流量。 按照啟用[對代理伺服器中的 Microsoft Defender ATP 服務 URL](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server)的訪問中的說明進行操作。

## <a name="test-the-feature"></a>測試功能

要生成良性的 Microsoft 防禦者 ATP 測試警報:

1. 創建資料夾"C:\測試-MDATP-測試"。

1. 您可以使用遠端桌面來存取 Windows Server 2012 R2 VM 或 Windows Server 2016 VM。 打開命令列視窗。

1. 在提示中，複製並執行下列命令： 命令提示字元視窗將會自動關閉。

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```

   ![內含上述命令的命令提示字元視窗](media/security-center-wdatp/image4.jpeg)

1. 如果命令成功,您將在 Azure 安全中心儀表板和 Microsoft Defender ATP 門戶上看到新的警報。 此警示可能需要幾分鐘才會顯示。

1. 要檢視安全中心的警報,請存**取安全警報** > **可疑電源 Shell 命令列**。

1. 從調查視窗,選擇要轉到 Microsoft Defender ATP 門戶的連結。

## <a name="next-steps"></a>後續步驟

- [Azure 資訊安全中心所支援的功能和平台](security-center-os-coverage.md)
- [在 Azure 資訊安全中心設定安全性原則](tutorial-security-policy.md)：了解如何為您的 Azure 訂用帳戶及資源群組設定安全性原則。
- [管理 Azure 資訊安全中心的安全性建議](security-center-recommendations.md)：了解建議如何協助保護您的 Azure 資源。
- [Azure 資訊安全中心的安全性健全狀況監視](security-center-monitoring.md)：了解如何監視 Azure 資源的健全狀況。
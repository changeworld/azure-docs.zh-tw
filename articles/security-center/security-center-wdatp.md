---
title: 使用隨附于 Azure 資訊安全中心的 Microsoft Defender for Endpoint 授權
description: 瞭解適用于端點的 Microsoft Defender，並從 Azure 資訊安全中心進行部署。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/20/2020
ms.author: memildin
ms.openlocfilehash: 519285f2bad352aa16bdc8d9a1db7a63c2eb04e5
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98876388"
---
# <a name="protect-your-endpoints-with-security-centers-integrated-edr-solution-microsoft-defender-for-endpoint"></a>使用安全中心的整合式 EDR 解決方案來保護您的端點： Microsoft Defender for Endpoint

Microsoft Defender for Endpoint 是全方位的雲端提供端點安全性解決方案。 其主要功能如下：

- 以風險為基礎的弱點管理和評量 
- 受攻擊面縮小
- 以行為為基礎和雲端支援的保護
- 端點偵測和回應 (EDR) 
- 自動調查和補救
- 受控搜尋服務

> [!TIP]
> 此端點偵測和回應 (EDR) 產品最初以 **Windows Defender ATP** 的形式啟動，會在2019中重新命名為 **Microsoft Defender ATP**。
>
> 在 Ignite 2020，我們啟動了 [Microsoft DEFENDER XDR suite](https://www.microsoft.com/security/business/threat-protection) ，而此 EDR 元件已重新命名 **為 Microsoft defender for Endpoint**。


## <a name="availability"></a>可用性

| 層面                          | 詳細資料                                                                                                                                                                                                                                                                                                       |
|---------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 版本狀態：                  | 正式上市 (GA)                                                                                                                                                                                                                                                                                      |
| 定價：                        | 需要[適用於伺服器的 Azure Defender](security-center-pricing.md)                                                                                                                                                                                                                                             |
| 支援的平台：            | 執行 Windows 的 Azure 機器<br>執行 Windows 的 Azure Arc 電腦|
| 支援的 Windows 版本：  |  •資訊安全中心支援 Windows Server 2016、2012 R2 和 2008 R2 SP1 的偵測<br> • Office 365 GCC 客戶已停用使用此整合的伺服器端點監視|
| 不支援的作業系統：  |  • Windows Server 2019<br> • Windows 10<br> • Linux|
| 必要的角色和權限： | 若要啟用/停用整合：**安全性系統管理員** 或 **擁有** 者<br>若要在安全性中心內查看 MDATP 警示： **安全性讀取者**、 **讀者**、 **資源群組參與者**、 **資源群組擁有** 者、 **安全性系統管理員**、訂用帳戶 **擁有** 者或訂用帳戶 **參與者**|
| 雲端：                         | ![是](./media/icons/yes-icon.png) 商業雲端<br>![是](./media/icons/yes-icon.png) US Gov<br>![否](./media/icons/no-icon.png) 中國 Gov、其他 Gov<br>![否](./media/icons/no-icon.png) 在全域 Azure 雲端中執行工作負載的 GCC 客戶                                                        |
|                                 |                                                                                                                                                                                                                                                                                                               |


## <a name="microsoft-defender-for-endpoint-features-in-security-center"></a>安全性中心的 Microsoft Defender for Endpoint 功能

適用于端點的 Microsoft Defender 提供：

- **Advanced 入侵後偵測感應器**。 適用于 Windows 機器的端點感應器的 Defender 會收集大量的行為信號。

- 以 **分析為基礎、以雲端為基礎的入侵後偵測**。 Defender for Endpoint 可快速適應不斷變化的威脅。 並使用進階分析與巨量資料。 它利用 Intelligent Security Graph 的強大功能，在 Windows、Azure 和 Office 之間的信號偵測到未知的威脅。 它提供採取行動警示，讓您得以快速回應。

- **威脅情報**。 Defender for Endpoint 會在識別攻擊者的工具、技術和程式時產生警示。 它會使用由 Microsoft 威脅獵人和安全小組提供的資料，利用合作夥伴提供的情報變得更強大。

藉由整合 Defender for Endpoint 與 Security Center，您將可受益于下列額外功能：

- **自動** 上線。 針對由安全性中心監視的所有 Windows 伺服器，「安全性中心」會自動啟用「Microsoft Defender for Endpoint 感應器」。 除了執行 Windows Server 2019 的 Windows Server （必須透過本機腳本上線）之外，群組原則物件 (GPO) ，或 [Microsoft Endpoint Configuration Manager](/mem/configmgr/) (先前的 SCCM) 。

- **單一窗格**。 安全性中心主控台會顯示 Microsoft Defender 的端點警示。 若要進一步調查，請使用 Microsoft Defender 作為端點自己的入口網站頁面，您可以在其中看到警示流程樹狀結構和事件圖表等其他資訊。 您也可以查看詳細的機器時間軸，其中會顯示最多六個月歷程記錄期間的每項行為。

    :::image type="content" source="./media/security-center-wdatp/microsoft-defender-security-center.png" alt-text="適用于端點本身的安全性中心的 Microsoft Defender" lightbox="./media/security-center-wdatp/microsoft-defender-security-center.png":::

## <a name="microsoft-defender-for-endpoint-tenant-location"></a>Microsoft Defender for Endpoint 租使用者位置

當您使用 Azure 資訊安全中心監視您的伺服器時，會自動建立 Microsoft Defender for Endpoint 租使用者。 Defender 針對端點所收集的資料會儲存在租使用者的地理位置中，如布建期間所識別。 客戶資料（匿名化形式）也可能儲存在美國中部儲存和處理系統中。 

設定位置之後，就無法變更它。 如果您有自己的 Microsoft Defender for Endpoint 授權，而且需要將您的資料移到另一個位置，請聯絡 Microsoft 支援服務，以重設租使用者。


## <a name="enabling-the-microsoft-defender-for-endpoint-integration"></a>啟用 Microsoft Defender 以進行端點整合

1. 啟用 **適用于伺服器的 Azure Defender**。 請參閱 [Azure 資訊安全中心的定價](security-center-pricing.md#enable-azure-defender)。

    > [!NOTE]
    > 若要保護已啟用 Azure Arc 的電腦，請使用 [快速入門：將混合式電腦與啟用 Azure Arc 的伺服器連接](../azure-arc/servers/learn/quick-enable-hybrid-vm.md)。

1. 如果您已在伺服器上授權並部署適用于端點的 Microsoft Defender，請使用 [下架 Windows server](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints#offboard-windows-servers)中所述的程式將它移除。
1. 在資訊安全中心功能表中，選取 [定價和設定]。
1. 選取您想變更的訂用帳戶。
1. 選取 [威脅偵測]。
1. 選取 [ **允許 Microsoft Defender For Endpoint 存取我的資料**]，然後選取 [ **儲存**]。

    :::image type="content" source="./media/security-center-wdatp/enable-integration-with-edr.png" alt-text="啟用 Azure 資訊安全中心與 Microsoft 的 EDR 解決方案、Microsoft Defender for Endpoint 之間的整合":::

    Azure 資訊安全中心會自動將您的伺服器上架到 Microsoft Defender for Endpoint。 上架最多可能需要24小時的時間。


## <a name="access-the-microsoft-defender-for-endpoint-portal"></a>存取適用于端點入口網站的 Microsoft Defender

1. 確定使用者帳戶具有必要的許可權。 [深入了解](/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access)。

1. 檢查您是否擁有封鎖匿名流量的 proxy 或防火牆。 Defender for Endpoint 感應器會從系統內容連接，因此必須允許匿名流量。 若要確保不受妨礙存取端點入口網站的 Defender，請依照在 [proxy 伺服器中啟用服務 url 的存取權](/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server)中的指示進行。

1. 開啟 [Microsoft Defender 資訊安全中心入口網站](https://securitycenter.windows.com/)。 在 [Microsoft Defender 資訊安全中心入口網站總覽](/windows/security/threat-protection/microsoft-defender-atp/portal-overview)中深入瞭解入口網站的功能和圖示。 

## <a name="send-a-test-alert"></a>傳送測試警示

若要產生良性的 Microsoft Defender for Endpoint test 警示：

1. 建立資料夾 ' C:\test-MDATP-test '。
1. 使用遠端桌面存取您的電腦。
1. 開啟命令列視窗。
1. 在提示中，複製並執行下列命令： 命令提示字元視窗將會自動關閉。

    ```powershell
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```
    :::image type="content" source="./media/security-center-wdatp/generate-edr-alert.png" alt-text="命令提示字元視窗，其中含有用來產生測試警示的命令。":::

1. 如果命令成功，您會在 Azure 資訊安全中心儀表板和 Microsoft Defender for Endpoint portal 上看到新的警示。 此警示可能需要幾分鐘才會顯示。
1. 若要在「安全性中心」內檢查警示，請前往 **安全性警示**  >  **可疑的 PowerShell 命令列**。
1. 從 [調查] 視窗中，選取連結以前往 Microsoft Defender for Endpoint 入口網站。

    > [!TIP]
    > 觸發警示的方式為 **資訊** 嚴重性。

## <a name="faq-for-security-centers-integrated-microsoft-defender-for-endpoint"></a>適用于端點的安全性中心整合式 Microsoft Defender 常見問題

- [Microsoft Defender for Endpoint 的授權需求有哪些？](#what-are-the-licensing-requirements-for-microsoft-defender-for-endpoint)
- [如果我已經有「適用於端點的 Microsoft Defender」授權，我可以擁有 Azure Defender 的折扣嗎？](#if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender)
- [從協力廠商 EDR 工具如何? 切換？](#how-do-i-switch-from-a-third-party-edr-tool)

### <a name="what-are-the-licensing-requirements-for-microsoft-defender-for-endpoint"></a>Microsoft Defender for Endpoint 的授權需求有哪些？
適用于 **伺服器的 Azure defender** 不需額外付費，即可使用 Defender for Endpoint。 或者，您也可以另外購買50部機器或更多。

### <a name="if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender"></a>如果我已經有「適用於端點的 Microsoft Defender」授權，我可以擁有 Azure Defender 的折扣嗎？
如果您已經有「適用於端點的 Microsoft Defender」授權，就不需要支付該部分的 Azure Defender 授權。

若要確認您的折扣，請洽詢資訊安全中心的支援小組，並針對每個相關授權提供相關的工作區識別碼、區域和授權資訊。

### <a name="how-do-i-switch-from-a-third-party-edr-tool"></a>從協力廠商 EDR 工具如何? 切換？
如需從非 Microsoft 端點解決方案切換的完整指示，請參閱 Microsoft Defender for Endpoint 檔： [遷移總覽](/windows/security/threat-protection/microsoft-defender-atp/switch-to-microsoft-defender-migration)。
  


## <a name="next-steps"></a>後續步驟

- [Azure 資訊安全中心所支援的功能和平台](security-center-os-coverage.md)
- [管理 Azure 資訊安全中心的安全性建議](security-center-recommendations.md)：了解建議如何協助保護您的 Azure 資源。
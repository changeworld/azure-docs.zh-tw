---
title: 使用日誌分析閘道連接電腦 |微軟文檔
description: 使用日誌分析閘道將資料發送到 Azure 自動化和日誌分析服務（當它們沒有 Internet 存取權限時），連接設備和操作管理器監視的電腦。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/24/2019
ms.openlocfilehash: a92e96a835f24ac54fa55b05086a35b9a91d609e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298343"
---
# <a name="connect-computers-without-internet-access-by-using-the-log-analytics-gateway-in-azure-monitor"></a>使用 Azure 監視器中的日誌分析閘道連接沒有 Internet 訪問的電腦

>[!NOTE]
>隨著 Microsoft 操作管理套件 （OMS） 過渡到 Microsoft Azure 監視器，術語正在更改。 本文將 OMS 閘道稱為 Azure 日誌分析閘道。 
>

本文介紹如何在直接連接或操作管理器監視的電腦沒有 Internet 存取權限時，使用日誌分析閘道配置與 Azure 自動化和 Azure 監視器的通信。 

日誌分析閘道是一個 HTTP 轉發代理，支援使用 HTTP CONNECT 命令進行 HTTP 隧道。 此閘道代表無法直接連接到 Internet 的電腦將資料發送到 Azure 自動化和 Azure 監視器中的日誌分析工作區。 

Log Analytics 閘道支援︰

* 報告到其後面的每個代理上配置的相同日誌分析工作區，這些工作區配置有 Azure 自動化混合 Runbook 工作程式。  
* Microsoft 監視代理直接連接到 Azure 監視器中的日誌分析工作區的 Windows 電腦。
* Linux 的日誌分析代理直接連接到 Azure 監視器中的日誌分析工作區的 Linux 電腦。  
* 系統中心運營管理器 2012 SP1，帶 UR7，運營管理器 2012 R2 與 UR3 一起，或運營管理器 2016 或更高版本的管理組與日誌分析集成。  

某些 IT 安全性原則不允許網路電腦進行互聯網連接。 例如，這些未連接的電腦可以是支援 IT 服務的銷售點 （POS） 設備或伺服器。 要將這些設備連接到 Azure 自動化或日誌分析工作區，以便可以管理和監視它們，請將其配置為直接與日誌分析閘道通信。 日誌分析閘道可以代表他們接收配置資訊和轉發資料。 如果電腦配置了日誌分析代理直接連接到日誌分析工作區，則電腦將與其與日誌分析閘道通信。  

日誌分析閘道將資料從代理直接傳輸到服務。 它不分析傳輸中的任何資料，並且閘道在失去與服務的連接時不會緩存資料。 當閘道無法與服務通信時，代理將繼續運行並排隊對受監視電腦磁片上收集的資料進行排隊。 還原連接後，代理會將收集的緩存資料發送到 Azure 監視器。

當操作管理器管理組與日誌分析集成時，可以配置管理伺服器以連接到日誌分析閘道以接收配置資訊和發送收集的資料，具體取決於您啟用的解決方案.  操作管理器代理向管理伺服器發送一些資料。 例如，代理可能會發送操作管理器警報、配置評估資料、實例空間資料和容量資料。 其他大容量資料（如 Internet 資訊服務 （IIS） 日誌、效能資料和安全事件）將直接發送到日誌分析閘道。 

如果部署了一個或多個操作管理器閘道伺服器來監視周邊網路或隔離網路中的不受信任的系統，則這些伺服器無法與日誌分析閘道進行通信。  操作管理器閘道伺服器只能向管理伺服器報告。  當操作管理器管理組配置為與日誌分析閘道通信時，代理配置資訊將自動分發到配置為為 Azure 監視器收集日誌資料的每台代理管理的電腦，即使設置為空。

要為通過閘道與日誌分析工作區通信的直接連接或操作管理組提供高可用性，請使用網路負載平衡 （NLB） 重定向和跨多個閘道伺服器分配流量。 這樣，如果一個閘道伺服器出現故障，流量將重定向到另一個可用節點。  

運行日誌分析閘道的電腦需要日誌分析 Windows 代理來標識閘道需要與這些終結點通信的服務終結點。 代理還需要指示閘道向閘道後面的代理或操作管理器管理組配置的同一工作區報告。 此配置允許閘道和代理與其分配的工作區通信。

閘道可以多居於最多四個工作區。 這是 Windows 代理支援的工作區總數。  

每個代理都必須與閘道具有網路連接，以便代理可以自動將資料傳輸到閘道。 避免在網域控制站上安裝閘道。 閘道伺服器後面的 Linux 電腦無法使用[包裝腳本安裝](agent-linux.md#install-the-agent-using-wrapper-script)方法安裝 Linux 的日誌分析代理。 代理必須手動下載、複製到電腦並手動安裝，因為閘道僅支援與前面提到的 Azure 服務通信。

下圖顯示了從直接代理（通過閘道）流向 Azure 自動化和日誌分析的資料。 代理代理配置必須與日誌分析閘道配置的埠匹配。  

![與服務直接代理通信圖](./media/gateway/oms-omsgateway-agentdirectconnect.png)

下圖顯示從 Operations Manager 管理群組流向 Log Analytics 的資料流程。   

![運營經理與日誌分析的通信圖](./media/gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="set-up-your-system"></a>設置系統

指定用於運行日誌分析閘道的電腦必須具有以下配置：

* Windows 10、Windows 8.1 或 Windows 7
* Windows 伺服器 2019， Windows 伺服器 2016， Windows 伺服器 2012 R2， Windows 伺服器 2012， Windows 伺服器 2008 R2， 或 Windows 伺服器 2008
* Microsoft .NET Framework 4.5
* 至少一個 4 核處理器和 8 GB 的記憶體 
* [Windows 的日誌分析代理](agent-windows.md)，配置為與通過閘道通信的代理報告到同一工作區

### <a name="language-availability"></a>提供的語言

日誌分析閘道提供以下語言：

- 中文 (簡體)
- 中文 (繁體)
- 捷克文
- 荷蘭文
- 英文
- 法文
- 德文
- 匈牙利文
- 義大利文
- 日文
- 韓文
- 波蘭文
- 葡萄牙文 (巴西)
- 葡萄牙文 (葡萄牙)
- 俄文
- 西班牙文 (國際)

### <a name="supported-encryption-protocols"></a>支援的加密通訊協定

日誌分析閘道僅支援傳輸層安全性 （TLS） 1.0、1.1 和 1.2。  它不支援安全通訊端層 （SSL）。  為確保傳輸到日誌分析的資料的安全性，請將閘道配置為至少使用 TLS 1.2。 舊版本的 TLS 或 SSL 容易受到攻擊。 儘管它們當前允許向後相容性，但請避免使用它們。  

如需其它資訊，請檢閱[使用 TLS 1.2 安全地傳送](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12)。 

### <a name="supported-number-of-agent-connections"></a>支援的代理程式連線數目

下表顯示了大約有多少代理可以與閘道伺服器通信。 支援基於每 6 秒上傳約 200 KB 資料的代理。 對於每個測試的代理，資料量約為每天 2.7 GB。

|閘道 |支援的代理（近似）|  
|--------|----------------------------------|  
|CPU：英特爾至強處理器 E5-2660 v3 \@ 2.6 GHz 2 內核<br> 記憶體： 4 GB<br> 網路頻寬： 1 Gbps| 600|  
|CPU：英特爾至強處理器 E5-2660 v3 \@ 2.6 GHz 4 內核<br> 記憶體： 8 GB<br> 網路頻寬： 1 Gbps| 1000|  

## <a name="download-the-log-analytics-gateway"></a>下載 Log Analytics 閘道

從 Microsoft 下載中心 （[下載連結](https://go.microsoft.com/fwlink/?linkid=837444)） 或 Azure 門戶獲取日誌分析閘道安裝程式檔的最新版本。

要從 Azure 門戶獲取日誌分析閘道，請按照以下步驟操作：

1. 瀏覽服務清單，然後選取 [Log Analytics]****。 
1. 選取工作區。
1. 在工作區刀鋒視窗中，選取 [一般]**** 下方的 [快速入門]****。 
1. 在 [選擇要連線至工作區的資料來源]**** 下方，選取 [電腦]****。
1. 在 **"直接代理"** 邊欄選項卡中，選擇 **"下載日誌分析"閘道**。
 
   ![下載日誌分析閘道的步驟的螢幕截圖](./media/gateway/download-gateway.png)

或 

1. 在您的工作區刀鋒視窗中，於 [設定] **** 下選取 [進階設定]****。
1. 轉到**連接源** > **Windows 伺服器**並選擇**下載日誌分析閘道**。

## <a name="install-log-analytics-gateway-using-setup-wizard"></a>使用設置嚮導安裝日誌分析閘道

要使用設置嚮導安裝閘道，請按照以下步驟操作。 

1. 從目的地資料夾中，按兩下 **Log Analytics gateway.msi**。
1. 在 [歡迎]**** 頁面上，選取 [下一步]****。

   ![閘道設置嚮導中歡迎頁面的螢幕截圖](./media/gateway/gateway-wizard01.png)

1. 在 **"授權合約"** 頁上，選擇 **"我接受授權合約中的條款**以同意 Microsoft 軟體授權條款"，然後選擇 **"下一步**"。
1. 在 [連接埠和 Proxy 位址]**** 頁面上：

   a. 輸入用於閘道的 TCP 埠號。 安裝程式使用此埠號在 Windows 防火牆上配置入站規則。  預設值為 8080。
      埠號的有效範圍為 1 到 65535。 如果輸入的值不在此範圍內，就會顯示錯誤訊息。

   b. 如果安裝閘道的伺服器需要通過代理進行通信，請輸入閘道需要連接的代理位址。 例如，輸入 `http://myorgname.corp.contoso.com:80`。  如果此欄位為空，閘道將嘗試直接連接到互聯網。  如果您的 Proxy 伺服器需要驗證，請輸入使用者名稱與密碼。

   c. 選取 [下一步]****。

   ![閘道代理配置螢幕截圖](./media/gateway/gateway-wizard02.png)

1. 如果未啟用 Microsoft 更新，則會出現 Microsoft 更新頁面，您可以選擇啟用該頁面。 進行選擇，然後選擇 **"下一步**"。 否則，請繼續下一個步驟。
1. 在 **"目的檔案夾"** 頁上，離開預設資料夾 C：*程式檔\OMS 閘道，或輸入要安裝閘道的位置。 然後選擇 **"下一步**"。
1. 在 **"準備安裝"** 頁上，選擇 **"安裝**"。 如果使用者帳戶控制請求安裝許可權，請選擇"**是**"。
1. 設置完成後，選擇 **"完成**"。 要驗證服務是否正在運行，請打開服務.msc 管理單元並驗證**OMS 閘道**是否出現在服務清單中，並且其狀態是否**正在運行**。

   ![本機服務的螢幕截圖，顯示 OMS 閘道正在運行](./media/gateway/gateway-service.png)

## <a name="install-the-log-analytics-gateway-using-the-command-line"></a>使用命令列安裝日誌分析閘道

閘道的下載檔案是 Windows 安裝程式包，支援從命令列或其他自動方法進行靜默安裝。 如果您不熟悉 Windows 安裝程式的標準命令列選項，請參閱[命令列選項](https://docs.microsoft.com/windows/desktop/Msi/command-line-options)。
 
下表突出顯示了安裝程式支援的參數。

|參數| 注意|
|----------|------| 
|埠號 | 用於偵聽閘道的 TCP 埠號 |
|代理 | 代理伺服器的 IP 位址 |
|INSTALLDIR | 完全限定的路徑，用於指定閘道軟體檔的安裝目錄 |
|USERNAME | 使用代理伺服器進行身份驗證的使用者 ID |
|PASSWORD | 使用代理進行身份驗證的使用者 ID 的密碼 |
|LicenseAccepted | 指定值**1**以驗證您接受授權合約 |
|哈索特 | 指定 USERNAME/PASSWORD 參數時指定值**1** |
|有代理 | 為**PROXY**參數指定 IP 位址時指定值**1** |

要靜默安裝閘道，並配置閘道與特定的代理位址，埠號，鍵入以下內容：

```dos
Msiexec.exe /I "oms gateway.msi" /qn PORTNUMBER=8080 PROXY="10.80.2.200" HASPROXY=1 LicenseAccepted=1 
```

使用 /qn 命令列選項隱藏設置，/qb 在靜默安裝期間顯示設定。  

如果需要提供憑據以使用代理進行身份驗證，請鍵入以下內容：

```dos
Msiexec.exe /I "oms gateway.msi" /qn PORTNUMBER=8080 PROXY="10.80.2.200" HASPROXY=1 HASAUTH=1 USERNAME="<username>" PASSWORD="<password>" LicenseAccepted=1 
```

安裝後，您可以使用以下 PowerShell Cmdlet 確認接受設置（不包括使用者名和密碼）：

- **獲取 OMSGateway Config** – 返回閘道配置為偵聽的 TCP 埠。
- **獲取 OMSGateway 中繼代理**– 返回您將其配置為與其通信的代理伺服器的 IP 位址。

## <a name="configure-network-load-balancing"></a>設定網路負載平衡

您可以使用 Microsoft[網路負載平衡 （NLB）、Azure](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing)[負載平衡器](../../load-balancer/load-balancer-overview.md)或基於硬體的負載等化器配置閘道以實現高可用性。 負載平衡器可藉由跨其節點將來自 Log Analytics 代理程式或 Operations Manager 管理伺服器的要求連線進行重新導向，來管理流量。 如果閘道伺服器故障，流量就會被重新導向到其他節點。

### <a name="microsoft-network-load-balancing"></a>Microsoft Network Load Balancing

若要了解如何設計和部署 Windows Server 2016 網路負載平衡叢集，請參閱[網路負載平衡](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing)。 下列步驟說明如何設定 Microsoft 網路負載平衡叢集。  

1. 使用系統管理帳戶登入屬於 NLB 叢集成員的 Windows 伺服器。  
2. 在 [伺服器管理員] 中開啟網路負載平衡管理員，然後依序按一下 [工具]**** 和 [網路負載平衡管理員]****。
3. 若要連線已安裝 Microsoft Monitoring Agent 的 Log Analytics 閘道伺服器，請用滑鼠右鍵按一下叢集的 IP 位址，然後按一下 [新增主機到叢集]****。 

    ![網路負載平衡管理員 – 新增主機到叢集](./media/gateway/nlb02.png)
 
4. 輸入您要連線之閘道伺服器的 IP 位址。 

    ![網路負載平衡管理員 – 新增主機到叢集：連線](./media/gateway/nlb03.png) 

### <a name="azure-load-balancer"></a>Azure Load Balancer

要瞭解如何設計和部署 Azure 負載等化器，請參閱什麼是[Azure 負載等化器？](../../load-balancer/load-balancer-overview.md) 要部署基本負載等化器，請按照此[快速入門](../../load-balancer/quickstart-load-balancer-standard-public-portal.md)中概述的步驟操作，不包括 **"創建後端伺服器**"一節中概述的步驟。   

> [!NOTE]
> 使用**基本 SKU**配置 Azure 負載等化器 要求 Azure 虛擬機器屬於可用性集。 要瞭解有關可用性集的更多資訊[，請參閱管理 Azure 中的 Windows 虛擬機器的可用性](../../virtual-machines/windows/manage-availability.md)。 要將現有虛擬機器添加到可用性集，請參閱[設置 Azure 資源管理器 VM 可用性集](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4)。
> 

創建負載等化器後，需要創建後端池，將流量分發到一個或多個閘道伺服器。 按照快速入門文章部分中描述的步驟[為負載等化器創建資源](../../load-balancer/quickstart-load-balancer-standard-public-portal.md)。  

>[!NOTE]
>配置運行狀況探測時，應將其配置為使用閘道伺服器的 TCP 埠。 運行狀況探測根據對運行狀況檢查的回應動態添加或刪除負載等化器旋轉中的閘道伺服器。 
>

## <a name="configure-the-log-analytics-agent-and-operations-manager-management-group"></a>配置日誌分析代理和運營經理管理組

在本節中，您將瞭解如何使用日誌分析閘道配置直接連接的日誌分析代理、操作管理器管理組或 Azure 自動化混合 Runbook 工作人員，以便與 Azure 自動化或日誌分析進行通信。  

### <a name="configure-a-standalone-log-analytics-agent"></a>配置獨立的日誌分析代理

配置日誌分析代理時，請將代理伺服器值替換為日誌分析閘道伺服器的 IP 位址及其埠號。 如果在負載等化器後面部署了多個閘道伺服器，則日誌分析代理代理配置是負載等化器的虛擬 IP 位址。  

>[!NOTE]
>要在直接連接到日誌分析的閘道和 Windows 電腦上安裝日誌分析代理，請參閱[將 Windows 電腦連接到 Azure 中的日誌分析服務](agent-windows.md)。 要連接 Linux 電腦，請參閱[將 Linux 電腦連接到 Azure 監視器](agent-linux.md)。 
>

在閘道伺服器上安裝代理後，將其配置為向與閘道通信的工作區或工作區代理報告。 如果閘道上未安裝日誌分析 Windows 代理，則事件 300 將寫入 OMS 閘道事件日誌，指示需要安裝代理。 如果代理已安裝，但未配置為向通過代理通信的代理報告到同一工作區，則事件 105 將寫入同一日誌，指示需要將閘道上的代理配置為向與與閘道通信。

完成配置後，重新開機**OMS 閘道**服務以應用更改。 否則，閘道將拒絕嘗試與日誌分析通信的代理，並將報告 OMS 閘道事件日誌中的事件 105。 當您從閘道伺服器上的代理配置中添加或刪除工作區時，也會發生這種情況。

有關自動化混合 Runbook 輔助角色的資訊，請參閱[使用混合 Runbook 輔助角色在資料中心或雲中自動執行資源](../../automation/automation-hybrid-runbook-worker.md)。

### <a name="configure-operations-manager-where-all-agents-use-the-same-proxy-server"></a>配置操作管理器，其中所有代理使用相同的代理伺服器

即使設定是空的，Operations Manager Proxy 組態仍會自動套用到所有向 Operations Manager 報告的代理程式。  

要使用 OMS 閘道來支援操作管理器，您必須具有：

* Microsoft 監視代理（版本 8.0.10900.0 或更高版本）安裝在 OMS 閘道伺服器上，並配置了管理組配置為報告的相同日誌分析工作區。
* 互聯網連接。 或者，OMS 閘道必須連接到連接到 Internet 的代理伺服器。

> [!NOTE]
> 如果未為閘道指定值，則空白值將推送到所有代理。
>

如果操作管理器管理組首次向日志分析工作區註冊，則不會在操作主控台中看到指定管理組代理配置的選項。 僅當管理組已註冊到服務時，此選項才可用。  

要組態集成，請使用運行操作主控台的系統上的 Netsh 以及管理組中的所有管理伺服器上更新系統代理配置。 請遵循下列步驟：

1. 打開提升的命令提示：

   a. 選擇 **"開始"** 並輸入**cmd**。  

   b. 按右鍵**命令提示**符並選擇"**以管理員身份運行**"。  

1. 輸入下列命令：

   `netsh winhttp set proxy <proxy>:<port>`

完成與日誌分析的集成後，通過運行`netsh winhttp reset proxy`刪除更改。 然後，在操作主控台中，使用 **"配置代理伺服器"** 選項指定日誌分析閘道伺服器。 

1. 在操作管理器主控台上，在**操作管理套件**下，選擇**連接**，然後選擇 **"配置代理伺服器**"。

   ![操作管理器的螢幕截圖，顯示配置代理伺服器的選擇](./media/gateway/scom01.png)

1. 選擇 **"使用代理伺服器訪問操作管理套件**"，然後輸入日誌分析閘道伺服器的 IP 位址或負載等化器的虛擬 IP 位址。 小心從首碼`http://`開始。

   ![操作管理器的螢幕截圖，顯示代理伺服器位址](./media/gateway/scom02.png)

1. 選取 [完成]****。 Operations Manager 管理群組現在已設定為透過閘道伺服器來與 Log Analytics 服務進行通訊。

### <a name="configure-operations-manager-where-specific-agents-use-a-proxy-server"></a>配置操作管理器，其中特定代理使用代理伺服器

對於大型或複雜的環境，您可能只希望特定伺服器（或組）使用日誌分析閘道伺服器。  對於這些伺服器，不能直接更新操作管理器代理，因為此值被管理組的全域值覆蓋。  相反，重寫用於推送這些值的規則。  

> [!NOTE] 
> 如果要允許環境中有多個日誌分析閘道伺服器，請使用此配置技術。  例如，您可以要求在區域基礎上指定特定的日誌分析閘道伺服器。
>

要配置特定伺服器或組以使用日誌分析閘道伺服器，請執行以下操作： 

1. 開啟 Operations Manager 主控台，然後選取 [撰寫]**** 工作區。  
1. 在 [撰寫] 工作區中選取 [規則]****。 
1. 在操作管理器工具列上，選擇 **"範圍**"按鈕。 如果此按鈕不可用，請確保在 **"監視"** 窗格中選擇物件而不是資料夾。 [範圍管理組件物件]**** 對話方塊會顯示一般會作為目標之類別、群組或物件的清單。 
1. 在"**查找"** 欄位中，輸入 **"運行狀況服務"** 並從清單中選擇它。 選取 [確定]****。  
1. 搜索**顧問代理設置規則**。 
1. 在操作管理器工具列上，選擇 **"覆蓋"，** 然後指向 **"覆蓋規則\對於類的特定物件：運行狀況服務**"並從清單中選擇一個物件。  或者創建一個自訂群組，其中包含要應用此覆蓋的伺服器的運行狀況服務物件。 然後，將重寫應用於自訂群組。
1. 在 **"覆蓋屬性"** 對話方塊中，在**WebProxy 位址**參數旁邊的 **"覆蓋**"列中添加核取記號。  在 **"覆蓋值"** 欄位中，輸入日誌分析閘道伺服器的 URL。 小心從首碼`http://`開始。  

    >[!NOTE]
    > 您不需要啟用此規則。 它已在面向 Microsoft 系統中心顧問監視伺服器組的 Microsoft 系統中心顧問安全參考覆蓋管理包中通過覆蓋自動管理。
    > 

1. 從 **"選擇目標管理包"** 清單中選擇管理包，或通過選擇 **"新建**"創建新的未密封管理包。 
1. 完成時，請選取 [確定]****。 

### <a name="configure-for-automation-hybrid-runbook-workers"></a>為自動化混合 Runbook 工作人員配置

如果您的環境中具有自動化混合 Runbook 工作，請按照以下步驟配置閘道以支援輔助角色。

請參閱"配置自動化文檔[的網路](../../automation/automation-hybrid-runbook-worker.md#network-planning)"部分，查找每個區域的 URL。

如果您的電腦自動註冊為混合 Runbook 輔助角色，例如，如果為一個或多個 VM 啟用了更新管理解決方案，請按照以下步驟操作：

1. 將作業執行階段資料服務 URL 新增到 Log Analytics 閘道上的「允許的主機」清單。 例如： `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. 使用以下 PowerShell Cmdlet 重新啟動 Log Analytics 閘道服務：`Restart-Service OMSGatewayService`

如果使用混合 Runbook 輔助角色註冊 Cmdlet 將電腦加入 Azure 自動化，請按照以下步驟操作：

1. 將代理程式服務註冊 URL 新增到 Log Analytics 閘道上的「允許的主機」清單。 例如： `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
1. 將作業執行階段資料服務 URL 新增到 Log Analytics 閘道上的「允許的主機」清單。 例如： `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. 重新啟動 Log Analytics 閘道服務。
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>PowerShell Cmdlet

您可以使用 Cmdlet 完成任務來更新日誌分析閘道的配置設置。 在使用 Cmdlet 之前，請確保：

1. 安裝日誌分析閘道（微軟 Windows 安裝程式）。
1. 開啟 PowerShell 主控台視窗。
1. 通過鍵入以下命令導入模組：`Import-Module OMSGateway`
1. 如果上一個步驟沒有發生錯誤，就表示模組已經成功匯入，且可以使用 Cmdlet。 輸入 `Get-Module OMSGateway`
1. 使用 Cmdlet 進行更改後，重新開機 OMS 閘道服務。

步驟 3 中的錯誤意味著未導入模組。 當 PowerShell 找不到模組時，可能會出現錯誤。 您可以在 OMS 閘道安裝路徑中找到該模組 *：C：\程式檔\微軟 OMS 閘道\PowerShell_OmsGateway*。

| **Cmdlet** | **參數** | **描述** | **範例** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Key |取得服務的組態 |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |索引鍵 (必要) <br> 值 |變更服務的組態 |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |取得轉送 (上游) Proxy 的位址 |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |位址<br> 使用者名稱<br> 密碼（安全字串） |設定轉送 (上游) Proxy 的位址 (與認證) |1. 設置中繼代理和憑據：<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. 設置不需要身份驗證的中繼代理：`Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. 清除繼電器代理設置：<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |獲取當前允許的主機（僅本地配置的允許主機，而不是自動下載允許的主機） |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |主機 (必要) |將主機加到允許清單 |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |主機 (必要) |將主機從允許清單移除 |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |主體 (必要) |將用戶端憑證主體加到允許清單 |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |主體 (必要) |將用戶端憑證主體從允許清單移除 |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |獲取當前允許的用戶端憑證主題（僅本地配置的允許主題，而不是自動下載允許的主題） |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>疑難排解

要收集閘道記錄的事件，應安裝日誌分析代理。

![日誌分析閘道日誌中事件檢視器清單的螢幕截圖](./media/gateway/event-viewer.png)

### <a name="log-analytics-gateway-event-ids-and-descriptions"></a>日誌分析閘道事件指示和說明

下表顯示了日誌分析閘道日誌事件的事件指示和說明。

| **識別碼** | **描述** |
| --- | --- |
| 400 |沒有特定 ID 的任何應用程式錯誤。 |
| 401 |錯誤組態。 例如，偵聽埠 = "文本"而不是整數。 |
| 402 |剖析 TLS 交握訊息時發生例外狀況。 |
| 403 |網路錯誤。 例如，無法連接到目標伺服器。 |
| 100 |一般資訊。 |
| 101 |服務已經啟動。 |
| 102 |服務已經停止。 |
| 103 |從用戶端接收了 HTTP CONNECT 命令。 |
| 104 |不是 HTTP CONNECT 命令。 |
| 105 |目標伺服器不在允許清單中，或目標埠不安全 （443）。 <br> <br> 確保 OMS 閘道伺服器上的 MMA 代理和與 OMS 閘道通信的代理連接到同一日誌分析工作區。 |
| 105 |錯誤 Tcp 連接 = 無效用戶端憑證：CN=閘道。 <br><br> 確保您使用的是 OMS 閘道版本 1.0.395.0 或更高版本。 還確保 OMS 閘道伺服器上的 MMA 代理和與 OMS 閘道通信的代理連接到同一日誌分析工作區。 |
| 106 |不支援的 TLS/SSL 協定版本。<br><br> 日誌分析閘道僅支援 TLS 1.0、TLS 1.1 和 1.2。 不支援 SSL。|
| 107 |已經驗證 TLS 工作階段。 |

### <a name="performance-counters-to-collect"></a>要收集的效能計數器

以下表格顯示可供 Log Analytics 閘道使用的效能計數器。 使用效能監視器添加計數器。

| **名稱** | **描述** |
| --- | --- |
| Log Analytics 閘道/使用中用戶端連線 |使用中用戶端網路 (TCP) 連線數目 |
| Log Analytics 閘道/錯誤計數 |錯誤數目 |
| Log Analytics 閘道/已連線用戶端 |已連線用戶端數目 |
| Log Analytics 閘道/拒絕次數 |因為任何 TLS 驗證錯誤而被拒絕的次數 |

![日誌分析閘道介面的螢幕截圖，顯示效能計數器](./media/gateway/counters.png)

## <a name="assistance"></a>協助

登錄到 Azure 門戶後，可以獲取有關日誌分析閘道或任何其他 Azure 服務或功能的説明。
要獲取説明，請選擇門戶右上角的問號圖示，然後選擇 **"新建支援請求**"。 然後填寫新的支援請求表。

![新支援請求的螢幕截圖](./media/gateway/support.png)

## <a name="next-steps"></a>後續步驟

[添加資料來源](../../azure-monitor/platform/agent-data-sources.md)以從連接的源收集資料，並將資料存儲在日誌分析工作區中。

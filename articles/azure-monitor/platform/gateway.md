---
title: 使用 Log Analytics 閘道來連接電腦 |Microsoft Docs
description: 使用 Log Analytics 閘道將您的裝置和 Operations Manager 監視的電腦連線，以將資料傳送至 Azure 自動化和 Log Analytics 服務，而無法存取網際網路。
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/24/2019
ms.openlocfilehash: 30854382b5a6dfd0faabfc2f59340dc21518d6f2
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773282"
---
# <a name="connect-computers-without-internet-access-by-using-the-log-analytics-gateway-in-azure-monitor"></a>在 Azure 監視器中使用 Log Analytics 閘道將電腦連線，而不需要網際網路存取

>[!NOTE]
>隨著 Microsoft Operations Management Suite （OMS）轉換至 Microsoft Azure 監視器，術語會改變。 本文是指作為 Azure Log Analytics 閘道的 OMS 閘道。 
>

本文說明如何在直接連線或受 Operations Manager 監視的電腦沒有網際網路存取時，使用 Log Analytics 閘道來設定與 Azure 自動化和 Azure 監視器的通訊。 

Log Analytics 閘道是 HTTP 正向 proxy，使用 HTTP CONNECT 命令支援 HTTP 通道。 此閘道會代表無法直接連線到網際網路的電腦，將資料傳送至 Azure 監視器中的 Azure 自動化和 Log Analytics 工作區。 

Log Analytics 閘道支援︰

* 最多報告在其後方的每個代理程式上設定的相同 Log Analytics 工作區，並使用 Azure 自動化混合式 Runbook 背景工作角色進行設定。  
* 在 Azure 監視器中，Microsoft Monitoring Agent 直接連線到 Log Analytics 工作區的 Windows 電腦。
* 適用于 Linux 的 Log Analytics 代理程式在 Azure 監視器中直接連線到 Log Analytics 工作區的 Linux 電腦。  
* System Center Operations Manager 2012 SP1 搭配 UR7、Operations Manager 2012 R2 with UR3，或 Operations Manager 2016 或更新版本中與 Log Analytics 整合的管理群組。  

某些 IT 安全性原則不允許網路電腦的網際網路連線。 這些未連線的電腦可以是銷售點（POS）裝置或支援 IT 服務的伺服器，例如。 若要將這些裝置連線到 Azure 自動化或 Log Analytics 工作區，讓您可以管理和監視它們，請將它們設定為直接與 Log Analytics 閘道通訊。 Log Analytics 閘道可以接收設定資訊，並代表其轉送資料。 如果將電腦設定為使用 Log Analytics 代理程式直接連線到 Log Analytics 工作區，則電腦會改為與 Log Analytics 閘道通訊。  

Log Analytics 閘道會將資料從代理程式直接傳輸到服務。 它不會分析傳輸中的任何資料，而且當閘道失去與服務的連接時，不會快取資料。 當閘道無法與服務通訊時，代理程式會繼續執行，並將收集到的資料排入受監視電腦磁片上的佇列。 當連接已還原時，代理程式會將收集到的快取資料傳送至 Azure 監視器。

當 Operations Manager 管理群組與 Log Analytics 整合時，可以將管理伺服器設定為連線到 Log Analytics 閘道，以根據您已啟用的解決方案來接收設定資訊和傳送收集到的資料.  Operations Manager 代理程式會將一些資料傳送到管理伺服器。 例如，代理程式可能會傳送 Operations Manager 警示、設定評估資料、實例空間資料和容量資料。 其他大量資料（例如 Internet Information Services （IIS）記錄、效能資料和安全性事件）會直接傳送到 Log Analytics 閘道。 

如果部署一或多部 Operations Manager 閘道伺服器來監視周邊網路或隔離網路中未受信任的系統，這些伺服器就無法與 Log Analytics 閘道通訊。  Operations Manager 閘道伺服器只能向管理伺服器報告。  當 Operations Manager 管理群組設定為與 Log Analytics 閘道通訊時，proxy 設定資訊會自動發佈到每一部代理程式管理的電腦，而這些電腦已設定為收集 Azure 監視器的記錄資料。即使設定是空的。

若要為透過閘道與 Log Analytics 工作區通訊的直接連線或作業管理群組提供高可用性，請使用網路負載平衡（NLB）將流量重新導向並分散到多個閘道伺服器。 如此一來，如果某個閘道伺服器故障，流量就會重新導向至另一個可用的節點。  

執行 Log Analytics 閘道的電腦需要 Log Analytics Windows 代理程式，才能識別閘道需要與之通訊的服務端點。 代理程式也需要指示閘道向已設定閘道後方代理程式或 Operations Manager 管理群組的相同工作區報告。 此設定可讓閘道和代理程式與其指派的工作區進行通訊。

閘道可以是多重主目錄，最多可達四個工作區。 這是 Windows 代理程式支援的工作區總數。  

每個代理程式都必須具有閘道的網路連線，代理程式才能自動將資料傳輸到閘道。 避免在網域控制站上安裝閘道。 位於閘道伺服器後方的 Linux 電腦無法使用包裝函式[腳本安裝](agent-linux.md#install-the-agent-using-wrapper-script)方法來安裝適用于 Linux 的 Log Analytics 代理程式。 代理程式必須手動下載、複製到電腦，並手動安裝，因為閘道僅支援與先前所述的 Azure 服務進行通訊。

下圖顯示透過閘道從直接代理程式流向 Azure 自動化和 Log Analytics 的資料。 代理程式 proxy 設定必須符合 Log Analytics 閘道所設定的埠。  

![直接代理程式與服務通訊的圖表](./media/gateway/oms-omsgateway-agentdirectconnect.png)

下圖顯示從 Operations Manager 管理群組流向 Log Analytics 的資料流程。   

![Operations Manager 與 Log Analytics 通訊的圖表](./media/gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="set-up-your-system"></a>設定您的系統

指定要執行 Log Analytics 閘道的電腦必須具有下列設定：

* Windows 10、Windows 8.1 或 Windows 7
* Windows Server 2019、Windows Server 2016、Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 或 Windows Server 2008
* Microsoft .NET Framework 4.5
* 至少4核心處理器和 8 GB 的記憶體 
* [適用于 Windows 的 Log Analytics 代理程式](agent-windows.md)，其設定為向透過閘道通訊的代理程式報告至相同的工作區

### <a name="language-availability"></a>提供的語言

Log Analytics 閘道提供下列語言版本：

- 中文 (簡體)
- 中文 (繁體)
- 捷克文
- 荷蘭文
- 繁體中文
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

Log Analytics 閘道僅支援傳輸層安全性（TLS）1.0、1.1 和1.2。  它不支援安全通訊端層（SSL）。  若要確保傳輸到 Log Analytics 的資料安全性，請將閘道設定為至少使用 TLS 1.2。 較舊版本的 TLS 或 SSL 很容易受到攻擊。 雖然目前允許回溯相容性，但請避免使用它們。  

如需其它資訊，請檢閱[使用 TLS 1.2 安全地傳送](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12)。 

### <a name="supported-number-of-agent-connections"></a>支援的代理程式連線數目

下表顯示大約有多少代理程式可以與閘道伺服器通訊。 支援是以每6秒上傳大約 200 KB 資料的代理程式為基礎。 針對每個測試的代理程式，資料磁片區每天大約 2.7 GB。

|閘道 |支援的代理程式（近似）|  
|--------|----------------------------------|  
|CPU： Intel （r）處理器 E5-2660 v3 \@ 2.6 GHz 2 核心<br> 記憶體： 4 GB<br> 網路頻寬： 1 Gbps| 600|  
|CPU： Intel 4.x 處理器 E5-2660 v3 \@ 2.6 GHz 4 核心<br> 記憶體： 8 GB<br> 網路頻寬： 1 Gbps| 1000|  

## <a name="download-the-log-analytics-gateway"></a>下載 Log Analytics 閘道

從 Microsoft 下載中心（[下載連結](https://go.microsoft.com/fwlink/?linkid=837444)）或 Azure 入口網站取得 Log Analytics 閘道安裝程式檔案的最新版本。

若要從 Azure 入口網站取得 Log Analytics 閘道，請遵循下列步驟：

1. 瀏覽服務清單，然後選取 [Log Analytics]。 
1. 選取工作區。
1. 在工作區刀鋒視窗中，選取 [一般] 下方的 [快速入門]。 
1. 在 [選擇要連線至工作區的資料來源] 下方，選取 [電腦]。
1. 在 [**直接代理程式**] 分頁中，選取 [**下載 Log Analytics 閘道**]。
 
   ![下載 Log Analytics 閘道之步驟的螢幕擷取畫面](./media/gateway/download-gateway.png)

或 

1. 在您的工作區刀鋒視窗中，於 [設定]下選取 [進階設定]。
1. 移至 連線的**來源** > **Windows 伺服器**，然後選取 **下載 Log Analytics 閘道**。

## <a name="install-log-analytics-gateway-using-setup-wizard"></a>使用安裝程式安裝 Log Analytics 閘道

若要使用安裝精靈來安裝閘道，請遵循下列步驟。 

1. 從目的地資料夾中，按兩下 **Log Analytics gateway.msi**。
1. 在 [歡迎] 頁面上，選取 [下一步]。

   ![閘道安裝程式中 [歡迎使用] 頁面的螢幕擷取畫面](./media/gateway/gateway-wizard01.png)

1. 在 [**授權合約**] 頁面上，選取 [**我接受授權合約中的條款**] 以同意 Microsoft 軟體授權條款，然後選取 **[下一步]** 。
1. 在 [連接埠和 Proxy 位址] 頁面上：

   a. 輸入閘道要使用的 TCP 埠號碼。 安裝程式會使用此埠號碼在 Windows 防火牆上設定輸入規則。  預設值為 8080。
      埠號碼的有效範圍是1到65535。 如果輸入的值不在此範圍內，就會顯示錯誤訊息。

   b. 如果閘道安裝所在的伺服器需要透過 proxy 進行通訊，請輸入閘道需要連線的 proxy 位址。 例如，輸入 `http://myorgname.corp.contoso.com:80`。  如果您將此欄位保留空白，閘道將會嘗試直接連線到網際網路。  如果您的 Proxy 伺服器需要驗證，請輸入使用者名稱與密碼。

   c. 選取 [下一步]。

   ![閘道 proxy 設定的螢幕擷取畫面](./media/gateway/gateway-wizard02.png)

1. 如果您未啟用 Microsoft Update，[Microsoft Update] 頁面隨即出現，您可以選擇啟用它。 進行選取，然後選取 **[下一步]** 。 否則，請繼續下一個步驟。
1. 在 [**目的地資料夾**] 頁面上，保留預設資料夾 C:\Program files\oms gateway gateway，或輸入您要安裝閘道的位置。 然後選取 [下一步]。
1. 在 [準備安裝] 頁面上，選取 [安裝]。 如果 [使用者帳戶控制] 要求安裝的許可權，請選取 **[是]** 。
1. 安裝完成之後，請選取 **[完成]** 。 若要確認服務是否正在執行，請開啟 services.msc 嵌入式管理單元，並確認服務清單**中已顯示**[ **OMS 閘道**]，且其狀態為 [執行中]。

   ![本機服務的螢幕擷取畫面，顯示 OMS 閘道正在執行](./media/gateway/gateway-service.png)

## <a name="install-the-log-analytics-gateway-using-the-command-line"></a>使用命令列安裝 Log Analytics 閘道

閘道下載的檔案是一個支援從命令列或其他自動化方法進行無訊息安裝的 Windows Installer 套件。 如果您不熟悉 Windows Installer 的標準命令列選項，請參閱[命令列選項](https://docs.microsoft.com/windows/desktop/Msi/command-line-options)。
 
下表將重點放在安裝程式所支援的參數。

|參數| 注意|
|----------|------| 
|PORTNUMBER | 要接聽之閘道的 TCP 通訊埠編號 |
|PROXY | Proxy 伺服器的 IP 位址 |
|INSTALLDIR | 指定閘道軟體檔案安裝目錄的完整路徑 |
|USERNAME | 用來向 proxy 伺服器進行驗證的使用者識別碼 |
|PASSWORD | 要使用 proxy 進行驗證之使用者識別碼的密碼 |
|LicenseAccepted | 指定**1**的值，確認您接受授權合約 |
|HASAUTH | 指定使用者名稱/密碼參數時，請指定**1**的值 |
|HASPROXY | 指定**PROXY**參數的 IP 位址時，請指定**1**的值 |

若要以無訊息方式安裝閘道，並使用特定的 proxy 位址（埠號碼）加以設定，請輸入下列內容：

```dos
Msiexec.exe /I "oms gateway.msi" /qn PORTNUMBER=8080 PROXY="10.80.2.200" HASPROXY=1 LicenseAccepted=1 
```

使用/qn 命令列選項會隱藏安裝程式，/qb 會在無訊息安裝期間顯示安裝程式。  

如果您需要提供認證以向 proxy 進行驗證，請輸入下列內容：

```dos
Msiexec.exe /I "oms gateway.msi" /qn PORTNUMBER=8080 PROXY="10.80.2.200" HASPROXY=1 HASAUTH=1 USERNAME="<username>" PASSWORD="<password>" LicenseAccepted=1 
```

安裝之後，您可以使用下列 PowerShell Cmdlet 來確認是否已接受設定（不包括使用者名稱和密碼）：

- **OMSGatewayConfig** –傳回閘道設定為接聽的 TCP 埠。
- **OMSGatewayRelayProxy** –傳回您設定要與之通訊的 PROXY 伺服器 IP 位址。

## <a name="configure-network-load-balancing"></a>設定網路負載平衡

您可以使用 Microsoft[網路負載平衡（nlb）](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing)、 [Azure Load Balancer](../../load-balancer/load-balancer-overview.md)或硬體型負載平衡器，透過網路負載平衡（nlb）設定閘道的高可用性。 負載平衡器可藉由跨其節點將來自 Log Analytics 代理程式或 Operations Manager 管理伺服器的要求連線進行重新導向，來管理流量。 如果閘道伺服器故障，流量就會被重新導向到其他節點。

### <a name="microsoft-network-load-balancing"></a>Microsoft 網路負載平衡

若要了解如何設計和部署 Windows Server 2016 網路負載平衡叢集，請參閱[網路負載平衡](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing)。 下列步驟說明如何設定 Microsoft 網路負載平衡叢集。  

1. 使用系統管理帳戶登入屬於 NLB 叢集成員的 Windows 伺服器。  
2. 在 [伺服器管理員] 中開啟網路負載平衡管理員，然後依序按一下 [工具] 和 [網路負載平衡管理員]。
3. 若要連線已安裝 Microsoft Monitoring Agent 的 Log Analytics 閘道伺服器，請用滑鼠右鍵按一下叢集的 IP 位址，然後按一下 [新增主機到叢集]。 

    ![網路負載平衡管理員–將主機新增至叢集](./media/gateway/nlb02.png)
 
4. 輸入您要連線之閘道伺服器的 IP 位址。 

    ![網路負載平衡管理員 – 新增主機到叢集：連線](./media/gateway/nlb03.png) 

### <a name="azure-load-balancer"></a>Azure Load Balancer

若要瞭解如何設計和部署 Azure Load Balancer，請參閱[什麼是 Azure Load Balancer？](../../load-balancer/load-balancer-overview.md)。 若要部署基本負載平衡器，請遵循本[快速入門](../../load-balancer/quickstart-load-balancer-standard-public-portal.md)中所述的步驟，但不包括**建立後端伺服器**一節中所述的步驟。   

> [!NOTE]
> 使用**基本 SKU**設定 Azure Load Balancer，會要求 Azure 虛擬機器必須屬於可用性設定組。 若要深入瞭解可用性設定組，請參閱[在 Azure 中管理 Windows 虛擬機器的可用性](../../virtual-machines/windows/manage-availability.md)。 若要將現有的虛擬機器新增至可用性設定組，請參閱[設定 Azure Resource Manager 的 VM 可用性設定組](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4)。
> 

建立負載平衡器之後，必須建立後端集區，以將流量分散到一或多個閘道伺服器。 請依照快速入門文章一節中所述的步驟[來建立負載平衡器的資源](../../load-balancer/quickstart-load-balancer-standard-public-portal.md)。  

>[!NOTE]
>設定健康情況探查時，應將其設定為使用閘道伺服器的 TCP 埠。 健康狀態探查會根據其對健康狀態檢查的回應，從負載平衡器輪替中動態新增或移除閘道伺服器。 
>

## <a name="configure-the-log-analytics-agent-and-operations-manager-management-group"></a>設定 Log Analytics 代理程式和 Operations Manager 管理群組

在本節中，您將瞭解如何設定直接連線的 Log Analytics 代理程式、Operations Manager 管理群組，或使用 Log Analytics 閘道 Azure 自動化混合式 Runbook 背景工作角色，以與 Azure 自動化或 Log Analytics 進行通訊。  

### <a name="configure-a-standalone-log-analytics-agent"></a>設定獨立的 Log Analytics 代理程式

設定 Log Analytics 代理程式時，請將 proxy 伺服器值取代為 Log Analytics 閘道伺服器的 IP 位址及其通訊埠編號。 如果您已在負載平衡器後方部署多個閘道伺服器，Log Analytics 代理程式 proxy 設定就是負載平衡器的虛擬 IP 位址。  

>[!NOTE]
>若要在直接連線到 Log Analytics 的閘道和 Windows 電腦上安裝 Log Analytics 代理程式，請參閱[將 Windows 電腦連線到 Azure 中的 Log analytics 服務](agent-windows.md)。 若要連接 Linux 電腦，請參閱[將 linux 電腦連線至 Azure 監視器](agent-linux.md)。 
>

在閘道伺服器上安裝代理程式之後，請將它設定為向與閘道通訊的工作區或工作區代理程式報告。 如果閘道上未安裝 Log Analytics Windows 代理程式，則會將事件300寫入 OMS 閘道事件記錄檔，表示需要安裝代理程式。 如果代理程式已安裝，但未設定為向透過它進行通訊的代理程式報告至相同的工作區，則會將事件105寫入相同的記錄檔，表示閘道上的代理程式必須設定為與共同的代理人回報相同的工作區使用閘道 mmunicate。

完成設定之後，請重新開機**OMS 閘道**服務以套用變更。 否則，閘道將會拒絕嘗試與 Log Analytics 通訊的代理程式，並且會在 OMS 閘道事件記錄檔中報告事件105。 當您從閘道伺服器上的代理程式設定新增或移除工作區時，也會發生這種情況。

如需自動化混合式 Runbook 背景工作角色的相關資訊，請參閱[使用混合式 runbook 背景工作角色將資料中心或雲端中的資源自動化](../../automation/automation-hybrid-runbook-worker.md)。

### <a name="configure-operations-manager-where-all-agents-use-the-same-proxy-server"></a>設定 Operations Manager，其中所有代理程式都會使用相同的 proxy 伺服器

即使設定是空的，Operations Manager Proxy 組態仍會自動套用到所有向 Operations Manager 報告的代理程式。  

若要使用 OMS 閘道來支援 Operations Manager，您必須具備：

* Microsoft Monitoring Agent （版本8.0.10900.0 或更新版本）安裝在 OMS 閘道伺服器上，並使用您的管理群組設定來回報的相同 Log Analytics 工作區來設定。
* 網際網路連線能力。 或者，OMS 閘道必須連接到連線到網際網路的 proxy 伺服器。

> [!NOTE]
> 如果您未指定閘道的值，系統會將空白值推送至所有代理程式。
>

如果您的 Operations Manager 管理群組第一次向 Log Analytics 工作區註冊，您將不會在 Operations 主控台中看到指定管理群組之 proxy 設定的選項。 只有在已向服務註冊管理群組時，才能使用此選項。  

若要設定整合，請在執行 Operations 主控台的系統上，以及在管理群組中的所有管理伺服器上，使用 Netsh 更新系統 proxy 設定。 請遵循下列步驟：

1. 開啟提升許可權的命令提示字元：

   a. 選取 [**開始**]，然後輸入**cmd**。  

   b. 以滑鼠右鍵按一下 [**命令提示**字元] 並選取 [以**系統管理員身分執行**]。  

1. 輸入下列命令：

   `netsh winhttp set proxy <proxy>:<port>`

完成與 Log Analytics 的整合之後，請執行 `netsh winhttp reset proxy`來移除變更。 然後，在 Operations 主控台中，使用 [**設定 proxy 伺服器**] 選項來指定 Log Analytics 閘道伺服器。 

1. 在 Operations Manager 主控台的 [ **Operations Management Suite**] 底下，**選取 [** 連線]，然後選取 [**設定 Proxy 伺服器**]。

   ![Operations Manager 的螢幕擷取畫面，其中顯示 [設定 Proxy 伺服器] 選項](./media/gateway/scom01.png)

1. 選取 [**使用 proxy 伺服器來存取 Operations Management Suite** ]，然後輸入 Log Analytics 閘道伺服器的 IP 位址或負載平衡器的虛擬 ip 位址。 請小心從前置詞 `http://`開始。

   ![Operations Manager 的螢幕擷取畫面，其中顯示 proxy 伺服器位址](./media/gateway/scom02.png)

1. 選取 [完成]。 Operations Manager 管理群組現在已設定為透過閘道伺服器來與 Log Analytics 服務進行通訊。

### <a name="configure-operations-manager-where-specific-agents-use-a-proxy-server"></a>設定 Operations Manager，其中特定代理程式使用 proxy 伺服器

對於大型或複雜的環境，您可能只想讓特定伺服器（或群組）使用 Log Analytics 閘道伺服器。  對於這些伺服器，您無法直接更新 Operations Manager 代理程式，因為管理群組的全域值會覆寫此值。  相反地，請覆寫用來推送這些值的規則。  

> [!NOTE] 
> 如果您想要在您的環境中允許多個 Log Analytics 閘道伺服器，請使用此設定技術。  例如，您可以要求以區域為基礎來指定特定的 Log Analytics 閘道伺服器。
>

若要設定要使用 Log Analytics 閘道伺服器的特定伺服器或群組： 

1. 開啟 Operations Manager 主控台，然後選取 [撰寫] 工作區。  
1. 在 [撰寫] 工作區中選取 [規則]。 
1. 在 [Operations Manager] 工具列上，選取 [**範圍**] 按鈕。 如果此按鈕無法使用，請確定您已在 [**監視**中] 窗格中選取物件，而不是資料夾。 [範圍管理組件物件] 對話方塊會顯示一般會作為目標之類別、群組或物件的清單。 
1. 在 [**尋找**] 欄位中，輸入**健全狀況服務**並從清單中選取。 選取 [確定]。  
1. 搜尋 [ **Advisor Proxy 設定規則**]。 
1. 在 [Operations Manager] 工具列上，選取 [**覆寫**]，然後指向 [覆**寫類別的特定物件：健全狀況服務的覆**]，然後從清單中選取物件。  或建立自訂群組，其中包含您想要套用此覆寫之伺服器的健全狀況服務物件。 然後將覆寫套用至您的自訂群組。
1. 在 [覆**寫**內容] 對話方塊的 [ **WebProxyAddress** ] 參數旁邊的 [覆**寫**] 資料行中，新增核取記號。  在 [覆**寫值**] 欄位中，輸入 Log Analytics 閘道伺服器的 URL。 請小心從前置詞 `http://`開始。  

    >[!NOTE]
    > 您不需要啟用此規則。 在以 Microsoft System center Advisor 監視伺服器群組為目標的 Microsoft System Center Advisor 安全參考覆寫管理元件中，它已自動使用覆寫進行管理。
    > 

1. 從 [**選取目的地管理**元件] 清單中選取管理元件，或選取 [**新增**] 來建立新的未密封管理元件。 
1. 完成時，請選取 [確定]。 

### <a name="configure-for-automation-hybrid-runbook-workers"></a>設定自動化混合式 Runbook 背景工作角色

如果您的環境中有自動化混合式 Runbook 背景工作角色，請遵循下列步驟來設定閘道以支援背景工作角色。

請參閱自動化檔的[設定您的網路](../../automation/automation-hybrid-runbook-worker.md#network-planning)一節，以尋找每個區域的 URL。

如果您的電腦是自動註冊為混合式 Runbook 背景工作角色，例如，如果已針對一或多個 Vm 啟用更新管理解決方案，請遵循下列步驟：

1. 將作業執行階段資料服務 URL 新增到 Log Analytics 閘道上的「允許的主機」清單。 例如： `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. 使用以下 PowerShell Cmdlet 重新啟動 Log Analytics 閘道服務：`Restart-Service OMSGatewayService`

如果您的電腦已使用混合式 Runbook 背景工作註冊 Cmdlet 加入 Azure 自動化，請遵循下列步驟：

1. 將代理程式服務註冊 URL 新增到 Log Analytics 閘道上的「允許的主機」清單。 例如： `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
1. 將作業執行階段資料服務 URL 新增到 Log Analytics 閘道上的「允許的主機」清單。 例如： `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. 重新啟動 Log Analytics 閘道服務。
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>PowerShell Cmdlet

您可以使用 Cmdlet 來完成工作，以更新 Log Analytics 閘道的設定。 使用 Cmdlet 之前，請務必：

1. 安裝 Log Analytics 閘道（Microsoft Windows Installer）。
1. 開啟 PowerShell 主控台視窗。
1. 輸入下列命令以匯入模組： `Import-Module OMSGateway`
1. 如果上一個步驟沒有發生錯誤，就表示模組已經成功匯入，且可以使用 Cmdlet。 輸入 `Get-Module OMSGateway`
1. 使用 Cmdlet 進行變更之後，請重新開機 OMS 閘道服務。

步驟3中的錯誤表示模組未匯入。 當 PowerShell 找不到模組時，可能會發生此錯誤。 您可以在 OMS 閘道安裝路徑中找到此模組： *C:\Program FILES\MICROSOFT OMS Gateway\PowerShell\OmsGateway*。

| **Cmdlet** | **參數** | **說明** | **範例** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |索引鍵 |取得服務的組態 |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |索引鍵 (必要) <br> 值 |變更服務的組態 |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |取得轉送 (上游) Proxy 的位址 |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |位址<br> 使用者名稱<br> 密碼 |設定轉送 (上游) Proxy 的位址 (與認證) |1. 設定轉送 proxy 和認證：<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. 設定不需要驗證的轉送 proxy： `Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. 清除轉送 proxy 設定：<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |取得目前允許的主機（僅限本機設定的允許的主機，而不是自動下載的允許的主機） |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |主機 (必要) |將主機加到允許清單 |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |主機 (必要) |將主機從允許清單移除 |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |主體 (必要) |將用戶端憑證主體加到允許清單 |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |主體 (必要) |將用戶端憑證主體從允許清單移除 |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |取得目前允許的用戶端憑證主體（僅限本機設定的允許主體，而不是自動下載的允許的主體） |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>疑難排解

若要收集閘道所記錄的事件，您應該安裝 Log Analytics 代理程式。

![Log Analytics 閘道記錄中事件檢視器清單的螢幕擷取畫面](./media/gateway/event-viewer.png)

### <a name="log-analytics-gateway-event-ids-and-descriptions"></a>Log Analytics 閘道事件識別碼和描述

下表顯示 Log Analytics 閘道記錄事件的事件識別碼和描述。

| **識別碼** | **說明** |
| --- | --- |
| 400 |沒有特定識別碼的任何應用程式錯誤。 |
| 401 |錯誤組態。 例如，listenPort = "text"，而不是整數。 |
| 402 |剖析 TLS 交握訊息時發生例外狀況。 |
| 403 |網路錯誤。 例如，無法連接到目標伺服器。 |
| 100 |一般資訊。 |
| 101 |服務已經啟動。 |
| 102 |服務已經停止。 |
| 103 |從用戶端接收到 HTTP CONNECT 命令。 |
| 104 |不是 HTTP CONNECT 命令。 |
| 105 |目的地伺服器不在允許清單中，或目的地埠不安全（443）。 <br> <br> 確定 OMS 閘道伺服器上的 MMA 代理程式和與 OMS 閘道通訊的代理程式已連線到相同的 Log Analytics 工作區。 |
| 105 |錯誤 Error tcpconnection –不正確用戶端憑證： CN = Gateway。 <br><br> 請確定您使用的是 OMS 閘道1.0.395.0 或更高版本。 此外，請確定 OMS 閘道伺服器上的 MMA 代理程式和與 OMS 閘道通訊的代理程式已連線到相同的 Log Analytics 工作區。 |
| 106 |不支援的 TLS/SSL 通訊協定版本。<br><br> Log Analytics 閘道僅支援 TLS 1.0、TLS 1.1 及1.2。 不支援 SSL。|
| 107 |已經驗證 TLS 工作階段。 |

### <a name="performance-counters-to-collect"></a>要收集的效能計數器

以下表格顯示可供 Log Analytics 閘道使用的效能計數器。 使用效能監視器來新增計數器。

| **名稱** | **說明** |
| --- | --- |
| Log Analytics 閘道/使用中用戶端連線 |使用中用戶端網路 (TCP) 連線數目 |
| Log Analytics 閘道/錯誤計數 |錯誤數目 |
| Log Analytics 閘道/已連線用戶端 |已連線用戶端數目 |
| Log Analytics 閘道/拒絕次數 |因為任何 TLS 驗證錯誤而被拒絕的次數 |

![Log Analytics 閘道介面的螢幕擷取畫面，其中顯示效能計數器](./media/gateway/counters.png)

## <a name="assistance"></a>協助

當您登入 Azure 入口網站時，您可以取得 Log Analytics 閘道或任何其他 Azure 服務或功能的協助。
若要取得協助，請選取入口網站右上角的問號圖示，然後選取 [**新增支援要求**]。 然後完成新的支援要求表單。

![新支援要求的螢幕擷取畫面](./media/gateway/support.png)

## <a name="next-steps"></a>後續步驟

[新增資料來源](../../azure-monitor/platform/agent-data-sources.md)以從連線的來源收集資料，並將資料儲存在您的 Log Analytics 工作區中。

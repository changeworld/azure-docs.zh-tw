---
title: 將 Syslog 資料連線到 Azure Sentinel |Microsoft Docs
description: 使用設備和 Sentinel 之間 Linux 電腦上的代理程式，將支援 Syslog 的任何電腦或設備連線到 Azure Sentinel。 
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 27c1ad4907b0b16ce6830a6fe787b78f6129eadd
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87322834"
---
# <a name="collect-data-from-linux-based-sources-using-syslog"></a>使用 Syslog 從以 Linux 為基礎的來源收集資料

您可以使用適用于 Linux 的 Log Analytics 代理程式（先前稱為 OMS 代理程式），將來自 Linux 型、支援 Syslog 的機器或設備的事件串流至 Azure Sentinel。 您可以針對任何可讓您直接在電腦上安裝 Log Analytics 代理程式的電腦執行此動作。 機器的原生 Syslog daemon 會收集指定類型的本機事件，並將它們轉送到本機代理程式，以將它們串流至您的 Log Analytics 工作區。

> [!NOTE]
> - 如果您的設備支援透過**Syslog 的一般事件格式（CEF）**，則會收集較完整的資料集，並在集合中剖析資料。 您應該選擇此選項，並遵循[使用 CEF 連接外部解決方案](connect-common-event-format.md)中的指示。
>
> - Log Analytics 支援收集由**rsyslog**或**syslog ng**的守護程式所傳送的訊息，其中的 rsyslog 是預設值。 Red Hat Enterprise Linux （RHEL）、CentOS 和 Oracle Linux 版本（**sysklog**）版本5的預設 syslog daemon 不支援 syslog 事件收集。 若要從此版的這些散發套件收集 syslog 資料，rsyslog 精靈應該安裝和設定為取代 sysklog。

## <a name="how-it-works"></a>運作方式

**Syslog**是 Linux 通用的事件記錄通訊協定。 當您的 VM 或設備上已安裝**適用于 Linux 的 Log Analytics 代理程式**時，安裝常式會將本機 Syslog daemon 設定為將訊息轉送至 TCP 通訊埠25224上的代理程式。 然後，代理程式會透過 HTTPS 將訊息傳送至您的 Log Analytics 工作區，並將它剖析成**Azure Sentinel > 記錄**檔中 Syslog 資料表的事件記錄專案。

如需詳細資訊，請參閱[Azure 監視器中的 Syslog 資料來源](../azure-monitor/platform/data-sources-syslog.md)。

## <a name="configure-syslog-collection"></a>設定 Syslog 集合

### <a name="configure-your-linux-machine-or-appliance"></a>設定您的 Linux 機器或設備

1. 在 Azure Sentinel 中，選取 [**資料連線器**]，然後選取 [ **Syslog**連接器]。

1. 在 [ **Syslog** ] 分頁上，選取 [**開啟連接器] 頁面**。

1. 安裝 Linux 代理程式。 在 **[選擇安裝代理程式的位置**] 底下：
    
    **針對 Azure Linux VM：**
      
    1. 選取 [**在 Azure Linux 虛擬機器上安裝代理程式**]。
    
    1. 按一下 [**下載 & 安裝適用于 Azure Linux 虛擬機器的代理程式 >** ] 連結。 
    
    1. 在 [**虛擬機器**] 分頁中，按一下要在其中安裝代理程式的虛擬機器，然後按一下 **[連線]**。 針對您要連接的每個 VM 重複此步驟。
    
    **針對任何其他 Linux 電腦：**

    1. 選取 [**在非 Azure Linux 電腦上安裝代理程式**]

    1. 按一下 [**下載 & 安裝非 Azure Linux 機器的代理程式 >** ] 連結。 

    1. 在 [**代理程式管理**] 分頁中，按一下 [ **linux 伺服器**] 索引標籤，然後複製適用于**linux 的下載和上架代理程式**的命令，並在 linux 電腦上執行。 
    
   > [!NOTE]
   > 請務必根據貴組織的安全性原則來設定這些電腦的安全性設定。 例如，您可以設定網路設定，使其符合組織的網路安全性原則，並變更背景程式中的埠和通訊協定，使其符合安全性需求。

### <a name="configure-the-log-analytics-agent"></a>設定 Log Analytics 代理程式

1. 在 [Syslog 連接器] 分頁底部，按一下 [**開啟您的工作區] [advanced settings configuration** ]，>] 連結。

1. 在 [**高級設定**] 分頁上，選取 [**資料**  >  **Syslog**]。 然後新增要收集之連接器的設備。
    
    - 新增您的 syslog 應用裝置在其記錄標頭中所包含的功能。 
    
    - 如果您想要搭配所收集的資料使用異常的 SSH 登入偵測，請新增**auth**和**authpriv**。 如需其他詳細資料，請參閱[下一節](#configure-the-syslog-connector-for-anomalous-ssh-login-detection)。

1. 當您已新增要監視的所有設備，並調整每個設施的任何嚴重性選項時，請選取 [**將下列設定套用到我的電腦**] 核取方塊。

1. 選取 [儲存]。 

1. 在您的 VM 或設備上，請確定您正在傳送您所指定的設備。

1. 若要查詢**記錄**檔中的 syslog 記錄資料，請 `Syslog` 在查詢視窗中輸入。

1. 您可以使用在[Azure 監視器記錄查詢中使用函數](../azure-monitor/log-query/functions.md)中所述的查詢參數來剖析 Syslog 訊息。 然後，您可以將查詢儲存為新的 Log Analytics 函式，並將它當做新的資料類型使用。

> [!NOTE]
>
> 您也可以使用現有的[CEF 記錄](connect-cef-agent.md)轉寄站電腦，從一般 Syslog 來源收集和轉送記錄。 不過，您必須執行下列步驟，以避免將兩種格式的事件傳送至 Azure Sentinel，因為這會導致事件重複。
>
>    已[從您的 CEF 來源設定資料收集](connect-common-event-format.md)，並已如下所示設定 Log Analytics 代理程式：
>
> 1. 在以 CEF 格式傳送記錄的每一部電腦上，您必須編輯 Syslog 設定檔，以移除用來傳送 CEF 訊息的設備。 如此一來，在 CEF 中傳送的設備也不會在 Syslog 中傳送。 如需如何執行此操作的詳細指示，請參閱在[Linux 代理程式上設定 Syslog](../azure-monitor/platform/data-sources-syslog.md#configure-syslog-on-linux-agent) 。
>
> 1. 您必須在這些電腦上執行下列命令，以在 Azure Sentinel 中停用 Syslog 設定的代理程式同步處理。 這可確保您在上一個步驟中所做的設定變更不會遭到覆寫。<br>
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`


### <a name="configure-the-syslog-connector-for-anomalous-ssh-login-detection"></a>設定 Syslog 連接器以進行異常的 SSH 登入偵測

> [!IMPORTANT]
> 異常的 SSH 登入偵測目前為公開預覽版。
> 這項功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure Sentinel 可以將機器學習（ML）套用至 syslog 資料，以識別異常的安全殼層（SSH）登入活動。 案例包括：

- 不可能的移動–當兩個成功的登入事件從兩個位置（不可能到達兩個登入事件的時間範圍內）發生時。
- 非預期的位置–發生成功登入事件的位置可疑。 例如，最近看不到位置。
 
此偵測需要特定的 Syslog 資料連線器設定： 

1. 在上一個程式的步驟5中，請確定已選取 [**驗證**] 和 [ **authpriv** ] 做為要監視的設備。 保留 [嚴重性] 選項的預設設定，讓它們全部選取。 例如：
    
    > [!div class="mx-imgBorder"]
    > ![異常 SSH 登入偵測所需的設備](./media/connect-syslog/facilities-ssh-detection.png)

2. 允許收集 syslog 資訊足夠的時間。 然後，流覽至**Azure Sentinel 記錄**檔，並複製並貼上下列查詢：
    
    ```console
    Syslog |  where Facility in ("authpriv","auth")| extend c = extract( "Accepted\\s(publickey|password|keyboard-interactive/pam)\\sfor ([^\\s]+)",1,SyslogMessage)| where isnotempty(c) | count 
    ```
    
    視需要變更**時間範圍**，然後選取 [**執行**]。
    
    如果產生的計數為零，請確認連接器的設定，以及受監視的電腦在您為查詢指定的時間週期內，是否有成功的登入活動。
    
    如果產生的計數大於零，則您的 syslog 資料適用于異常的 SSH 登入偵測。 您可以從**分析**  >   **規則範本**  >  **（預覽）異常的 SSH 登入偵測**來啟用此偵測。

## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將 Syslog 內部部署應用裝置連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。


---
title: 部署記錄轉寄站，以將 CEF 資料連線到 Azure Sentinel |Microsoft Docs
description: 瞭解如何部署代理程式，以將 CEF 資料連線到 Azure Sentinel。
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
ms.date: 10/01/2020
ms.author: yelevin
ms.openlocfilehash: 2f90a7c954fc8930f69dfee3e7b56b394225405a
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94516732"
---
# <a name="step-1-deploy-the-log-forwarder"></a>步驟1：部署記錄轉寄站


在此步驟中，您將指定並設定 Linux 機器，以將記錄從安全性解決方案轉寄至 Azure Sentinel 工作區。 這部電腦可以是您內部部署環境中的實體或虛擬機器、Azure VM 或另一個雲端中的 VM。 使用提供的連結，您將在指定的電腦上執行腳本，以執行下列工作：

- 安裝適用于 Linux 的 Log Analytics 代理程式 (也稱為 OMS 代理程式) 並針對下列用途進行設定：
    - 接聽 TCP 通訊埠25226上內建 Linux Syslog daemon 的 CEF 訊息
    - 透過 TLS 安全地將訊息傳送到您的 Azure Sentinel 工作區，並在其中進行剖析和擴充

- 針對下列用途，設定內建 Linux Syslog daemon (rsyslog. d/Syslog-ng) ：
    - 在 TCP 埠514上接聽來自安全性解決方案的 Syslog 訊息
    - 使用 TCP 通訊埠25226，只將它識別為 CEF 的訊息轉送到 localhost 上的 Log Analytics 代理程式
 
## <a name="prerequisites"></a>必要條件

- 您必須在指定的 Linux 電腦上擁有較高的許可權 (sudo) 。

- 您必須在 Linux 機器上安裝 **python 2.7** 或 **3** 。<br>使用 `python -version` 命令來檢查。

- 在您安裝 Log Analytics 代理程式之前，必須先將 Linux 電腦連線到任何 Azure 工作區。

- 在此程式中的某個時間點，您可能需要工作區識別碼和工作區主要金鑰。 您可以在工作區資源的 [代理程式 **管理** ] 下找到它們。

## <a name="run-the-deployment-script"></a>執行部署指令碼
 
1. 在 Azure Sentinel 導覽功能表中，按一下 [ **資料連線器** ]。 從連接器清單中，按一下 [ **常見事件格式] (CEF)** 磚，然後按一下右下方的 [ **開啟連接器] 頁面** 按鈕。 

1. 在 **1.2 的 Linux 機器上安裝 CEF 收集器** ，複製下執行下列腳本下提供的連結， **以安裝並套用 CEF 收集器** ，或從下面的文字 (套用工作區識別碼和主要金鑰來取代預留位置) ：

    ```bash
    sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`
    ```

1. 當腳本正在執行時，請檢查以確定您沒有收到任何錯誤或警告訊息。
    - 您可能會收到一則訊息，引導您執行命令以更正 *電腦* 欄位對應的問題。 如需詳細資訊，請參閱 [部署腳本中的說明](#mapping-command) 。

> [!NOTE]
> **使用相同的電腦來轉送一般 Syslog *和* CEF 訊息**
>
> 如果您打算使用此記錄轉寄站電腦來轉寄 [syslog 訊息](connect-syslog.md) 和 CEF，則為了避免將事件複製到 Syslog 和 CommonSecurityLog 資料表：
>
> 1. 在以 CEF 格式將記錄傳送至轉寄站的每部來源電腦上，您必須編輯 Syslog 設定檔以移除用來傳送 CEF 訊息的功能。 如此一來，在 CEF 中傳送的設備也不會在 Syslog 中傳送。 如需如何進行的詳細指示，請參閱在 [Linux 代理程式上設定 Syslog](../azure-monitor/platform/data-sources-syslog.md#configure-syslog-on-linux-agent) 。
>
> 1. 您必須在這些電腦上執行下列命令，以在 Azure Sentinel 中停用 Syslog 設定的代理程式同步處理。 這可確保您在前一個步驟中所做的設定變更不會遭到覆寫。<br>
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`

繼續進行 [步驟2：設定您的安全性解決方案以轉寄 CEF 訊息](connect-cef-solution-config.md) 。

## <a name="deployment-script-explained"></a>說明的部署腳本

以下是部署腳本動作的命令指令描述。

選擇 syslog daemon 以查看適當的描述。

# <a name="rsyslog-daemon"></a>[rsyslog daemon](#tab/rsyslog)

1. **下載並安裝 Log Analytics 代理程式：**

    - 下載 Log Analytics (OMS) Linux 代理程式的安裝腳本。

        ```bash
        wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/
            onboard_agent.sh
        ```

    - 安裝 Log Analytics 代理程式。
    
        ```bash
        sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com
        ```

1. **將 Log Analytics 代理程式設定設定為接聽埠25226，並將 CEF 訊息轉送至 Azure Sentinel：**

    - 從 Log Analytics 代理程式 GitHub 存放庫下載設定。

        ```bash
        wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf
            https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/
            omsagent.d/security_events.conf
        ```

1. **設定 Syslog daemon：**

    - 使用 syslog 設定檔開啟埠514以進行 TCP 通訊 `/etc/rsyslog.conf` 。

    - 藉由將特殊設定檔案插入 syslog daemon 目錄，設定在 TCP 埠25226上將 CEF 訊息轉送至 Log Analytics 代理程式的 daemon `security-config-omsagent.conf` `/etc/rsyslog.d/` 。

        檔案的內容 `security-config-omsagent.conf` ：

        ```bash
        if $rawmsg contains "CEF:" or $rawmsg contains "ASA-" then @@127.0.0.1:25226 
        ```

1. **重新開機 Syslog daemon 和 Log Analytics 代理程式：**

    - 重新開機 rsyslog daemon。
    
        ```bash
        service rsyslog restart
        ```

    - 重新開機 Log Analytics 代理程式。

        ```bash
        /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. **確認 *電腦* 欄位的對應是否如預期：**

    - 使用下列命令，確保 syslog 來源中的 *電腦* 欄位已正確地對應至 Log Analytics 代理程式： 

        ```bash
        grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
        ```
    - <a name="mapping-command"></a>如果對應有問題，腳本會產生錯誤訊息，讓您以 **手動方式執行下列命令** (套用工作區識別碼來取代預留位置) 。 此命令會確保正確的對應，並重新啟動代理程式。
    
        ```bash
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

# <a name="syslog-ng-daemon"></a>[syslog-ng daemon](#tab/syslogng)

1. **下載並安裝 Log Analytics 代理程式：**

    - 下載 Log Analytics (OMS) Linux 代理程式的安裝腳本。

        ```bash
        wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/
            onboard_agent.sh
        ```

    - 安裝 Log Analytics 代理程式。
    
        ```bash
        sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com
        ```

1. **將 Log Analytics 代理程式設定設定為接聽埠25226，並將 CEF 訊息轉送至 Azure Sentinel：**

    - 從 Log Analytics 代理程式 GitHub 存放庫下載設定。

        ```bash
        wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf
            https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/
            omsagent.d/security_events.conf
        ```

1. **設定 Syslog daemon：**

    - 使用 syslog 設定檔開啟埠514以進行 TCP 通訊 `/etc/syslog-ng/syslog-ng.conf` 。

    - 藉由將特殊設定檔案插入 syslog daemon 目錄，設定在 TCP 埠25226上將 CEF 訊息轉送至 Log Analytics 代理程式的 daemon `security-config-omsagent.conf` `/etc/syslog-ng/conf.d/` 。

        檔案的內容 `security-config-omsagent.conf` ：

        ```bash
        filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
        destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
        log {source(s_src);filter(f_oms_filter);destination(oms_destination);};
        ```

1. **重新開機 Syslog daemon 和 Log Analytics 代理程式：**

    - 重新開機 syslog-ng daemon。
    
        ```bash
        service syslog-ng restart
        ```

    - 重新開機 Log Analytics 代理程式。

        ```bash
        /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. **確認 *電腦* 欄位的對應是否如預期：**

    - 使用下列命令，確保 syslog 來源中的 *電腦* 欄位已正確地對應至 Log Analytics 代理程式： 

        ```bash
        grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
        ```
    - <a name="mapping-command"></a>如果對應有問題，腳本會產生錯誤訊息，讓您以 **手動方式執行下列命令** (套用工作區識別碼來取代預留位置) 。 此命令會確保正確的對應，並重新啟動代理程式。
    
        ```bash
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何部署 Log Analytics 代理程式，以將 CEF 設備連線至 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats.md)。


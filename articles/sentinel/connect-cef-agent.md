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
ms.date: 04/19/2020
ms.author: yelevin
ms.openlocfilehash: 502fbe3bc7b1de2038bc444ae5daf180cfc80203
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85298985"
---
# <a name="step-1-deploy-the-log-forwarder"></a>步驟1：部署記錄轉寄站


在此步驟中，您將指定並設定將從安全性解決方案將記錄轉送至 Azure Sentinel 工作區的 Linux 機器。 這部電腦可以是內部部署環境中的實體或虛擬機器、Azure VM 或另一個雲端中的 VM。 使用提供的連結，您將會在執行下列工作的指定電腦上執行腳本：
- 安裝適用于 Linux 的 Log Analytics 代理程式（也稱為 OMS 代理程式），並針對下列目的進行設定：
    - 接聽 TCP 通訊埠25226上內建 Linux Syslog daemon 的 CEF 訊息
    - 透過 TLS 安全地將訊息傳送至您的 Azure Sentinel 工作區，並在其中剖析和擴充訊息

- 設定內建的 Linux Syslog daemon （rsyslog. d/Syslog-ng）以進行下列目的：
    - 從 TCP 通訊埠514上的安全性解決方案接聽 Syslog 訊息
    - 使用 TCP 埠25226，僅將其識別為 CEF 的訊息轉送至 localhost 上的 Log Analytics 代理程式
 
## <a name="prerequisites"></a>必要條件

- 您必須在指定的 Linux 電腦上具有更高的許可權（sudo）。
- 您必須在 Linux 機器上安裝 python。<br>使用 `python -version` 命令來進行檢查。
- 在您安裝 Log Analytics 代理程式之前，Linux 機器不得連線到任何 Azure 工作區。

## <a name="run-the-deployment-script"></a>執行部署指令碼
 
1. 從 Azure Sentinel 導覽功能表中，按一下 [**資料連線器**]。 從連接器清單中，按一下 [**一般事件格式（CEF）** ] 磚，然後在右下方的 [**開啟連接器頁面**] 按鈕。 

1. 在**1.2 底下的 Linux 機器上安裝 CEF 收集器**，複製 [執行下列腳本] 底下提供的連結，**以安裝和套用 CEF 收集器**，或從下面的文字：

     `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`

1. 當腳本正在執行時，請檢查以確定您未收到任何錯誤或警告訊息。

繼續進行[步驟2：設定您的安全性解決方案轉送 CEF 訊息](connect-cef-solution-config.md)。

## <a name="deployment-script-explained"></a>已說明部署腳本

以下是部署腳本動作的命令逐步說明。

選擇 syslog daemon 以查看適當的描述。

# <a name="rsyslog-daemon"></a>[rsyslog daemon](#tab/rsyslog)

1. **下載並安裝 Log Analytics 代理程式：**

    - 下載 Log Analytics （OMS） Linux 代理程式的安裝腳本<br>
        `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh`

    - 安裝 Log Analytics 代理程式<br>
        `sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com`

1. **設定 Syslog daemon：**

    1. 針對使用 syslog 設定檔的 TCP 通訊開啟埠 514 `/etc/rsyslog.conf` 。

    1. 藉由將特殊設定檔案插入 syslog daemon 目錄，設定守護程式將 CEF 訊息轉送到 TCP 通訊埠25226上的 Log Analytics 代理程式 `security-config-omsagent.conf` `/etc/rsyslog.d/` 。

        檔案的內容 `security-config-omsagent.conf` ：

            :rawmsg, regex, "CEF"|"ASA"
            *.* @@127.0.0.1:25226

1. **重新開機 Syslog daemon**

    `service rsyslog restart`

1. **將 Log Analytics 代理程式設定設為接聽埠25226，並將 CEF 訊息轉送至 Azure Sentinel**

    1. 從 Log Analytics 代理程式 GitHub 存放庫下載設定<br>
        `wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/omsagent.d/security_events.conf`


    1. 重新開機 Log Analytics 代理程式<br>
        `/opt/microsoft/omsagent/bin/service_control restart [workspaceID]`

# <a name="syslog-ng-daemon"></a>[syslog-ng daemon](#tab/syslogng)

1. **下載並安裝 Log Analytics 代理程式：**

    - 下載 Log Analytics （OMS） Linux 代理程式的安裝腳本<br>`wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh`

    - 安裝 Log Analytics 代理程式<br>`sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com`

1. **設定 Syslog daemon：**

    1. 針對使用 syslog 設定檔的 TCP 通訊開啟埠 514 `/etc/syslog-ng/syslog-ng.conf` 。

    1. 藉由將特殊設定檔案插入 syslog daemon 目錄，設定守護程式將 CEF 訊息轉送到 TCP 通訊埠25226上的 Log Analytics 代理程式 `security-config-omsagent.conf` `/etc/syslog-ng/conf.d/` 。

        檔案的內容 `security-config-omsagent.conf` ：

            filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
            destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
            log {source(s_src);filter(f_oms_filter);destination(oms_destination);};

1. **重新開機 Syslog daemon**

    `service syslog-ng restart`

1. **將 Log Analytics 代理程式設定設為接聽埠25226，並將 CEF 訊息轉送至 Azure Sentinel**

    1. 從 Log Analytics 代理程式 GitHub 存放庫下載設定<br>
        `wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/omsagent.d/security_events.conf`


    1. 重新開機 Log Analytics 代理程式<br>
        `/opt/microsoft/omsagent/bin/service_control restart [workspaceID]`

---

## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何部署 Log Analytics 代理程式，以將 CEF 設備連線到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats.md)。


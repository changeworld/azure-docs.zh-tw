---
title: 部署紀錄轉寄站將 CEF 資料連接到 Azure 哨兵 |微軟文件
description: 瞭解如何部署代理以將 CEF 資料連接到 Azure Sentinel。
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
ms.openlocfilehash: 5a8b97e5bef57b29f388c86628f0af5d05e1724a
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731650"
---
# <a name="step-1-deploy-the-log-forwarder"></a>第一步:部署紀錄轉寄站


在此步驟中,您將指定和配置 Linux 計算機,它將日誌從安全解決方案轉發到 Azure Sentinel 工作區。 此電腦可以是本地環境中的物理電腦或虛擬機器、Azure VM 或其他雲端中的 VM。 使用提供的連結,您將在指定的電腦上執行執行以下工作的文稿:
- 安裝 Linux 的紀錄分析代理(也稱為 OMS 代理),並將其設定為以下目的:
    - 從 TCP 連接埠 25226 上的內建 Linux Syslog 守護行程偵聽 CEF 訊息
    - 透過 TLS 安全地將郵件發送到 Azure Sentinel 工作區,在那裡對其進行解析和豐富

- 為以下目的設定內建 Linux Syslog 守護程式 (rsyslog.d/syslog-ng):
    - 在 TCP 連接埠 514 上從安全解決方案中偵聽 Syslog 訊息
    - 使用 TCP 連接埠 25226 將識別為 CEF 的郵件僅轉寄到本地主機上的紀錄分析代理
 
## <a name="prerequisites"></a>Prerequisites

- 您必須在指定的 Linux 電腦上具有提升的許可權 (sudo)。
- 必須在 Linux 電腦上安裝 python。<br>使用`python -version`命令進行檢查。
- 在安裝日誌分析代理之前,不能將 Linux 電腦連接到任何 Azure 工作區。

## <a name="run-the-deployment-script"></a>執行部署指令碼
 
1. 在 Azure Sentinel 導航功能表中,按下 **「資料連接器**」。 從連接器清單中,按下 **「常見事件格式 」(CEF)」** 磁貼,然後按下右下角的 **「打開連接器」頁面**按鈕。 

1. 在**1.2 下,在 Linux 電腦上安裝 CEF 收集器**,複製**執行以下文稿下提供的連結以安裝和應用 CEF 收集器**,或從以下文字中複製:

     `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`

1. 文本運行時,請檢查以確保不會收到任何錯誤或警告消息。

繼續[執行步驟 2: 設定安全解決方案以轉發 CEF 訊息](connect-cef-solution-config.md)。

## <a name="deployment-script-explained"></a>已解釋的部署文稿

以下是部署腳本操作的逐命令說明。

選擇系統守護程序以查看相應的說明。

# <a name="rsyslog-daemon"></a>[rsyslog 守護程式](#tab/rsyslog)

1. **下載並安裝紀錄分析代理:**

    - 下載紀錄分析 (OMS) Linux 代理的安裝文稿<br>
        `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh`

    - 安裝紀錄分析代理<br>
        `sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com`

1. **設定 Syslog 伺服程式:**

    1. 使用 syslog`/etc/rsyslog.conf`設定檔 打開埠 514 以進行 TCP 通訊。

    1. 通過將特殊配置檔`security-config-omsagent.conf`插入到 syslog`/etc/rsyslog.d/`守護程序目錄 中,將守護程序配置為將 CEF 消息轉發到 TCP 埠 25226 上的日誌分析代理。

        `security-config-omsagent.conf`檔案的內容:

            :rawmsg, regex, "CEF\|ASA" ~
            *.* @@127.0.0.1:25226

1. **重新啟動 Syslog 伺服程式**

    `service rsyslog restart`

1. **將紀錄分析代理設定設定為偵聽埠 25226 並將 CEF 訊息轉寄到 Azure Sentinel**

    1. 從紀錄分析代理 GitHub 儲存函式庫下載設定<br>
        `wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/omsagent.d/security_events.conf`


    1. 重新啟動紀錄分析代理<br>
        `/opt/microsoft/omsagent/bin/service_control restart [workspaceID]`

# <a name="syslog-ng-daemon"></a>[syslog-ng 守護程式](#tab/syslogng)

1. **下載並安裝紀錄分析代理:**

    - 下載紀錄分析 (OMS) Linux 代理的安裝文稿<br>`wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh`

    - 安裝紀錄分析代理<br>`sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com`

1. **設定 Syslog 伺服程式:**

    1. 使用 syslog`/etc/syslog-ng/syslog-ng.conf`設定檔 打開埠 514 以進行 TCP 通訊。

    1. 通過將特殊配置檔`security-config-omsagent.conf`插入到 syslog`/etc/syslog-ng/conf.d/`守護程序目錄 中,將守護程序配置為將 CEF 消息轉發到 TCP 埠 25226 上的日誌分析代理。

        `security-config-omsagent.conf`檔案的內容:

            filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
            destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
            log {source(s_src);filter(f_oms_filter);destination(oms_destination);};

1. **重新啟動 Syslog 伺服程式**

    `service syslog-ng restart`

1. **將紀錄分析代理設定設定為偵聽埠 25226 並將 CEF 訊息轉寄到 Azure Sentinel**

    1. 從紀錄分析代理 GitHub 儲存函式庫下載設定<br>
        `wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/omsagent.d/security_events.conf`


    1. 重新啟動紀錄分析代理<br>
        `/opt/microsoft/omsagent/bin/service_control restart [workspaceID]`

---

## <a name="next-steps"></a>後續步驟
在本文件中,您學習了如何部署日誌分析代理,將 CEF 設備連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats.md)。


---
title: 驗證 Azure Sentinel 的連線能力 |Microsoft Docs
description: 驗證安全性解決方案的連線，確保 CEF 的訊息會轉送到 Azure Sentinel。
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
ms.openlocfilehash: 6b91e36ee09aa855c119add2c0eb268cf8b97393
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81731823"
---
# <a name="step-3-validate-connectivity"></a>步驟3：驗證連線能力

一旦您已部署記錄轉寄站（在步驟1中），並設定您的安全性解決方案以將 CEF 訊息傳送給它（在步驟2中），請遵循這些指示來驗證您的安全性解決方案與 Azure Sentinel 之間的連線能力。 

## <a name="prerequisites"></a>先決條件

- 您必須在記錄轉寄站電腦上擁有較高的許可權（sudo）。

- 您必須在記錄轉寄站機器上安裝 Python。<br>
使用`python –version`命令來進行檢查。

## <a name="how-to-validate-connectivity"></a>如何驗證連線能力

1. 從 Azure Sentinel 導覽功能表中，開啟 [**記錄**]。 使用**CommonSecurityLog**架構來執行查詢，以查看您是否收到來自安全性解決方案的記錄。<br>
請注意，大約需要20分鐘的時間，記錄才會開始出現在**Log Analytics**中。 

1. 如果您沒有看到查詢的任何結果，請確認事件是從您的安全性解決方案產生，或嘗試產生一些，並確認它們正轉送至您指定的 Syslog 轉寄站機器。 

1. 在記錄轉寄站上執行下列腳本，以檢查您的安全性解決方案、記錄轉寄站和 Azure Sentinel 之間的連線能力。 此腳本會檢查守護程式是否在正確的埠上接聽、是否已正確設定轉送，以及是否有任何內容無法封鎖守護程式與 Log Analytics 代理程式之間的通訊。 它也會傳送 mock 訊息 ' TestCommonEventFormat '，以檢查端對端連線能力。 <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`

## <a name="validation-script-explained"></a>驗證腳本說明

驗證腳本會執行下列檢查：

# <a name="rsyslog-daemon"></a>[rsyslog daemon](#tab/rsyslog)

1. 檢查檔案<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    存在且有效。

1. 檢查檔案是否包含下列文字：

        <source>
            type syslog
            port 25226
            bind 127.0.0.1
            protocol_type tcp
            tag oms.security
            format /(?<time>(?:\w+ +){2,3}(?:\d+:){2}\d+|\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}.[\w\-\:\+]{3,12}):?\s*(?:(?<host>[^: ]+) ?:?)?\s*(?<ident>.*CEF.+?(?=0\|)|%ASA[0-9\-]{8,10})\s*:?(?<message>0\|.*|.*)/
            <parse>
                message_format auto
            </parse>
        </source>

        <filter oms.security.**>
            type filter_syslog_security
        </filter>

1. 檢查電腦上是否有任何可能封鎖網路流量（例如主機防火牆）的安全性增強功能。

1. 檢查 syslog daemon （rsyslog）是否已正確設定，以便將其識別為 CEF （使用 RegEx）的訊息，傳送至 TCP 埠25226上的 Log Analytics 代理程式：

    - 設定檔：`/etc/rsyslog.d/security-config-omsagent.conf`

            :rawmsg, regex, "CEF\|ASA" ~
            *.* @@127.0.0.1:25226

1. 檢查 syslog daemon 是否正在接收埠514上的資料

1. 檢查是否已建立必要的連線： tcp 514 用於接收資料，tcp 25226 用於 syslog daemon 與 Log Analytics 代理程式之間的內部通訊

1. 將 MOCK 資料傳送至 localhost 上的埠514。 您可以執行下列查詢，在 Azure Sentinel 工作區中觀察這項資料：

        CommonSecurityLog
        | where DeviceProduct == "MOCK"

# <a name="syslog-ng-daemon"></a>[syslog-ng daemon](#tab/syslogng)

1. 檢查檔案<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    存在且有效。

1. 檢查檔案是否包含下列文字：

        <source>
            type syslog
            port 25226
            bind 127.0.0.1
            protocol_type tcp
            tag oms.security
            format /(?<time>(?:\w+ +){2,3}(?:\d+:){2}\d+|\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}.[\w\-\:\+]{3,12}):?\s*(?:(?<host>[^: ]+) ?:?)?\s*(?<ident>.*CEF.+?(?=0\|)|%ASA[0-9\-]{8,10})\s*:?(?<message>0\|.*|.*)/
            <parse>
                message_format auto
            </parse>
        </source>

        <filter oms.security.**>
            type filter_syslog_security
        </filter>

1. 檢查電腦上是否有任何可能封鎖網路流量（例如主機防火牆）的安全性增強功能。

1. 檢查 syslog daemon （syslog-ng）是否已正確設定，以便將其識別為 CEF （使用 RegEx）的訊息，傳送至 TCP 埠25226上的 Log Analytics 代理程式：

    - 設定檔：`/etc/syslog-ng/conf.d/security-config-omsagent.conf`

            filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
            destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
            log {source(s_src);filter(f_oms_filter);destination(oms_destination);};

1. 檢查 syslog daemon 是否正在接收埠514上的資料

1. 檢查是否已建立必要的連線： tcp 514 用於接收資料，tcp 25226 用於 syslog daemon 與 Log Analytics 代理程式之間的內部通訊

1. 將 MOCK 資料傳送至 localhost 上的埠514。 您可以執行下列查詢，在 Azure Sentinel 工作區中觀察這項資料：

        CommonSecurityLog
        | where DeviceProduct == "MOCK"

---

## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將 CEF 設備連線到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)來監視您的資料。


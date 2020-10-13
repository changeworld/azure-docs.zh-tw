---
title: 驗證 Azure Sentinel 的連線能力 |Microsoft Docs
description: 驗證安全性解決方案的連線，以確保 CEF 的訊息轉送至 Azure Sentinel。
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
ms.openlocfilehash: 643b28b2e88f233d2924270511d3c87fa4d9b767
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91631625"
---
# <a name="step-3-validate-connectivity"></a>步驟3：驗證連線能力

當您在步驟1中部署記錄轉寄站 () 並設定您的安全性解決方案，以在步驟 2)  (傳送 CEF 訊息，請遵循下列指示來確認安全性解決方案與 Azure Sentinel 之間的連線能力。 

## <a name="prerequisites"></a>必要條件

- 您必須在記錄轉寄站電腦上擁有較高的許可權 (sudo) 。

- 您必須在記錄轉寄站電腦上安裝 Python。<br>
使用 `python –version` 命令來檢查。

## <a name="how-to-validate-connectivity"></a>如何驗證連線能力

1. 從 Azure Sentinel 導覽功能表開啟 [ **記錄**檔]。 使用 **CommonSecurityLog** 架構來執行查詢，以查看您是否從安全性解決方案接收記錄。<br>
請注意，在您的記錄開始顯示于 **Log Analytics**之前，可能需要大約20分鐘的時間。 

1. 如果您沒有看到查詢的任何結果，請確認已從您的安全性解決方案產生事件，或嘗試產生一些事件，並確認它們正在轉送至您指定的 Syslog 轉寄站電腦。 

1. 在記錄轉寄站上執行下列腳本，以檢查您的安全性解決方案、記錄轉寄站和 Azure Sentinel 之間的連線能力。 此腳本會檢查守護程式是否正在接聽正確的埠、是否已正確設定轉送，而且沒有任何專案會封鎖 daemon 與 Log Analytics 代理程式之間的通訊。 它也會傳送 mock 訊息 ' TestCommonEventFormat ' 來檢查端對端連線能力。 <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`

## <a name="validation-script-explained"></a>驗證腳本說明

驗證腳本會執行下列檢查：

# <a name="rsyslog-daemon"></a>[rsyslog daemon](#tab/rsyslog)

1. 檢查檔案<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    存在且有效。

1. 檢查檔案是否包含下列文字：

    ```bash
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
    ```

1. 檢查是否已如預期般設定防火牆事件的 Cisco ASA 剖析：

    ```bash
    sed -i "s|return '%ASA' if ident.include?('%ASA')|return ident if ident.include?('%ASA')|g" 
        /opt/microsoft/omsagent/plugin/security_lib.rb && 
        sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
    ```

1. 檢查 syslog 來源中的 *電腦* 欄位是否已正確對應至 Log Analytics 代理程式：

    ```bash
    sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" 
        -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/pl ugin/
        filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
    ```

1. 檢查電腦上是否有任何安全性增強功能可能會封鎖網路流量 (例如主機防火牆) 。

1. 檢查 syslog 背景程式 (rsyslog) 是否已正確設定為傳送訊息， (將其識別為 CEF) 至 TCP 埠25226上的 Log Analytics 代理程式：

    - 設定檔： `/etc/rsyslog.d/security-config-omsagent.conf`

        ```bash
        if $rawmsg contains "CEF:" or $rawmsg contains "ASA-" then @@127.0.0.1:25226 
        ```

1. 重新開機 syslog daemon 和 Log Analytics 代理程式：

    ```bash
    service rsyslog restart

    /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
    ```

1. 檢查是否已建立必要的連線： tcp 514 用於接收資料，tcp 25226 用於 syslog daemon 和 Log Analytics 代理程式之間的內部通訊：

    ```bash
    netstat -an | grep 514

    netstat -an | grep 25226
    ```

1. 檢查 syslog daemon 是否正在接收埠514上的資料，以及代理程式是否正在接收埠25226上的資料：

    ```bash
    sudo tcpdump -A -ni any port 514 -vv

    sudo tcpdump -A -ni any port 25226 -vv
    ```

1. 將 MOCK 資料傳送到 localhost 上的埠514。 您可以藉由執行下列查詢，在 Azure Sentinel 工作區中觀察這項資料：

    ```kusto
    CommonSecurityLog
    | where DeviceProduct == "MOCK"
    ```

# <a name="syslog-ng-daemon"></a>[syslog-ng daemon](#tab/syslogng)

1. 檢查檔案<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    存在且有效。

1. 檢查檔案是否包含下列文字：

    ```bash
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
    ```

1. 檢查是否已如預期般設定防火牆事件的 Cisco ASA 剖析：

    ```bash
    sed -i "s|return '%ASA' if ident.include?('%ASA')|return ident if ident.include?('%ASA')|g" 
        /opt/microsoft/omsagent/plugin/security_lib.rb && 
        sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
    ```

1. 檢查 syslog 來源中的 *電腦* 欄位是否已正確對應至 Log Analytics 代理程式：

    ```bash
    sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" 
        -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/pl ugin/
        filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
    ```

1. 檢查電腦上是否有任何安全性增強功能可能會封鎖網路流量 (例如主機防火牆) 。

1. 檢查 syslog daemon (syslog-ng) 是否已正確設定，以使用 RegEx) 在 TCP 埠25226上使用 Log Analytics 代理程式來傳送識別為 CEF (的訊息：

    - 設定檔： `/etc/syslog-ng/conf.d/security-config-omsagent.conf`

        ```bash
        filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
        destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
        log {source(s_src);filter(f_oms_filter);destination(oms_destination);};
        ```

1. 重新開機 syslog daemon 和 Log Analytics 代理程式：

    ```bash
    service syslog-ng restart

    /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
    ```

1. 檢查是否已建立必要的連線： tcp 514 用於接收資料，tcp 25226 用於 syslog daemon 和 Log Analytics 代理程式之間的內部通訊：

    ```bash
    netstat -an | grep 514

    netstat -an | grep 25226
    ```

1. 檢查 syslog daemon 是否正在接收埠514上的資料，以及代理程式是否正在接收埠25226上的資料：

    ```bash
    sudo tcpdump -A -ni any port 514 -vv

    sudo tcpdump -A -ni any port 25226 -vv
    ```

1. 將 MOCK 資料傳送到 localhost 上的埠514。 您可以藉由執行下列查詢，在 Azure Sentinel 工作區中觀察這項資料：

    ```kusto
    CommonSecurityLog
    | where DeviceProduct == "MOCK"
    ```
---

## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將 CEF 設備連線至 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。


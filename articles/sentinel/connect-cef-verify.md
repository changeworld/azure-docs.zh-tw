---
title: 驗證與 Azure 哨兵的連接 |微軟文件
description: 驗證安全解決方案的連接性,以確保 CEF 消息被轉發到 Azure Sentinel。
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
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731823"
---
# <a name="step-3-validate-connectivity"></a>第三步:驗證連線

部署日誌轉寄站(在步驟 1 中)並配置安全解決方案以向其發送 CEF 消息(在步驟 2 中),請按照這些說明驗證安全解決方案和 Azure Sentinel 之間的連接。 

## <a name="prerequisites"></a>Prerequisites

- 日誌轉寄器電腦上必須具有提升的許可權 (sudo)。

- 必須在日誌轉寄器電腦上安裝了 Python。<br>
使用`python –version`命令進行檢查。

## <a name="how-to-validate-connectivity"></a>如何驗證連線

1. 從 Azure Sentinel 瀏覽選單中,開啟**紀錄**。 使用**通用安全日誌**架構運行查詢,以查看是否從安全解決方案接收日誌。<br>
請注意,可能需要大約 20 分鐘,直到日誌開始出現在**日誌分析**中。 

1. 如果看不到查詢的任何結果,請驗證事件是否從安全解決方案生成,或者嘗試生成一些事件,並驗證它們正轉發到您指定的 Syslog 轉發器電腦。 

1. 在日誌轉寄器上運行以下腳本,以檢查安全解決方案、日誌轉寄器和 Azure Sentinel 之間的連接。 此腳本檢查守護進程是否偵聽正確的埠,轉發是否配置正確,並且沒有任何內容阻止守護進程和日誌分析代理之間的通信。 它還發送類比消息「TestCommonEventFormat」以檢查端到端連接。 <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`

## <a name="validation-script-explained"></a>驗證文稿解釋

驗證文稿執行以下檢查:

# <a name="rsyslog-daemon"></a>[rsyslog 守護程式](#tab/rsyslog)

1. 檢查檔案<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    存在且有效。

1. 檢查此檔案中的檔案中包含以下文字:

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

1. 檢查電腦上是否有任何可能阻止網路流量(如主機防火牆)的安全增強功能。

1. 檢查 syslog 伺服程式 (rsyslog) 是否正確設定為 TCP 連接埠 25226 上的紀錄分析代理傳送其識別為 CEF(使用正規表示式)的消息:

    - 設定檔:`/etc/rsyslog.d/security-config-omsagent.conf`

            :rawmsg, regex, "CEF\|ASA" ~
            *.* @@127.0.0.1:25226

1. 檢查 syslog 伺服程式是否接收埠 514 的資料

1. 檢查是否建立了必要的連線:用於接收資料的 tcp 514,用於 syslog 守護行程和日誌分析代理之間的內部通訊的 tcp 25226

1. 將 MOCK 資料發送到本地主機上的連接埠 514。 此資料應在 Azure Sentinel 工作區中透過執行以下查詢來觀察:

        CommonSecurityLog
        | where DeviceProduct == "MOCK"

# <a name="syslog-ng-daemon"></a>[syslog-ng 守護程式](#tab/syslogng)

1. 檢查檔案<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    存在且有效。

1. 檢查此檔案中的檔案中包含以下文字:

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

1. 檢查電腦上是否有任何可能阻止網路流量(如主機防火牆)的安全增強功能。

1. 檢查 syslog 伺服程式 (syslog-ng) 是否正確設定為向 TCP 連接埠 25226 上的紀錄分析代理傳送其識別為 CEF(使用正規表示式)的消息:

    - 設定檔:`/etc/syslog-ng/conf.d/security-config-omsagent.conf`

            filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
            destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
            log {source(s_src);filter(f_oms_filter);destination(oms_destination);};

1. 檢查 syslog 伺服程式是否接收埠 514 的資料

1. 檢查是否建立了必要的連線:用於接收資料的 tcp 514,用於 syslog 守護行程和日誌分析代理之間的內部通訊的 tcp 25226

1. 將 MOCK 資料發送到本地主機上的連接埠 514。 此資料應在 Azure Sentinel 工作區中透過執行以下查詢來觀察:

        CommonSecurityLog
        | where DeviceProduct == "MOCK"

---

## <a name="next-steps"></a>後續步驟
在本文件中,您學習了如何將 CEF 設備連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats.md)。
- [使用工作簿](tutorial-monitor-your-data.md)監視數據。


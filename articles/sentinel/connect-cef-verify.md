---
title: 驗證與 Azure 哨兵的連接*微軟文檔
description: 驗證安全解決方案的連線性，以確保 CEF 消息被轉發到 Azure Sentinel。
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
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: e224f6d5cfd82dfc6cb1ce107d111ee0e031247b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588428"
---
# <a name="step-3-validate-connectivity"></a>第 3 步：驗證連接



部署代理並將安全解決方案配置為轉發 CEF 消息後，請使用本文瞭解如何驗證 Azure Sentinel 和安全解決方案之間的連接。 

## <a name="how-to-validate-connectivity"></a>如何驗證連接

1. 打開日誌分析，以確保使用通用安全性記錄檔架構接收日誌。<br> 可能需要 20 分鐘以上，直到日誌開始出現在日誌分析中。 

1. 在運行腳本之前，我們建議您從安全解決方案發送消息，以確保郵件被轉發到您配置的 Syslog 代理電腦。 
1. 您的電腦上必須具有較高的權限 （sudo）。 請確保電腦上使用以下命令具有 Python：`python –version`
1. 運行以下腳本以檢查代理、Azure Sentinel 和安全解決方案之間的連接。 它檢查守護進程轉發是否配置正確，偵聽正確的埠，並且沒有任何阻止守護進程和日誌分析代理之間的通信。 該腳本還會發送類比消息"TestCommonEventFormat"以檢查端到端連接。 <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`


## <a name="next-steps"></a>後續步驟
在本文檔中，您學習了如何將 CEF 設備連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。


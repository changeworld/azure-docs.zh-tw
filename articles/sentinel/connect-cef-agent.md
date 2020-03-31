---
title: 部署代理以將 CEF 資料連線到 Azure 哨兵預覽*微軟文檔
description: 瞭解如何部署代理以將 CEF 資料連線到 Azure Sentinel。
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
ms.date: 11/26/2019
ms.author: yelevin
ms.openlocfilehash: b0c9335357cb793ea76e1dbe68575f716a50372a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588462"
---
# <a name="step-1-deploy-the-agent"></a>第 1 步：部署代理


在此步驟中，您需要選擇在 Azure Sentinel 和安全解決方案之間充當代理的 Linux 電腦。 您必須在代理電腦上運行腳本：
- 安裝日誌分析代理並根據需要對其進行配置，以偵聽 Syslog 消息。
- 配置 Syslog 守護進程以使用 TCP 埠 514 偵聽 Syslog 消息，然後僅使用 TCP 埠 25226 將 CEF 消息轉發到日誌分析代理。
- 設置 Syslog 代理以收集資料並將其安全地發送到 Azure Sentinel，並對其進行分析和豐富。
 
## <a name="deploy-the-agent"></a>部署代理
 
1. 在 Azure Sentinel 門戶中，按一下 **"資料"連接子**並選擇 **"常見事件格式 "（CEF），** 然後**選擇"打開連接器"頁**。 

1. 在 **"安裝和配置 Syslog 代理**"下，選擇電腦類型（Azure、其他雲或本地）。 
   > [!NOTE]
   > 由於下一步的腳本安裝日誌分析代理並將電腦連接到 Azure Sentinel 工作區，因此請確保此電腦未連接到任何其他工作區。
1. 您的電腦上必須具有較高的權限 （sudo）。 請確保電腦上使用以下命令具有 Python：`python –version`

1. 在代理電腦上運行以下腳本。
   `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`
1. 腳本運行時，請檢查以確保不會收到任何錯誤或警告訊息。

繼續[執行步驟 2：配置安全解決方案以轉發 CEF 消息](connect-cef-solution-config.md)。


## <a name="next-steps"></a>後續步驟
在本文檔中，您學習了如何將 CEF 設備連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats.md)。


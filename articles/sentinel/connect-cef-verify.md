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
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: e224f6d5cfd82dfc6cb1ce107d111ee0e031247b
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588428"
---
# <a name="step-3-validate-connectivity"></a>步驟3：驗證連線能力



在您部署代理程式並設定安全性解決方案以轉送 CEF 訊息之後，請使用本文來瞭解如何確認 Azure Sentinel 與您的安全性解決方案之間的連線能力。 

## <a name="how-to-validate-connectivity"></a>如何驗證連線能力

1. 開啟 Log Analytics，以確保使用 CommonSecurityLog 架構來接收記錄。<br> 可能需要20分鐘的時間，您的記錄才會開始出現在 Log Analytics 中。 

1. 執行腳本之前，建議您從安全性解決方案傳送訊息，以確定它們正轉送至您設定的 Syslog proxy 電腦。 
1. 您的電腦上必須具有更高的許可權（sudo）。 請確定您的電腦上有 Python，並使用下列命令： `python –version`
1. 執行下列腳本，以檢查代理程式、Azure Sentinel 和安全性解決方案之間的連線能力。 它會檢查是否已正確設定背景程式轉送、接聽正確的埠，而且不會封鎖守護程式與 Log Analytics 代理程式之間的通訊。 此腳本也會傳送 mock 訊息 ' TestCommonEventFormat '，以檢查端對端連線能力。 <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`


## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將 CEF 設備連線到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)來監視您的資料。


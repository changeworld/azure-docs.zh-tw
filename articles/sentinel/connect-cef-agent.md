---
title: 部署代理程式，以將 CEF 資料連線至 Azure Sentinel 預覽 |Microsoft Docs
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
ms.date: 11/26/2019
ms.author: yelevin
ms.openlocfilehash: b0c9335357cb793ea76e1dbe68575f716a50372a
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588462"
---
# <a name="step-1-deploy-the-agent"></a>步驟1：部署代理程式


在此步驟中，您必須選取將作為 Azure Sentinel 與安全性解決方案之間的 proxy 的 Linux 機器。 您將必須在 proxy 電腦上執行腳本，其如下所示：
- 會安裝 Log Analytics 代理程式，並視需要設定以接聽 Syslog 訊息。
- 將 Syslog daemon 設定為使用 TCP 埠514接聽 Syslog 訊息，然後使用 TCP 埠25226，只將 CEF 的訊息轉送到 Log Analytics 代理程式。
- 設定 Syslog 代理程式來收集資料，並安全地將它傳送至 Azure Sentinel，並在其中進行剖析和擴充。
 
## <a name="deploy-the-agent"></a>部署代理程式
 
1. 在 Azure Sentinel 入口網站中，按一下 [**資料連線器**]，然後選取 **[一般事件格式（CEF）** ]，然後**開啟 [連接器] 頁面**。 

1. 在 **[安裝和設定 Syslog 代理程式**] 下，選取您的電腦類型： [Azure]、[其他雲端] 或 [內部部署]。 
   > [!NOTE]
   > 因為下一個步驟中的腳本會安裝 Log Analytics 代理程式，並將電腦連接到您的 Azure Sentinel 工作區，請確定這部電腦未連線到任何其他工作區。
1. 您的電腦上必須具有更高的許可權（sudo）。 請確定您的電腦上有 Python，並使用下列命令： `python –version`

1. 在您的 proxy 電腦上執行下列腳本。
   `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`
1. 當腳本正在執行時，請檢查以確定您未收到任何錯誤或警告訊息。

繼續進行[步驟2：設定您的安全性解決方案轉送 CEF 訊息](connect-cef-solution-config.md)。


## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將 CEF 設備連線到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats.md)。


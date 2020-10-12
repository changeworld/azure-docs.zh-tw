---
title: 將 Check Point 資料連線至 Azure Sentinel |Microsoft Docs
description: 設定您的 Check Point 設備以透過 Syslog 代理程式，將 Syslog 訊息以 CEF 格式轉送至您的 Azure Sentinel 工作區。
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
ms.openlocfilehash: 32cbdabef4d89c7fabb47a52ebf0589690b7ab33
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85566052"
---
# <a name="connect-check-point-to-azure-sentinel"></a>將 Check Point 連接到 Azure Sentinel



本文說明如何將 Check Point 設備連接到 Azure Sentinel。 Check Point data connector 可讓您輕鬆地將 Check Point 記錄與 Azure Sentinel 連接、查看儀表板、建立自訂警示，以及改進調查。 在 Azure Sentinel 上使用 Check Point，可讓您深入瞭解組織的網際網路使用方式，並將增強其安全性作業功能。 

## <a name="forward-check-point-logs-to-the-syslog-agent"></a>將 Check Point 記錄轉送到 Syslog 代理程式

設定您的 Check Point 設備以透過 Syslog 代理程式，將 Syslog 訊息以 CEF 格式轉送至 Azure 工作區。

1. 移至 [Check Point 記錄匯出](https://aka.ms/asi-syslog-checkpoint-forwarding)。
1. 向下移至 **基本部署** ，並遵循指示來設定連線，請使用下列指導方針：
   - 將 **Syslog 埠** 設定為 **514** 或您在代理程式上設定的埠。
     - 使用 Syslog 代理程式名稱和 IP 位址，取代 CLI 中的 **名稱** 和 **目標伺服器 IP 位址** 。
     - 將格式設定為 **CEF**。
1. 如果您使用版本 R 77.30 或 R 80.10，請向上移至 **安裝** ，並遵循指示安裝您版本的記錄匯出工具。
1. 繼續進行[步驟3：驗證連線能力](connect-cef-verify.md)。
 

## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將 Check Point 設備連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- [驗證連線能力](connect-cef-verify.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。



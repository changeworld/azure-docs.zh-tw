---
title: 將檢查點資料連線到 Azure Sentinel |Microsoft Docs
description: 瞭解如何將檢查點資料連線到 Azure Sentinel。
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
ms.openlocfilehash: 70836ec557eff1be035d92e8e7db30a882e05fc6
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588411"
---
# <a name="connect-check-point-to-azure-sentinel"></a>將檢查點連接到 Azure Sentinel



本文說明如何將您的檢查點設備連線到 Azure Sentinel。 「檢查點資料」連接器可讓您輕鬆地將您的檢查點記錄與 Azure Sentinel 連線、查看儀表板、建立自訂警示，以及改善調查。 使用 Azure Sentinel 的檢查點可讓您深入瞭解組織的網際網路使用方式，並將增強其安全性作業功能。 

## <a name="forward-check-point-logs-to-the-syslog-agent"></a>將檢查點記錄轉送到 Syslog 代理程式

設定您的檢查點設備，以透過 Syslog 代理程式將 Syslog 訊息以 CEF 格式轉送至您的 Azure 工作區。

1. 移至[檢查點記錄檔匯出](https://aka.ms/asi-syslog-checkpoint-forwarding)。
1. 使用下列指導方針，向下流覽至 [**基本部署**]，並遵循指示來設定連線：
   - 將**Syslog 埠**設定為**514**或您在代理程式上設定的埠。
     - 以 Syslog 代理程式名稱和 IP 位址取代 CLI 中的**名稱**和**目標伺服器 IP 位址**。
     - 將格式設定為**CEF**。
1. 如果您使用的是 R 77.30 或 R 80.10 版本，請向上快到**安裝**，並遵循指示來安裝您版本的記錄匯出程式。
1. 繼續進行[步驟3：驗證連線能力](connect-cef-verify.md)。
 

## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將檢查點設備連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- [驗證連線能力](connect-cef-verify.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)來監視您的資料。



---
title: 將 Infoblox 網路識別作業系統 (NIOS) 資料連線至 Azure Sentinel |Microsoft Docs
description: 瞭解如何將 Infoblox 網路識別作業系統 (NIOS) 資料連線至 Azure Sentinel。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 28abb9f09e3bca2522b959c6a9b890de5320b17a
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567443"
---
# <a name="connect-your-infoblox-nios-to-azure-sentinel"></a>將您的 Infoblox NIOS 連線至 Azure Sentinel

> [!IMPORTANT]
> Azure Sentinel 中的 Infoblox NIOS 資料連線器目前處於公開預覽狀態。
> 這項功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文說明如何將您的 [Infoblox 網路識別作業系統 (NIOS) 設備](https://www.infoblox.com/glossary/network-identity-operating-system-nios/) 連線至 Azure Sentinel。 Infoblox NIOS data connector 可讓您輕鬆地將 Infoblox 記錄與 Azure Sentinel 連線、查看儀表板、建立自訂警示，以及改進調查。 Infoblox NIOS 與 Azure Sentinel 之間的整合會使用 Syslog。

> [!NOTE]
> 資料會儲存在您執行 Azure Sentinel 之工作區的地理位置。

## <a name="forward-infoblox-logs-to-the-syslog-agent"></a>將 Infoblox 記錄轉送到 Syslog 代理程式  

設定 Infoblox 以透過 Syslog 代理程式將 Syslog 訊息轉寄至 Azure Sentinel 工作區。

1. 在 Azure Sentinel 入口網站中，按一下 [ **資料連線器** ]，然後選取 [ **Infoblox NIOS** 連接器]。

1. 選取 [ **開啟連接器] 頁面**。

1. 遵循 [ **INFOBLOX NIOS** ] 頁面上的指示。

## <a name="find-your-data"></a>尋找您的資料

建立成功的連線之後，資料就會出現在 Syslog 下的 Log Analytics 中。

## <a name="validate-connectivity"></a>驗證連線能力

最多可能需要20分鐘的時間，記錄才會出現在 Log Analytics 中。 

## <a name="next-steps"></a>後續步驟

在本檔中，您已瞭解如何將 Infoblox NIOS 連線至 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：

- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。
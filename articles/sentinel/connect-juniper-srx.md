---
title: 將刺柏的網路 SRX 資料連線到 Azure Sentinel |Microsoft Docs
description: 瞭解如何使用刺柏 SRX 資料連線器，將刺柏 SRX 記錄提取至 Azure Sentinel。 查看活頁簿中的刺柏 SRX 資料、建立警示及改進調查。
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
ms.date: 01/17/2021
ms.author: yelevin
ms.openlocfilehash: 0583846fcd847e92a6f001dae828d31d9d11cb00
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567870"
---
# <a name="connect-your-juniper-srx-firewall-to-azure-sentinel"></a>將您的刺柏 SRX 防火牆連線到 Azure Sentinel

> [!IMPORTANT]
> 刺柏 SRX 連接器目前為 **預覽** 狀態。 請參閱 [Microsoft Azure 預覽的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) ，以取得適用于 Azure 功能（Beta、預覽或尚未發行正式運作）的其他法律條款。

本文說明如何將您的刺柏 SRX 防火牆設備連線至 Azure Sentinel。 刺柏 SRX 資料連線器可讓您輕鬆地將 SRX 記錄與 Azure Sentinel 連接，以便您可以在活頁簿中查看資料、使用它來建立自訂警示，並將其納入以改善調查。 在刺柏 SRX 和 Azure Sentinel 之間的整合會使用 Syslog。

> [!NOTE]
> 資料會儲存在您執行 Azure Sentinel 之工作區的地理位置。

## <a name="prerequisites"></a>Prerequisites

- 您必須擁有 Azure Sentinel 工作區的 [讀取] 和 [寫入] 許可權。

- 您的刺柏 SRX 解決方案必須設定為透過 Syslog 匯出記錄。

## <a name="forward-juniper-srx-logs-to-the-syslog-agent"></a>將刺柏的 SRX 記錄轉送到 Syslog 代理程式  

設定刺柏 SRX，透過 Syslog 代理程式將 Syslog 訊息轉送至 Azure Sentinel 工作區。

1. 在 Azure Sentinel 導覽功能表中，選取 [ **資料連線器**]。

1. 從 **資料連線器** 資源庫中，選取 [ **刺柏 SRX (Preview)** 連接器]，然後 **開啟 [連接器] 頁面**。

1. 遵循 [ **刺柏 SRX** connector] 頁面上的指示：

    1. 安裝 Linux 代理程式並將其上線

        - 選擇 Azure Linux VM 或非 Azure Linux 機器 (實體或虛擬) 。

    1. 設定要收集的記錄

        - 在工作區的 [advanced settings] 設定中選取設備和嚴重性

    1. 設定和連接刺柏 SRX

        - 請遵循下列指示，將刺柏 SRX 設定為轉寄 syslog。
            - [ (安全性原則記錄) 的流量記錄 ](https://kb.juniper.net/InfoCenter/index?page=content&id=KB16509&actp=METADATA)
            - [系統記錄檔](https://kb.juniper.net/InfoCenter/index?page=content&id=kb16502)
        - 若是遠端伺服器，請使用您安裝 Linux 代理程式的 Linux 電腦 IP 位址。

## <a name="find-your-data"></a>尋找您的資料

建立成功的連線之後，資料就會出現在 Syslog 下的 Log Analytics 中。

請參閱連接器頁面中的 [ **下一個步驟** ] 索引標籤，以取得一些實用的範例查詢。

## <a name="validate-connectivity"></a>驗證連線能力

最多可能需要20分鐘的時間，記錄才會出現在 Log Analytics 中。

## <a name="next-steps"></a>後續步驟

在本檔中，您已瞭解如何將刺柏 SRX 連接至 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：

- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。

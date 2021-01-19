---
title: 將 Squid Proxy 資料連線到 Azure Sentinel |Microsoft Docs
description: 瞭解如何使用 Squid Proxy 資料連線器，將 Squid Proxy 記錄提取至 Azure Sentinel。 查看活頁簿中的 Squid Proxy 資料、建立警示及改進調查。
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
ms.openlocfilehash: b183abf8d42e6f4b1c43db2d87b2650721e0c2a9
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567901"
---
# <a name="connect-your-squid-proxy-to-azure-sentinel"></a>將您的 Squid Proxy 連線至 Azure Sentinel

> [!IMPORTANT]
> Squid Proxy 連接器目前為 **預覽** 狀態。 請參閱 [Microsoft Azure 預覽的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) ，以取得適用于 Azure 功能（Beta、預覽或尚未發行正式運作）的其他法律條款。

本文說明如何將您的 Squid Proxy 應用裝置連線至 Azure Sentinel。 Squid Proxy 資料連線器可讓您輕鬆地將您的 Squid 記錄檔與 Azure Sentinel 連線，讓您可以在活頁簿中查看資料、使用它來建立自訂警示，並將其納入以改善調查。 在 Squid Proxy 與 Azure Sentinel 之間的整合會使用 Syslog。

> [!NOTE]
> 資料會儲存在您執行 Azure Sentinel 之工作區的地理位置。

## <a name="prerequisites"></a>Prerequisites

- 您必須擁有 Azure Sentinel 工作區的 [讀取] 和 [寫入] 許可權。

## <a name="forward-squid-proxy-logs-to-the-syslog-agent"></a>將 Squid Proxy 記錄轉送到 Syslog 代理程式  

設定 Squid Proxy，以透過 Syslog 代理程式將 Syslog 訊息轉送至您的 Azure 工作區。

1. 在 Azure Sentinel 導覽功能表中，選取 [ **資料連線器**]。

1. 從 [ **資料連線器** 資源庫] 中，選取 [ **Squid Proxy (預覽])** 連接器，然後 **開啟 [連接器] 頁面**。

1. 遵循 [ **Squid Proxy** 連接器] 頁面上的指示：

    1. 安裝 Linux 代理程式並將其上線

        - 選擇 Azure Linux VM 或非 Azure Linux 機器 (實體或虛擬) 。

    1. 設定要收集的記錄

        - 在工作區的 [advanced] 設定中，新增自訂記錄檔類型、上傳範例檔案，並依照指示進行設定。

## <a name="find-your-data"></a>尋找您的資料

建立成功的連接之後，資料就會出現在資料表的 [**自訂記錄**] 的 [**記錄** 檔] 中 `SquidProxy_CL` 。

請參閱連接器頁面中的 [ **下一個步驟** ] 索引標籤，以取得一些實用的範例查詢。

## <a name="validate-connectivity"></a>驗證連線能力

最多可能需要20分鐘的時間，記錄才會出現在 Log Analytics 中。 

## <a name="next-steps"></a>後續步驟

在本檔中，您已瞭解如何將 Squid Proxy 連線至 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：

- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。

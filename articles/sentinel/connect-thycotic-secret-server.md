---
title: 將 Thycotic Secret Server 連線至 Azure Sentinel |Microsoft Docs
description: 瞭解如何使用 Thycotic Secret Server data connector 將 Thycotic Secret Server 記錄提取至 Azure Sentinel。 查看活頁簿中的 Thycotic Secret Server 資料、建立警示及改進調查。
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
ms.date: 12/13/2020
ms.author: yelevin
ms.openlocfilehash: a0056391dddec25511deb7033d37f59db3d835c8
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567874"
---
# <a name="connect-your-thycotic-secret-server-to-azure-sentinel"></a>將您的 Thycotic Secret Server 連線至 Azure Sentinel

> [!IMPORTANT]
> Thycotic Secret Server connector 目前為 **預覽** 狀態。 請參閱 [Microsoft Azure 預覽的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) ，以取得適用于 Azure 功能（Beta、預覽或尚未發行正式運作）的其他法律條款。

本文說明如何將您的 Thycotic Secret Server 設備連線至 Azure Sentinel。 Thycotic Secret Server data connector 可讓您輕鬆地將 Thycotic Secret Server 記錄檔與 Azure Sentinel 連線，以便您可以在活頁簿中查看資料、使用它來建立自訂警示，並加以合併以改善調查。 Thycotic 與 Azure Sentinel 之間的整合會使用 CEF 資料連線器來適當地剖析及顯示 Secret Server Syslog 訊息。

> [!NOTE]
> 資料會儲存在您執行 Azure Sentinel 之工作區的地理位置。

## <a name="prerequisites"></a>Prerequisites

- 您必須擁有 Azure Sentinel 工作區的讀取和寫入權限。

- 您必須具有工作區共用金鑰的讀取權限。

- 您的 Thycotic Secret Server 必須設定為透過 Syslog 匯出記錄。

## <a name="send-thycotic-secret-server-logs-to-azure-sentinel"></a>將 Thycotic Secret Server 記錄傳送給 Azure Sentinel

若要將其登入 Azure Sentinel，請設定 Thycotic Secret Server 以 CEF 格式將 Syslog 訊息傳送到以 Linux 為基礎的記錄轉送伺服器 (執行 rsyslog 或 Syslog) 。 此伺服器將會安裝 Log Analytics 代理程式，而代理程式會將記錄轉送到您的 Azure Sentinel 工作區。

1. 在 Azure Sentinel 導覽功能表中，選取 [ **資料連線器**]。

1. 從 **資料連線器** 資源庫中，選取 [ **Thycotic Secret Server (Preview])**，然後 **開啟 [連接器] 頁面**。

1. 遵循 [**說明**] 索引標籤的 [**設定：**

    1. 小於 **1。Linux Syslog 代理程式** 設定-如果您還沒有執行中的記錄轉寄站，或您需要另一個記錄轉寄站，請執行此步驟。 如需詳細的指示和說明，請參閱 Azure Sentinel 檔中的 [步驟1：部署記錄](connect-cef-agent.md) 轉寄站。

    1. 在 **2。轉寄常見事件格式 (CEF) 記錄到 Syslog 代理** 程式-遵循 Thycotic 的指示來 [設定 Secret Server](https://thy.center/ss/link/syslog)。 這項設定應該包含下列元素：
        - 記錄目的地–記錄轉送伺服器的主機名稱和/或 IP 位址
        - 通訊協定和埠– **TCP 514** (如果另有建議，請務必在記錄轉送伺服器上的 syslog daemon 進行平行變更) 
        - 記錄格式– CEF
        - 記錄檔類型–全部可用

    1. 在 **3。驗證連接** ：在 [連接器] 頁面上複製命令並在記錄轉寄站上執行命令，以驗證資料內嵌。 如需詳細的指示和說明，請參閱 Azure Sentinel 檔中的 [步驟3：驗證連接](connect-cef-verify.md) 。

        最多可能需要20分鐘的時間，記錄才會出現在 Log Analytics 中。

## <a name="find-your-data"></a>尋找您的資料

建立成功的連接之後，資料就會出現在 [ **記錄**] 的 [ **Azure Sentinel** ] 區段的 [ *CommonSecurityLog* ] 資料表中。

若要查詢 Log Analytics 中的 Thycotic Secret Server 資料，請將下列內容複寫到查詢視窗中，並在您選擇的情況下套用其他篩選準則：

```kusto
CommonSecurityLog 
| where DeviceVendor == "Thycotic Software"
```

請參閱連接器頁面中的 [ **下一個步驟** ] 索引標籤，以取得一些有用的活頁簿和查詢範例。

## <a name="next-steps"></a>後續步驟

在本檔中，您已瞭解如何將 Thycotic Secret Server 連線至 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：

- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。
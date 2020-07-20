---
title: 將周邊81資料連線到 Azure Sentinel |Microsoft Docs
description: 瞭解如何將周邊81資料連線到 Azure Sentinel。
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
ms.date: 06/21/2020
ms.author: yelevin
ms.openlocfilehash: a0eb234d9677c479d2cb404b065da408854168e3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85368369"
---
# <a name="connect-your-perimeter-81-activity-logs-to-azure-sentinel"></a>將您的周邊81活動記錄連接到 Azure Sentinel

本文說明如何將您的[周邊81活動記錄](https://www.perimeter81.com/)設備連線到 Azure Sentinel。 周邊81活動記錄連接器可讓您輕鬆地將周邊81資料帶入 Azure Sentinel，讓您可以在活頁簿中查看、使用它來建立自訂警示，並將其納入以改善調查。

> [!NOTE]
> 資料將會儲存在您執行 Azure Sentinel 之工作區的地理位置中。

## <a name="prerequisites"></a>必要條件

- 您必須擁有 [Azure Sentinel] 工作區的 [讀取] 和 [寫入] 許可權。

- 您必須具有工作區共用金鑰的讀取權限。

## <a name="configure-and-connect-perimeter-81-activity-logs"></a>設定及連接周邊81活動記錄

周邊81活動記錄可以將記錄直接整合到 Azure Sentinel，並將其匯出。

1. 在 Azure Sentinel 入口網站中，按一下導覽功能表上的 [**資料連線器**]。

1. 從資源庫選取 [**周邊81活動記錄**]，然後按一下 [**開啟連接器頁面**] 按鈕。

1. 在 [周邊81活動記錄連接器] 頁面上，複製 [**工作區識別碼**] 和 [**主要金鑰**]，並將其貼到周邊81中，[如這裡所指示](https://support.perimeter81.com/hc/en-us/articles/360012680780)。

1. 完成指示之後，您會在 [Azure Sentinel 連接器] 頁面中看到連接的資料類型。

## <a name="find-your-data"></a>尋找您的資料

建立成功的連線之後，資料會顯示在 [ **CustomLogs**Perimeter81_CL] 底下的 [**記錄**] 中  -  ** **。

這可能需要20分鐘的時間，直到您的記錄開始出現為止。

## <a name="next-steps"></a>後續步驟

在本檔中，您已瞭解如何將周邊81活動記錄連接到 Azure Sentinel。 若要充分利用此資料連線器內建的功能，請按一下 [資料連線器] 頁面上的 [**後續步驟**] 索引標籤。 您會在這裡找到現成的活頁簿和一些範例查詢，讓您可以開始尋找有用的資訊。

若要深入了解 Azure Sentinel，請參閱下列文章：

- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。

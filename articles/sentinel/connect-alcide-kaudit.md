---
title: 將 Alcide kAudit 資料連線到 Azure Sentinel |Microsoft Docs
description: 瞭解如何將 Alcide kAudit 資料連線到 Azure Sentinel。
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
ms.openlocfilehash: 121c9258353505b6147aa059d327bc711e9fede4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85368375"
---
# <a name="connect-your-alcide-kaudit-to-azure-sentinel"></a>將您的 Alcide kAudit 連線至 Azure Sentinel

[Alcide kAudit](https://www.alcide.io/kaudit-K8s-forensics/)可協助您識別異常 Kubernetes 行為，並專注于 Kubernetes 缺口和事件，同時減少偵測時間。 本文說明如何將您的 Alcide kAudit 解決方案連接到 Azure Sentinel。 Alcide kAudit 資料連線器可讓您輕鬆地將 kAudit 記錄資料帶入 Azure Sentinel 中，以便在活頁簿中進行查看、使用它來建立自訂警示，並將其納入以改善調查。 Alcide kAudit 和 Azure Sentinel 之間的整合會使用 REST API。

> [!NOTE]
> 資料將會儲存在您執行 Azure Sentinel 之工作區的地理位置中。

## <a name="prerequisites"></a>必要條件

- 您必須擁有 [Azure Sentinel] 工作區的 [讀取] 和 [寫入] 許可權。

- 您必須具有工作區共用金鑰的讀取權限。

## <a name="configure-and-connect-alcide-kaudit"></a>設定及連接 Alcide kAudit

Alcide kAudit 可以將記錄直接匯出至 Azure Sentinel。

1. 在 Azure Sentinel 入口網站中，按一下導覽功能表上的 [**資料連線器**]。

1. 從資源庫中選取 [ **Alcide kAudit** ]，然後按一下 [**開啟連接器頁面**] 按鈕。

1. 遵循《 [Alcide KAudit 安裝指南》](https://get.alcide.io/hubfs/Azure%20Sentinel%20Integration%20with%20kAudit.pdf)中提供的逐步指示。

1. 當系統要求您提供工作區識別碼和主要金鑰時，您可以從 [Alcide kAudit 資料連線器] 頁面複製它們。

    :::image type="content" source="media/connect-alcide-kaudit/alcide-workspace-id-primary-key.png" alt-text="工作區識別碼和主要金鑰":::

## <a name="find-your-data"></a>尋找您的資料

建立成功的連線之後，資料會出現在**CustomLogs**的下列資料類型下的**記錄**中：

- **alcide_kaudit_detections_1_CL** alcide kaudit 偵測 
- **alcide_kaudit_activity_1_CL** alcide kaudit 活動記錄
- **alcide_kaudit_selections_count_1_CL** alcide kaudit 活動計數
- **alcide_kaudit_selections_details_1_CL** alcide kaudit 活動詳細資料

若要在記錄檔中針對 Alcide kAudit 使用相關的架構，請搜尋上述的資料類型。

可能需要20分鐘的時間，您的記錄才會開始出現在 Log Analytics 中。

## <a name="next-steps"></a>後續步驟

在本檔中，您已瞭解如何將 Alcide kAudit 連接到 Azure Sentinel。 若要充分利用此資料連線器內建的功能，請按一下 [資料連線器] 頁面上的 [**後續步驟**] 索引標籤。 您可以在這裡找到一些現成的範例查詢，讓您可以開始尋找有用的資訊。

若要深入了解 Azure Sentinel，請參閱下列文章：

- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。

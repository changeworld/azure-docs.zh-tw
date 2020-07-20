---
title: 將 Azure 活動資料連線至 Azure Sentinel |Microsoft Docs
description: 只要按一下，就能將 Azure 活動記錄串流至 Azure Sentinel。 活動記錄會記錄並顯示 Azure 上的訂用帳戶層級事件。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 8c25baa8-b93b-41da-9e6c-15bb7b5c5511
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2020
ms.author: yelevin
ms.openlocfilehash: 88f363406ec40dfa439a52ad351501d1c64dce95
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85564493"
---
# <a name="connect-data-from-azure-activity-log"></a>從 Azure 活動記錄連接資料

只要按一下，您就可以將記錄從[Azure 活動記錄](../azure-monitor/platform/platform-logs-overview.md)串流至 Azure Sentinel。 「活動記錄」是一種訂用客戶紀錄，可在 Azure 上記錄和顯示訂用帳戶層級的事件，從 Azure Resource Manager 運算元據到服務健康狀態事件的更新。 使用活動記錄，您可以判斷訂用帳戶中資源上執行的任何寫入作業（PUT、POST、DELETE）的「內容、物件和時間」。 您也可以瞭解作業的狀態和其他相關屬性。 活動記錄不包含讀取（GET）作業，或是使用傳統/"RDFE" 模型之資源的作業。 

## <a name="prerequisites"></a>必要條件

- 您的使用者必須具有 Log Analytics 工作區的參與者許可權。
- 您的使用者必須擁有您想要串流至 Azure Sentinel 之記錄的任何訂用帳戶的「讀者」許可權。

## <a name="set-up-the-azure-activity-connector"></a>設定 Azure 活動連接器

1. 從 Azure Sentinel 導覽功能表中，選取 [**資料連線器**]。 從連接器清單中，按一下 [ **Azure 活動**]，然後在右下方的 [**開啟連接器頁面**] 按鈕上。

2. 在 [**指示**] 索引標籤下，按一下 [**設定 Azure 活動記錄 >** ] 連結。

3. 在 [ **Azure 活動記錄**檔] 窗格中，選取您想要將其記錄串流至 Azure Sentinel 的訂用帳戶。 

4. 在開啟右側的 [訂用帳戶] 窗格中，按一下 **[連接]**。

5. 若要在 Log Analytics 中使用適用于 Azure 活動警示的相關架構，請 `AzureActivity` 在查詢視窗中輸入。

## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將 Azure 活動記錄連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 使用[內建](tutorial-detect-threats-built-in.md)或[自訂](tutorial-detect-threats-custom.md)規則，開始偵測 Azure Sentinel 的威脅。

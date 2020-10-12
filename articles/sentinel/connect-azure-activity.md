---
title: 將 Azure 活動資料連接到 Azure Sentinel |Microsoft Docs
description: 只要按一下，就能將 Azure 活動記錄串流至 Azure Sentinel。 活動記錄會記錄並顯示跨 Azure 的訂用帳戶層級事件。
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85564493"
---
# <a name="connect-data-from-azure-activity-log"></a>從 Azure 活動記錄連接資料

只要按一下，您就可以將 [Azure 活動記錄](../azure-monitor/platform/platform-logs-overview.md) 中的記錄串流至 Azure Sentinel。 活動記錄是一種訂用客戶紀錄，可記錄並顯示 Azure 中的訂用帳戶層級事件，從 Azure Resource Manager 運算元據到服務健康狀態事件的更新。 您可以使用活動記錄檔來判斷任何寫入作業的「內容、物件和時間」 (PUT、POST、DELETE) 在訂用帳戶中的資源上執行。 您也可以瞭解作業的狀態和其他相關屬性。 活動記錄不包含讀取 (取得使用傳統/"RDFE" 模型之資源的) 作業或作業。 

## <a name="prerequisites"></a>Prerequisites

- 您的使用者必須具有 Log Analytics 工作區的參與者許可權。
- 您的使用者必須具有您想要串流至 Azure Sentinel 之記錄的任何訂用帳戶的讀取者許可權。

## <a name="set-up-the-azure-activity-connector"></a>設定 Azure 活動連接器

1. 從 Azure Sentinel 導覽功能表中，選取 [ **資料連線器**]。 從連接器清單中，按一下 [ **Azure 活動**]，然後按一下右下方的 [ **開啟連接器頁面** ] 按鈕。

2. 在 [ **說明** ] 索引標籤下，按一下 [ **設定 Azure 活動記錄] >** 連結。

3. 在 [ **Azure 活動記錄** 檔] 窗格中，選取您要將記錄串流至 Azure Sentinel 的訂用帳戶。 

4. 在開啟于右側的 [訂用帳戶] 窗格中，按一下 [連線 **]**。

5. 若要在 Log Analytics 中使用適用于 Azure 活動警示的相關架構，請 `AzureActivity` 在 [查詢] 視窗中輸入。

## <a name="next-steps"></a>接下來的步驟
在本檔中，您已瞭解如何將 Azure 活動記錄連線至 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 使用 [內建](tutorial-detect-threats-built-in.md) 或 [自訂](tutorial-detect-threats-custom.md) 規則開始偵測 Azure Sentinel 的威脅。

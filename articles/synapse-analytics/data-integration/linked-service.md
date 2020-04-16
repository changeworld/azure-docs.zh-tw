---
title: 在 Azure 同步分析中預配和保護連結服務
description: 瞭解如何使用託管 Vnet 預配和保護連結服務
services: synapse-analytics
author: acomet
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 435c3fd6b1e6444fa3a31c68b4d74c2553d2e634
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430548"
---
# <a name="securing-a-linked-service-with-private-links"></a>使用私人連結保護連結服務 

在本文中,您將學習如何使用專用終結點在 Synapse 中保護連結服務。

## <a name="prerequisites"></a>Prerequisites

* **Azure 訂閱**:如果沒有 Azure 訂閱,請先建立一個[免費的 Azure 帳戶](https://azure.microsoft.com/free/)。
* **Azure 儲存帳戶**:使用 Azure 數據湖第 2 代作為*來源*資料儲存。 如果沒有存儲帳戶,請參閱[創建 Azure 存儲帳戶](../../storage/blobs/data-lake-storage-quickstart-create-account.md)以執行創建存儲帳戶的步驟。 確保儲存帳戶具有用於存取它的 Synapse Studio IP 篩選,並且您只允許**選定網路**存取儲存帳戶。 刀片**防火牆和虛擬網路**下的設置應如下所示。

![安全性儲存帳戶](./media/secure-storage-account.png)

## <a name="create-a-linked-service-with-private-links"></a>使用私人連結建立連結服務

在 Azure Synapse 分析中,連結服務是定義與其他服務的連接資訊的位置。 在本節中,您將將 Azure 突觸分析和 Azure 數據湖第 2 代作為連結服務添加。

1. 打開 Azure 同步工作室並轉到 **"管理**"選項卡。
1. 在 **「外部連線**」下,選擇 **「連結的服務**」。。
1. 要添加連結的服務,請按下"**新建**"。
1. 從清單中選擇 Azure 資料儲存湖儲存 Gen2 磁貼,然後單擊「**繼續**」。
1. 請確保啟用**互動式創作**。 啟用可能需要大約 1 分鐘。 
1. 輸入身份驗證憑據。 帳戶金鑰、服務主體和託管標識當前受支援身份驗證類型。 單擊測試連接以驗證您的憑據是否正確。
1. 選擇**測試連接**,它應該失敗,因為存儲帳戶不允許訪問它,沒有創建和批准專用終結點。 在錯誤訊息中,應看到一個連結來創建**私有終結點**,您可以遵循該連結轉到下一部分。 如果遵循該連結,請跳過下一部分。
1. 完成後，請選取 [建立]****。

## <a name="create-a-managed-private-endpoint"></a>建立託管專用終結點

如果測試上述連接時未按下超連結,請遵循以下路徑。 現在,您需要創建一個託管專用終結點,您將連接到上面創建的連結服務。

1. 跳到 **"管理'** 選項卡。
1. 轉到**託管虛擬網路**部分。
1. 選擇 **= 託管**專用終結點下的新增功能。
1. 從清單中選擇 Azure 資料湖儲存 Gen2 磁貼,然後選擇「**繼續**」。
1. 輸入您在上面創建的儲存帳戶的名稱。
1. 選擇 **"創建"**
1. 您應該在等了幾秒鐘后看到創建的專用連結需要批准。

## <a name="approval-of-a-private-link"></a>批准專用連結
1. 選擇上面創建的專用終結點。 您可以在存儲帳戶級別看到允許您批准專用終結點的超連結。 *另一種方法是直接轉到 Azure 門戶存儲帳戶並轉到**專用終結點連接**邊欄選項卡。*
1. 勾選您在 Studio 中創建的專用終結點,然後選擇 **「批准**」。
1. 添加說明並單擊 **"是"**
1. 傳回「**管理**」選項卡「的 **「託管虛擬網路**」部分下的 Synapse 工作室。
1. 大約需要 1 分鐘才能為私有終結點反映批准。

## <a name="check-the-connection-works"></a>檢查連線工作
1. 跳到 **'管理**'選項卡並選擇您建立的連結服務。
1. 確保**互動式創作**處於活動狀態。
1. 選取 [測試連線]****。 您應該看到連接成功。

現在,您已經建立了 Synapse 和連結服務之間的安全和私人連接!

## <a name="next-steps"></a>後續步驟

要進一步瞭解 Synapse 分析中的託管專用終結點,請參閱[Synapse 託管專用終結點的概念](data-integration-data-lake.md)一文。

有關 Synapse 分析的數據整合的詳細資訊,請參閱將資料[引入數據湖](data-integration-data-lake.md)一文。
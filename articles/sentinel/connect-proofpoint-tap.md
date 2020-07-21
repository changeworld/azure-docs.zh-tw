---
title: 將 Proofpoint on demand 目標攻擊防護（點選）資料連線到 Azure Sentinel |Microsoft Docs
description: 瞭解如何將 Proofpoint on demand 目標攻擊防護（點選）資料連線到 Azure Sentinel。
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
ms.openlocfilehash: e4185de879fa6136531e6d4c64908befa1d3bc45
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531181"
---
# <a name="connect-your-proofpoint-tap-to-azure-sentinel-with-azure-function"></a>使用 Azure Function 將您的 Proofpoint on demand 點連接到 Azure Sentinel

Proofpoint on demand 目標攻擊防護（點路）連接器可讓您使用 Azure Sentinel 輕鬆地連接所有[proofpoint on demand](https://www.proofpoint.com/us/products/advanced-threat-protection/targeted-attack-protection)的安全性解決方案記錄，以查看儀表板、建立自訂警示，以及改善調查。 Proofpoint on demand 和 Azure Sentinel 之間的整合會使用 Azure Functions，利用 REST API 來提取記錄資料。

> [!NOTE]
> 資料將會儲存在您執行 Azure Sentinel 之工作區的地理位置中。

## <a name="configure-and-connect-proofpoint-tap"></a>設定並連接 Proofpoint on demand 點

Azure Functions 可以直接從 Proofpoint on demand 點來整合和提取事件和記錄，並將它們轉送到 Azure Sentinel。

1. 在 Azure Sentinel 入口網站中，按一下 [**資料連線器**]，然後選取 **[proofpoint on demand 攻**接頭]。

1. 選取 [**開啟連接器] 頁面**。

1. 依照 [ **proofpoint on demand** ] 頁面上的指示進行。

## <a name="find-your-data"></a>尋找您的資料

成功建立連線之後，資料會顯示在 Log Analytics 的 [ **ProofpointTAPMessagesBlocked_CL**]、[ **ProofpointTAPMessagesDelivered_CL**]、[ **ProofpointTAPClicksPermitted_CL** ] 和 [ **ProofpointTAPClicksBlocked_CL** ] 資料表之下。

## <a name="validate-connectivity"></a>驗證連線能力

可能需要20分鐘的時間，您的記錄才會開始出現在 Log Analytics 中。

## <a name="next-steps"></a>後續步驟

在本檔中，您已瞭解如何使用 Azure 函式應用程式，將 Proofpoint on demand 點連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：

- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。

---
title: 將 Proofpoint on demand 隨選電子郵件安全性資料連線到 Azure Sentinel |Microsoft Docs
description: 瞭解如何使用 Proofpoint on demand 隨選電子郵件安全性資料連線器，將 POD 電子郵件安全性記錄提取至 Azure Sentinel。 查看活頁簿中的 POD 電子郵件安全性資料、建立警示，以及改進調查。
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
ms.openlocfilehash: 0e256f2fe4b8d4275e331e0fe1fadd67e1d96655
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567902"
---
# <a name="connect-your-proofpoint-on-demand-email-security-pod-solution-to-azure-sentinel"></a>將您的 Proofpoint on demand 隨選電子郵件安全性 (POD) 解決方案連線至 Azure Sentinel

> [!IMPORTANT]
> Proofpoint on demand 隨選電子郵件安全性連接器目前為 **預覽** 狀態。 請參閱 [Microsoft Azure 預覽的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) ，以取得適用于 Azure 功能（Beta、預覽或尚未發行正式運作）的其他法律條款。

本文說明如何將您的 Proofpoint on demand 隨選電子郵件安全性應用裝置連線到 Azure Sentinel。 POD data connector 可讓您輕鬆地將 POD 記錄與 Azure Sentinel 連接，以便您可以在活頁簿中查看資料、使用它來建立自訂警示，並加以合併以改善調查。  Proofpoint on demand 隨選電子郵件安全性和 Azure Sentinel 會使用 Websocket API 來進行整合。

> [!NOTE]
> 資料會儲存在您執行 Azure Sentinel 之工作區的地理位置。

## <a name="prerequisites"></a>Prerequisites

- 您必須擁有 Azure Sentinel 工作區的 [讀取] 和 [寫入] 許可權。

- 您必須具有工作區共用金鑰的讀取權限。 [深入瞭解工作區金鑰](../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key)。

- 您必須具備 Azure Functions 的讀取和寫入權限，才能建立函數應用程式。 [深入瞭解 Azure Functions](/azure/azure-functions/)。

- 您必須具有下列 Websocket API 認證： ProofpointClusterID、ProofpointToken。 [深入瞭解 WEBSOCKET API](https://proofpointcommunities.force.com/community/s/article/Proofpoint-on-Demand-Pod-Log-API)。

## <a name="configure-and-connect-proofpoint-on-demand-email-security"></a>設定和連接 Proofpoint on demand 隨選電子郵件安全性

Proofpoint on demand 隨選電子郵件安全性可將記錄直接整合並匯出至 Azure Sentinel。

1. 在 Azure Sentinel 導覽功能表中，選取 [ **資料連線器**]。

1. 從 **資料連線器** 資源庫中，選取 [ **Proofpoint on demand 隨選電子郵件安全性] (預覽)** 然後 **開啟 [連接器] 頁面**。

1. 遵循 [連接器] 頁面的 [設定 **] 區段中** 所述的步驟。

## <a name="find-your-data"></a>尋找您的資料

建立成功的連線之後，資料就會出現在 **記錄** 檔中的 [ *自訂記錄* 檔] 下，如下表所示：
- `ProofpointPOD_message_CL`
- `ProofpointPOD_maillog_CL`

請參閱連接器頁面中的 [ **下一個步驟** ] 索引標籤，以取得一些實用的範例查詢。

## <a name="validate-connectivity"></a>驗證連線能力

在記錄開始顯示于 Log Analytics 之前，最多可能需要60分鐘的時間。

## <a name="next-steps"></a>後續步驟

在本檔中，您已瞭解如何將 Proofpoint on demand 隨選電子郵件安全性連接至 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：

- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。

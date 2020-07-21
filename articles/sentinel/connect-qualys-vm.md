---
title: 將 Qualys 弱點管理資料連線到 Azure Sentinel |Microsoft Docs
description: 瞭解如何將 Qualys 弱點管理資料連線至 Azure Sentinel。
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
ms.openlocfilehash: 44002a8f4ab3b644e3530ee2d2fc06a7af271fbe
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531177"
---
# <a name="connect-your-qualys-vm-to-azure-sentinel-with-azure-function"></a>使用 Azure Function 將您的 Qualys VM 連線至 Azure Sentinel

Qualys 弱點管理（VM）連接器可讓您輕鬆地將所有[QUALYS VM](https://www.qualys.com/apps/vulnerability-management/)安全性解決方案記錄與 Azure Sentinel 連線，以查看儀表板、建立自訂警示及改善調查。 Qualys VM 與 Azure Sentinel 之間的整合會使用 Azure Functions，利用 REST API 來提取記錄資料。

> [!NOTE]
> 資料將會儲存在您執行 Azure Sentinel 之工作區的地理位置中。

## <a name="configure-and-connect-qualys-vm"></a>設定並聯機到 Qualys VM

Azure Functions 可以直接從 Qualys VM 整合和提取事件和記錄，並將它們轉送至 Azure Sentinel。

1. 在 Azure Sentinel 入口網站中，按一下 [**資料連線器**]，然後選取 [ **Qualys 弱點管理**連接器]。

1. 選取 [**開啟連接器] 頁面**。

1. 依照 [ **Qualys 弱點管理**] 頁面上的指示進行。

## <a name="find-your-data"></a>尋找您的資料

建立成功的連線之後，資料會顯示在 Log Analytics 的 [ **QualysHostDetection_CL** ] 資料表下。

## <a name="validate-connectivity"></a>驗證連線能力

可能需要20分鐘的時間，您的記錄才會開始出現在 Log Analytics 中。

## <a name="next-steps"></a>後續步驟

在本檔中，您已瞭解如何使用 Azure 函數應用程式將 Qualys VM 連線至 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：

- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。

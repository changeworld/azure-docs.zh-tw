---
title: 連接脈衝將安全資料連線到 Azure Sentinel |Microsoft Docs
description: 瞭解如何將「脈衝連線安全資料」連線到 Azure Sentinel。
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
ms.openlocfilehash: 01d41b20c543262280e215cf4e31fd0cccce877f
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531180"
---
# <a name="connect-your-pulse-connect-secure-to-azure-sentinel"></a>將您的脈衝連接安全連線到 Azure Sentinel

本文說明如何將您的[脈衝 Connect 安全](https://www.pulsesecure.net/products/pulse-connect-secure/)設備連線到 Azure Sentinel。 「脈衝連線安全資料」連接器可讓您輕鬆地將您的脈衝連接安全記錄與 Azure Sentinel 連線，以查看儀表板、建立自訂警示及改善調查。 在「脈衝連線」安全和 Azure Sentinel 之間的整合會使用 Syslog。

> [!NOTE]
> 資料將會儲存在您執行 Azure Sentinel 之工作區的地理位置中。

## <a name="forward-pulse-connect-secure-logs-to-the-syslog-agent"></a>轉送脈衝將安全記錄連線到 Syslog 代理程式  

設定脈衝 Connect Secure，以透過 Syslog 代理程式將 Syslog 訊息轉寄到您的 Azure 工作區。

1. 在 Azure Sentinel 入口網站中，按一下 [**資料連線器**]，然後選取 **[脈衝] [連接安全連線器]** 。

1. 選取 [**開啟連接器] 頁面**。

1. 依照 [**脈衝連線安全]** 頁面上的指示進行。

## <a name="find-your-data"></a>尋找您的資料

建立成功的連線之後，資料會出現在 Syslog 底下的 Log Analytics 中。

## <a name="validate-connectivity"></a>驗證連線能力

可能需要20分鐘的時間，您的記錄才會開始出現在 Log Analytics 中。 

## <a name="next-steps"></a>後續步驟

在本檔中，您已瞭解如何將「脈衝連接安全」連線到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：

- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。
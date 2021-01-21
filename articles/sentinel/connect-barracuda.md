---
title: 將 Barracuda 資料連線到 Azure Sentinel |Microsoft Docs
description: 瞭解如何使用 Barracuda Web 應用程式防火牆 (WAF) 連接器，將 Barracuda 記錄與 Azure Sentinel 連接。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 3b33b4aa-7286-4d79-b461-8e1812edc2e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 1131c8e79d9df2883dcf7e975af9732c21318a49
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98621159"
---
# <a name="connect-your-barracuda-waf-appliance"></a>連接您的 Barracuda WAF 設備 

Barracuda Web 應用程式防火牆 (WAF) 連接器可讓您輕鬆地將 Barracuda 記錄與 Azure Sentinel 連線、查看儀表板、建立自訂警示，以及改進調查。 這可讓您深入瞭解組織的網路，並改善安全性作業功能。 Azure Sentinel 利用 **Barracuda** 與 Log Analytics 代理程式之間的原生整合，以提供順暢的整合。 

> [!NOTE]
> 資料會儲存在您執行 Azure Sentinel 之工作區的地理位置。

## <a name="configure-and-connect-barracuda-waf"></a>設定並連接 Barracuda WAF
Barracuda Web 應用程式防火牆可以透過 Log Analytics 代理程式，將記錄直接整合和匯出至 Azure Sentinel。
1. 移至 [BARRACUDA WAF 設定流程](https://campus.barracuda.com/product/webapplicationfirewall/doc/73696965/configure-the-barracuda-web-application-firewall-to-integrate-with-the-oms-server-and-export-logs/)，並依照指示使用下列參數來設定連接：
    - **工作區識別碼**：從 Azure Sentinel Barracuda 連接器頁面複製工作區識別碼的值。
    - **主鍵**：從 Azure Sentinel Barracuda 連接器頁面複製您的主要金鑰值。
1. 若要在 Log Analytics 中針對 Barracuda 事件使用相關的架構，請搜尋 **CommonSecurityLog** 和 **barracuda_CL**。


## <a name="validate-connectivity"></a>驗證連線能力

可能需要20分鐘的時間，記錄才會開始出現在 Log Analytics 中。 



## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將 Barracuda 設備連線至 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。



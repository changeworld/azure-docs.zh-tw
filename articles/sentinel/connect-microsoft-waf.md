---
title: 將 Web 應用程式防火牆資料連線到 Azure 哨兵
description: 瞭解如何將 Microsoft Web 應用程式防火牆資料連線到 Azure Sentinel。
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: a5cef16694fa2cfae036152d22cfa4473956fc72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588173"
---
# <a name="connect-data-from-microsoft-web-application-firewall"></a>連接來自微軟 Web 應用程式防火牆的資料



可以從 Azure 應用程式閘道的 Microsoft Web 應用程式防火牆 （WAF） 資料流日誌。 此 WAF 可保護應用程式免受常見的 Web 漏洞（如 SQL 注入和跨網站腳本）的影響，並允許您自訂規則以減少誤報。 按照這些說明將 Microsoft Web 應用程式防火牆日誌資料流到 Azure Sentinel 中。


## <a name="prerequisites"></a>Prerequisites

- 現有應用程式閘道資源

## <a name="connect-to-microsoft-web-application-firewall"></a>連接到微軟 Web 應用程式防火牆

如果您已經擁有 Microsoft Web 應用程式防火牆，請確保擁有現有的閘道資源。
部署 Microsoft Web 應用程式防火牆並獲取資料後，警報資料可以輕鬆地資料流到 Azure Sentinel 中。
    
1. 在 Azure Sentinel 門戶中，選擇 **"資料連線器**"。
1. 在"資料連線器"頁中，選擇**WAF**磁貼。
1. 轉到[應用程式閘道資源](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAllResourcesBlade/resourceType/Microsoft.Network%2FapplicationGateways) 並選擇您的 WAF。
    1. 選取 [診斷設定]****。
    1. 選擇 = 在表下**添加診斷設置**。
    1. 在 **"診斷設置"** 頁中，鍵入 **"名稱**"並選擇"**發送到日誌分析**"。
    1. 在**日誌分析工作區**下選擇 Azure 哨兵工作區。
    1. 選擇要分析的日誌類型。 我們建議：應用程式閘道訪問日誌和應用程式閘道防火牆日誌。
1. 要在日誌分析中為 Microsoft Web 應用程式防火牆警報使用相關架構，請搜索**Azure 診斷**。

## <a name="next-steps"></a>後續步驟
在本文檔中，您學習了如何將 Microsoft Web 應用程式防火牆連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。

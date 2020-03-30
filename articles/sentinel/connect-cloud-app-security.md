---
title: 將雲應用安全資料連線到 Azure 哨兵*微軟文檔
description: 瞭解如何將雲應用安全資料連線到 Azure 哨兵。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/23/2019
ms.author: yelevin
ms.openlocfilehash: 348576fbbdd1037f9e2e792218b96bbbecf36668
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588360"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>連接來自微軟雲應用安全的資料 



只需按一下一下，即可將日誌從[雲應用安全資料流](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security)到 Azure Sentinel 中。 此連接使您能夠將警報從雲應用安全資料流到 Azure Sentinel 中。 

## <a name="prerequisites"></a>Prerequisites

- 具有全域管理員或安全管理員許可權的使用者
- 要將雲發現日誌資料流到 Azure Sentinel 中，[請啟用 Azure Sentinel 作為 Microsoft 雲應用安全中的 SIEM。](https://aka.ms/AzureSentinelMCAS)

> [!IMPORTANT]
> 雲發現日誌的引入當前處於公共預覽版中。
> 此功能在沒有服務等級協定的情況下提供，不建議用於生產工作負載。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
 
## <a name="connect-to-cloud-app-security"></a>連線至 Cloud App Security

如果您已經擁有雲應用安全性，請確保它在[您的網路上已啟用](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security)。
如果部署雲應用安全性並引入資料，則警報資料可以輕鬆地資料流到 Azure Sentinel 中。


1. 在 Azure 哨兵中，選擇 **"資料連線器**"，按一下 **"雲應用安全**"磁貼並選擇 **"打開連接器"頁**。

1. 選擇要資料流到 Azure Sentinel 的日誌，您可以選擇 **"警報**"和"**雲發現"日誌**（預覽）。 

1. 按一下 [連線]****。

1. 要在日誌分析中為雲應用安全警報使用相關架構，請搜索**安全警報**。




## <a name="next-steps"></a>後續步驟
在本文檔中，您學習了如何將 Microsoft 雲應用安全性連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats.md)。

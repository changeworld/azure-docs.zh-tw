---
title: 將 Azure AD 資料連線到 Azure 哨兵 |微軟文檔
description: 瞭解如何將 Azure 活動目錄資料連接到 Azure 哨兵。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: be9241a6156621d3f90dbab2da5bebeb463b4232
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588615"
---
# <a name="connect-data-from-azure-active-directory"></a>連接 Azure 活動目錄中的資料



Azure 哨兵使您能夠從 Azure[活動目錄](../active-directory/fundamentals/active-directory-whatis.md)收集資料並將其資料流到 Azure Sentinel 中。 您可以選擇資料流[登錄日誌](../active-directory/reports-monitoring/concept-sign-ins.md)和[稽核記錄](../active-directory/reports-monitoring/concept-audit-logs.md)。

## <a name="prerequisites"></a>Prerequisites

- 如果要從活動目錄匯出登錄資料，則必須具有 Azure AD P1 或 P2 許可證。

- 對要從中資料流日誌的租戶具有全域管理員或安全管理員許可權的使用者。

- 要查看連接狀態，您必須具有訪問 Azure AD 診斷日誌的許可權。 


## <a name="connect-to-azure-ad"></a>連接至 Azure AD

1. 在 Azure 哨兵中，選擇 **"資料"連接器**，然後按一下**Azure 活動目錄**磁貼。

1. 在要資料流到 Azure Sentinel 的日誌旁邊，按一下"**連接**"。

1. 您可以選擇是否希望 Azure AD 中的警報在 Azure Sentinel 中自動建置事件。 在 [建立事件]**** 底下，選取 [啟用]**** 來啟用預設分析規則，以自動從已連線安全性服務中產生的警示建立事件。 接著，您可以在 [分析]**** 下編輯此規則，然後編輯 [有效規則]****。

1. 要在日誌分析中為 Azure AD 警報使用相關架構，請搜索**SigninLogs**和**稽核記錄**。




## <a name="next-steps"></a>後續步驟
在本文檔中，您學習了如何將 Azure AD 連接到 Azure 哨兵。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。

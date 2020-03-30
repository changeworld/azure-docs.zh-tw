---
title: 將 Azure AD 標識保護資料連線到 Azure 哨兵
description: 瞭解如何將 Azure AD 標識保護資料連線到 Azure 哨兵。
author: yelevin
manager: rkarlin
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 11/17/2019
ms.author: yelevin
ms.openlocfilehash: 7d42ff28ddd2d883feb25139096d781efe64d50f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588564"
---
# <a name="connect-data-from-azure-ad-identity-protection"></a>連接來自 Azure AD 標識保護的資料



您可以將[Azure AD 標識保護](https://docs.microsoft.com/azure/information-protection/reports-aip)中的日誌資料流到 Azure Sentinel 中，以將警報資料流到 Azure Sentinel 中，以查看儀表板、創建自訂警報並改進調查。 Azure 活動目錄標識保護提供風險使用者、風險檢測和漏洞的整合視圖，並能夠立即修復風險，並設置策略以自動修復未來事件。 該服務基於 Microsoft 保護消費者身份的經驗，從每天超過 130 億次登錄的信號中獲得極大的準確性。 


## <a name="prerequisites"></a>Prerequisites

- 您必須具有[Azure 活動目錄高級 P1 或 P2 許可證](https://azure.microsoft.com/pricing/details/active-directory/)
- 具有全域管理員或安全管理員許可權的使用者


## <a name="connect-to-azure-ad-identity-protection"></a>連接到 Azure AD 標識保護

如果您已經具有 Azure AD 標識保護，請確保它在[網路上啟用](../active-directory/identity-protection/overview-identity-protection.md)。
如果部署 Azure AD 標識保護並獲取資料，則警報資料可以輕鬆地資料流到 Azure Sentinel 中。


1. 在 Azure 哨兵中，選擇 **"資料"連接器**，然後按一下**Azure AD 標識保護**磁貼。

2. 按一下"**連接**"可開始將 Azure AD 標識保護事件資料流到 Azure 哨兵中。


6. 要在日誌分析中為 Azure AD 標識保護警報使用相關架構，請搜索**安全警報**。

## <a name="next-steps"></a>後續步驟
在本文檔中，您學習了如何將 Azure AD 標識保護連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。

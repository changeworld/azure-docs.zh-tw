---
title: 將 Azure AD 識別保護資料連線到 Azure 哨兵
description: 瞭解如何將 Azure AD 識別保護數據連接到 Azure 哨兵。
author: yelevin
manager: rkarlin
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 11/17/2019
ms.author: yelevin
ms.openlocfilehash: b82ddfef57efaaca0ae43750cd306a63a772b911
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80616831"
---
# <a name="connect-data-from-azure-ad-identity-protection"></a>連接來自 Azure AD 識別保護的資料



您可以將[Azure AD 標誌保護](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection)中的日誌串流式傳輸到 Azure Sentinel 中,以將警報流式傳輸到 Azure Sentinel 中,以檢視儀表板、創建自訂警報並改進調查。 Azure 活動目錄標識保護提供風險使用者、風險檢測和漏洞的整合檢視,並能夠立即修復風險,並設置策略以自動修復未來事件。 該服務基於 Microsoft 保護消費者身份的經驗,從每天超過 130 億次登錄的信號中獲得極大的準確性。 


## <a name="prerequisites"></a>Prerequisites

- 您必須具有[Azure 的目錄進階 P1 或 P2 許可證](https://azure.microsoft.com/pricing/details/active-directory/)
- 具有全域管理員或安全管理員權限的使用者


## <a name="connect-to-azure-ad-identity-protection"></a>連接到 Azure AD 識別保護

如果您已經具有 Azure AD 識別保護,請確保它在[網路上啟用](../active-directory/identity-protection/overview-identity-protection.md)。
如果部署 Azure AD 識別保護並獲取數據,則警報數據可以輕鬆地流式傳輸到 Azure Sentinel 中。


1. 在 Azure 哨兵中,選擇 **「資料」連接器**,然後單擊**Azure AD 標識保護**磁貼。

2. 按下「**連接**」可開始將 Azure AD 識別保護事件流式傳輸到 Azure 哨兵中。


6. 要在紀錄分析中為 Azure AD 識別保護警報使用相關架構,請搜尋**安全警報**。

## <a name="next-steps"></a>後續步驟
在本文件中,您學習了如何將 Azure AD 識別保護連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。

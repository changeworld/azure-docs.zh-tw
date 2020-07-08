---
title: 將 Azure Active Directory 資料連線到 Azure Sentinel |Microsoft Docs
description: 瞭解如何從 Azure Active Directory 收集資料，並將 Azure AD 登入記錄和 audit 記錄串流至 Azure Sentinel。
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
ms.openlocfilehash: 37106517c47c86f4a4a562eebd6d120e31e22334
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85564532"
---
# <a name="connect-data-from-azure-active-directory-azure-ad"></a>從 Azure Active Directory 連接資料（Azure AD）



Azure Sentinel 可讓您從[Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)收集資料，並將它串流至 Azure Sentinel。 您可以選擇串流[登入記錄](../active-directory/reports-monitoring/concept-sign-ins.md)和[審核記錄](../active-directory/reports-monitoring/concept-audit-logs.md)。

## <a name="prerequisites"></a>必要條件

- 如果您想要從 Azure AD 匯出登入資料，您必須具有 Azure AD P1 或 P2 授權。

- 在您想要串流處理記錄的租使用者上具有全域管理員或安全性系統管理員許可權的使用者。

- 若要能夠查看線上狀態，您必須擁有存取 Azure AD 診斷記錄的許可權。 


## <a name="connect-to-azure-active-directory"></a>連接至 Azure Active Directory

1. 在 Azure Sentinel 中，從導覽功能表選取 [**資料連線器**]。

1. 從資料連線器資源庫中，選取**Azure Active Directory** ，然後按一下 [**開啟連接器] 頁面**按鈕。

1. 將您要串流的記錄旁的核取方塊標記為 Azure Sentinel，然後按一下 [**連接]**。

1. 您可以選取是否要讓警示 Azure AD 在 Azure Sentinel 中自動產生事件。 在 [建立事件] 底下，選取 [啟用] 來啟用預設分析規則，以自動從已連線安全性服務中產生的警示建立事件。 接著，您可以在 [分析] 下編輯此規則，然後編輯 [有效規則]。

1. 若要在 Log Analytics 中使用相關的架構來查詢 Azure AD 警示，請 `SigninLogs` `AuditLogs` 在查詢視窗中輸入或。




## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將 Azure Active Directory 連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。

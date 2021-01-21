---
title: 將 Azure Active Directory 資料連線至 Azure Sentinel |Microsoft Docs
description: 瞭解如何從 Azure Active Directory 收集資料，並將 Azure AD 登入記錄和審核記錄串流至 Azure Sentinel。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: 9700e5d9179f7c1e33b2371eea89be9bb1c8d08f
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98621357"
---
# <a name="connect-data-from-azure-active-directory-azure-ad"></a>將資料從 Azure Active Directory 的 (Azure AD) 

您可以使用 Azure Sentinel 的內建連接器，從 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) 收集資料，並將其串流至 Azure Sentinel。 連接器可讓您串流登 [入記錄](../active-directory/reports-monitoring/concept-sign-ins.md) 和 [審核記錄](../active-directory/reports-monitoring/concept-audit-logs.md)。

## <a name="prerequisites"></a>必要條件

- 您必須有 [Azure AD Premium P2](https://azure.microsoft.com/pricing/details/active-directory/) 訂用帳戶，才能將登入記錄內嵌至 Azure Sentinel。 Azure 監視器 (Log Analytics) 和 Azure Sentinel 可能會收取額外的每 gb 費用。

- 您的使用者必須被指派工作區上的 Azure Sentinel 參與者角色。

- 您必須將您想要從中串流記錄的租使用者上的全域管理員或安全性系統管理員角色指派給您的使用者。

- 您的使用者必須具有 Azure AD 診斷設定的 [讀取] 和 [寫入] 許可權，才能夠看到線上狀態。 

## <a name="connect-to-azure-active-directory"></a>連接至 Azure Active Directory

1. 在 Azure Sentinel 中，從導覽功能表中選取 [ **資料連線器** ]。

1. 從資料連線器資源庫中，選取 **Azure Active Directory** 然後選取 [ **開啟連接器] 頁面**。

1. 將您想要串流的記錄旁的核取方塊標示為 Azure Sentinel，然後按一下 [連線 **]**。

1. 您可以選取是否要 Azure AD 中的警示自動產生 Azure Sentinel 的事件。 在 [ **建立事件** ] 底下，選取 [ **啟用** ] 以啟用預設分析規則，此規則會自動從已連線的安全性服務中產生的警示建立事件。 接著，您可以在 [分析] 下編輯此規則，然後編輯 [有效規則]。

1. 若要在 Log Analytics 中使用相關的架構來查詢 Azure AD 警示，請 `SigninLogs` `AuditLogs` 在查詢視窗中輸入或。

## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將 Azure Active Directory 連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。

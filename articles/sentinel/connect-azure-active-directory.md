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
ms.openlocfilehash: 409a316bd9c4222dd9b8ff30e42e37d23805c38b
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98757757"
---
# <a name="connect-data-from-azure-active-directory-azure-ad"></a>將資料從 Azure Active Directory 的 (Azure AD) 

您可以使用 Azure Sentinel 的內建連接器，從 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) 收集資料，並將其串流至 Azure Sentinel。 連接器可讓您串流登 [入記錄](../active-directory/reports-monitoring/concept-sign-ins.md) 和 [審核記錄](../active-directory/reports-monitoring/concept-audit-logs.md)。

## <a name="prerequisites"></a>必要條件

- 任何 Azure AD 授權 (Free/O365/P1/P2) 都足以將登入記錄內嵌至 Azure Sentinel。 Azure 監視器 (Log Analytics) 和 Azure Sentinel 可能會收取額外的每 gb 費用。

- 您的使用者必須被指派工作區上的 Azure Sentinel 參與者角色。

- 您必須將您想要從中串流記錄的租使用者上的全域管理員或安全性系統管理員角色指派給您的使用者。

- 您的使用者必須具有 Azure AD 診斷設定的 [讀取] 和 [寫入] 許可權，才能夠看到線上狀態。 

## <a name="connect-to-azure-active-directory"></a>連接至 Azure Active Directory

1. 在 Azure Sentinel 中，從導覽功能表中選取 [ **資料連線器** ]。

1. 從資料連線器資源庫中，選取 **Azure Active Directory** 然後選取 [ **開啟連接器] 頁面**。

1. 將您想要串流至 Azure Sentinel 的記錄檔類型旁的核取方塊標記，然後按一下 **[連接]**。 以下是您可以選擇的記錄類型：

    - **登入記錄**：使用受控應用程式和使用者登入活動的相關資訊。
    - **Audit logs**：有關使用者和群組管理、受控應用程式和目錄活動的系統活動資訊。
    - **非互動式使用者登入記錄**：用戶端代表使用者執行的登入相關資訊，不需要使用者進行任何互動或驗證因素。
    - **服務主體登入記錄**：依應用程式和服務主體登入不牽涉到任何使用者的相關資訊。 在這些登入中，應用程式或服務會代表自己提供認證，以驗證或存取資源。
    - **受控識別登入記錄**： azure 資源的登入，這些資源具有由 azure 管理的秘密。
    - 布建 **記錄**：有關 Azure AD 布建服務所布建之使用者、群組和角色的系統活動資訊。

## <a name="find-your-data"></a>尋找您的資料

建立成功的連接之後，資料會顯示在 [ **記錄**] 中的 [ **LogManagement** ] 區段下，如下表所示：

- `SigninLogs`
- `AuditLogs`
- `AADNonInteractiveUserSignInLogs`
- `AADServicePrincipalSignInLogs`
- `AADManagedIdentitySignInLogs`
- `AADProvisioningLogs`

若要查詢 Azure AD 記錄，請在查詢視窗的頂端輸入相關的資料表名稱。

## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將 Azure Active Directory 連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。

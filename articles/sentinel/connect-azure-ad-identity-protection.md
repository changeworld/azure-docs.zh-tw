---
title: 將 Azure AD Identity Protection 資料連線至 Azure Sentinel
description: 瞭解如何將 Azure AD Identity Protection 中的記錄和警示串流至 Azure Sentinel 以查看儀表板、建立自訂警示，以及改進調查。
author: yelevin
manager: rkarlin
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 06/24/2020
ms.author: yelevin
ms.openlocfilehash: f420e80f10072c440f5401c042d6370a061b1a7f
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632236"
---
# <a name="connect-data-from-azure-active-directory-azure-ad-identity-protection"></a>將資料從 Azure Active Directory (Azure AD) Identity Protection

您可以將 [Azure AD Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) 中的記錄串流至 Azure Sentinel，將警示串流至 Azure Sentinel 以查看儀表板、建立自訂警示，以及改進調查。 Azure Active Directory Identity Protection 提供風險使用者、風險偵測和弱點的匯總視圖，並能夠立即補救風險，並設定原則以自動補救未來的事件。 這項服務是以 Microsoft 的經驗為基礎，可保護取用者身分識別，並從每日超過13000000000的登入信號獲得極高的精確度。 

## <a name="prerequisites"></a>必要條件

- 您必須有 [Azure AD Premium P2 訂](https://azure.microsoft.com/pricing/details/active-directory/)用帳戶。
- 您必須擁有具有全域管理員或安全性系統管理員許可權的使用者。

## <a name="connect-to-azure-ad-identity-protection"></a>連接到 Azure AD Identity Protection

如果您有 Azure AD Premium P2 訂用帳戶，則會包含 Azure AD Identity Protection。 如果 [已啟用任何原則](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 並產生警示，則可以輕鬆地將警示資料串流至 Azure Sentinel。

1. 在 Azure Sentinel 中，選取 [ **資料連線器** ]，然後按一下 [ **Azure AD Identity Protection** ] 磚。

1. 按一下 **[連線]** 開始將 Azure AD Identity Protection 事件串流至 Azure Sentinel。

1. 若要在 Log Analytics 中針對 Azure AD Identity Protection 警示使用相關的架構，請搜尋 **SecurityAlert**。

如果您想要測試連接器，您可以 [模擬](../active-directory/identity-protection/howto-identity-protection-simulate-risk.md) 偵測，以產生將串流至 Azure Sentinel 的範例警示。

## <a name="next-steps"></a>下一步

在本檔中，您已瞭解如何將 Azure AD Identity Protection 連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。

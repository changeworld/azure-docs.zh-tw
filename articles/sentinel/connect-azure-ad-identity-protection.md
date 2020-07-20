---
title: 將 Azure AD Identity Protection 資料連線到 Azure Sentinel
description: 瞭解如何將記錄和警示從 Azure AD Identity Protection 串流至 Azure Sentinel 以查看儀表板、建立自訂警示，以及改善調查。
author: yelevin
manager: rkarlin
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 06/24/2020
ms.author: yelevin
ms.openlocfilehash: 69ab76bf213653ea10db8dfd181b615a7e0f47b5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85564477"
---
# <a name="connect-data-from-azure-active-directory-azure-ad-identity-protection"></a>從 Azure Active Directory （Azure AD） Identity Protection 連接資料

您可以將[Azure AD Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)的記錄串流至 Azure Sentinel，以將警示串流至 Azure Sentinel 以查看儀表板、建立自訂警示，以及改善調查。 Azure Active Directory Identity Protection 提供有風險的使用者、風險偵測和弱點的匯總視圖，並能夠立即補救風險，以及設定原則來自動補救未來的事件。 這項服務是以 Microsoft 的經驗為基礎來保護取用者身分識別，並從一天超過13000000000個登入的信號獲得極高的精確度。 

## <a name="prerequisites"></a>必要條件

- 您必須具有[Azure AD Premium P2 訂](https://azure.microsoft.com/pricing/details/active-directory/)用帳戶。
- 您必須具有具有全域管理員或安全性系統管理員許可權的使用者。


## <a name="connect-to-azure-ad-identity-protection"></a>連接到 Azure AD Identity Protection

如果您有 Azure AD Premium P2 訂用帳戶，Azure AD Identity Protection 就會包含在內。 如果[已啟用任何原則](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)並產生警示，則可以輕鬆地將警示資料串流至 Azure Sentinel。

1. 在 Azure Sentinel 中，選取 [**資料連線器**]，然後按一下 [ **Azure AD Identity Protection** ] 磚。

1. 按一下 **[連接]** 開始將 Azure AD Identity Protection 事件串流至 Azure Sentinel。

1. 若要在 Log Analytics 中針對 Azure AD Identity Protection 警示使用相關的架構，請搜尋**SecurityAlert**。

如果您想要測試連接器，可以[模擬](../active-directory/identity-protection/howto-identity-protection-simulate-risk.md)偵測來產生會串流至 Azure Sentinel 的範例警示。

## <a name="next-steps"></a>後續步驟

在本檔中，您已瞭解如何將 Azure AD Identity Protection 連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。

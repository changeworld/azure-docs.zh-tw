---
title: 將 Azure 資訊安全中心資料連線到 Azure Sentinel
description: 瞭解如何從 Azure 資訊安全中心（ASC）標準層連線警示，並將它們串流至 Azure Sentinel。
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 2fc7744600a9652ad43fd0aae8d886dc94acd58f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559152"
---
# <a name="connect-data-from-azure-security-center-asc"></a>從 Azure 資訊安全中心連接資料（ASC）

Azure Sentinel 可讓您從[Azure 資訊安全中心](../security-center/security-center-intro.md)連接警示，並將它們串流至 Azure Sentinel。 

## <a name="prerequisites"></a>必要條件

- 若要從 Azure 資訊安全中心匯出警示，您必須在您所串流之記錄的訂用帳戶中具有安全性讀取者角色。

- 您必須在訂用帳戶上執行[Azure 資訊安全中心標準層](../security-center/security-center-pricing.md)。 如果不是，請將[您的訂用帳戶升級為標準版](https://azure.microsoft.com/pricing/details/security-center/)。

## <a name="connect-to-azure-security-center"></a>連接到 Azure 資訊安全中心

1. 在 Azure Sentinel 中，從導覽功能表選取 [**資料連線器**]。

1. 從資料連線器資源庫中，選取 [ **Azure 資訊安全中心**]，然後按一下 [**開啟連接器頁面**] 按鈕。

1. 在 [設定] 下，按一下每個您想要串流其警示的訂**用帳戶旁邊**的 **[連線]** Azure Sentinel。 只有在您擁有必要的許可權和 ASC 標準層訂用帳戶時，才能使用 [連線] 按鈕。

1. 您可以選取是否要讓警示 Azure 資訊安全中心在 Azure Sentinel 中自動產生事件。 在 [**建立事件**] 底下，選取 [**已啟用**] 以開啟自動從警示建立事件的預設分析規則。 接著，您可以在 [作用中**規則**] 索引標籤中的 [**分析**] 底下編輯此規則。

1. 若要在 Log Analytics 中針對 Azure 資訊安全中心警示使用相關的架構，請搜尋**SecurityAlert**。

## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將 Azure 資訊安全中心連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。

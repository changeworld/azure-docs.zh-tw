---
title: 將 Azure Defender 資料連線到 Azure Sentinel
description: 瞭解如何從 Azure 資訊安全中心連線到 Azure Defender 警示，並將其串流至 Azure Sentinel。
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 09/07/2020
ms.author: yelevin
ms.openlocfilehash: b1188e533039b0137cebb22652d9921418c41deb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89659648"
---
# <a name="connect-azure-defender-alert-data-from-azure-security-center"></a>從 Azure 資訊安全中心連接 Azure Defender 警示資料

使用 Azure Defender 警示連接器從 [Azure 資訊安全中心](../security-center/security-center-intro.md) 內嵌 Azure defender 警示，並將其串流至 Azure Sentinel。 

## <a name="prerequisites"></a>必要條件

- 您的使用者必須具有您所串流記錄的訂用帳戶中的「安全性讀取者」角色。

- 您必須啟用 Azure 資訊安全中心內的 Azure Defender。  (標準層不再存在，而且不再是授權需求。 ) 

## <a name="connect-to-azure-defender"></a>連接到 Azure Defender

1. 在 Azure Sentinel 中，從導覽功能表中選取 [ **資料連線器** ]。

1. 從 [資料連線器資源庫] 中，選取 [ASC (可能仍會被呼叫 Azure 資訊安全中心) ] 的 [ **Azure Defender 警示** ]，然後按一下 [ **開啟連接器頁面** ] 按鈕。

1. 在 [設定] 底下，按一下您想要串流至 Azure Sentinel 的每個訂 **用帳戶旁**的 **[** 連線]。 只有當您有必要的許可權時，[連接] 按鈕才會出現。

1. 您可以選取是否要讓 Azure Defender 的警示在 Azure Sentinel 中自動產生事件。 在 [ **建立事件**] 底下，選取 [ **已啟用** ] 以開啟自動從警示建立事件的預設分析規則。 然後，您可以在 [作用中**規則**] 索引標籤的 [**分析**] 下編輯此規則。

1. 若要在 Log Analytics 中使用適用于 Azure Defender 警示的相關架構，請搜尋 **SecurityAlert**。

## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將 Azure Defender 連線至 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。

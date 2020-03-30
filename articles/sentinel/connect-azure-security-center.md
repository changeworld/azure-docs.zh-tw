---
title: 將 Azure 安全中心資料連線到 Azure 哨兵
description: 瞭解如何將 Azure 安全中心資料連線到 Azure 哨兵。
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: e6e24a97a8b98bdd5447295880811914100563fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588530"
---
# <a name="connect-data-from-azure-security-center"></a>連接 Azure 安全中心的資料





Azure 哨兵使您能夠連接 Azure[安全中心的](../security-center/security-center-intro.md)警報並將其資料流到 Azure Sentinel 中。 

## <a name="prerequisites"></a>Prerequisites

- 要從 Azure 安全中心匯出警報，必須在流的日誌訂閱中具有安全讀取器角色。

- 必須在訂閱上運行[Azure 安全中心標準層](../security-center/security-center-pricing.md)。 如果沒有，[請將訂閱升級到標準](https://azure.microsoft.com/pricing/details/security-center/)。



## <a name="connect-to-azure-security-center"></a>連接到 Azure 安全中心

1. 在 Azure 哨兵中，選擇**資料連線器**，然後按一下**Azure 安全中心**磁貼。

1. 在右側，按一下要資料流到 Azure Sentinel 的每個訂閱旁邊的 **"連接**"。 請確保將每個訂閱升級到 Azure 安全中心標準層，以便將警報資料流到 Azure Sentinel。

1. 您可以選擇是否希望 Azure 安全中心的警報在 Azure Sentinel 中自動建置事件。 在 [建立事件]**** 底下，選取 [啟用]**** 來啟用預設分析規則，以自動從已連線安全性服務中產生的警示建立事件。 接著，您可以在 [分析]**** 下編輯此規則，然後編輯 [有效規則]****。

3. 按一下 [連線]****。

4. 要在日誌分析中為 Azure 安全中心警報使用相關架構，請搜索**安全警報**。

## <a name="next-steps"></a>後續步驟
在本文檔中，您學習了如何將 Azure 安全中心連接到 Azure 哨兵。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。

---
title: 將 Azure ATP 資料連線到 Azure 哨兵*微軟文檔
description: 瞭解如何將 Azure ATP 資料連線到 Azure 哨兵。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 387d04cbbb125006efcc4efc53a02015fe3f5919
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588581"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp"></a>連接來自 Azure 高級威脅防護 （ATP） 的資料

> [!IMPORTANT]
> Azure 哨兵中的 Azure 高級威脅保護資料連線器當前處於公共預覽版中。
> 此功能在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

只需按一下一下，即可將 Azure[高級威脅保護中的](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp)日誌資料流到 Azure 哨兵中。

## <a name="prerequisites"></a>Prerequisites

- 具有全域管理員或安全管理員許可權的使用者
- 您必須是 Azure ATP 的預覽客戶，並在 Azure ATP 和 Microsoft 雲應用安全性之間啟用集成。 有關詳細資訊，請參閱[Azure 高級保護集成](https://docs.microsoft.com/cloud-app-security/aatp-integration)。

## <a name="connect-to-azure-atp"></a>連接到 Azure ATP

確保[在網路上啟用](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1)Azure ATP 預覽版本。
如果部署 Azure ATP 並引入資料，則可疑警報可以輕鬆地資料流到 Azure Sentinel 中。 警報可能需要長達 24 小時才能開始資料流到 Azure Sentinel 中。


1. 要將 Azure ATP 連接到 Azure 哨兵，必須首先啟用 Azure ATP 和 Microsoft 雲應用安全性之間的集成。 有關如何執行此操作的資訊，請參閱[Azure 高級威脅保護集成](https://docs.microsoft.com/cloud-app-security/aatp-integration)。

1. 在 Azure 哨兵中，選擇**資料連線器**，然後按一下**Azure 高級威脅保護（預覽）** 磁貼。

1. 您可以選擇是否希望 Azure ATP 中的警報在 Azure Sentinel 中自動建置事件。 在 [建立事件]**** 底下，選取 [啟用]**** 來啟用預設分析規則，以自動從已連線安全性服務中產生的警示建立事件。 接著，您可以在 [分析]**** 下編輯此規則，然後編輯 [有效規則]****。

1. 按一下 [連線]****。

1. 要在日誌分析中為 Azure ATP 警報使用相關架構，請搜索**安全警報**。

> [!NOTE]
> 如果警報大於 30 KB，Azure Sentinel 將停止在警報中顯示"實體"欄位。

## <a name="next-steps"></a>後續步驟
在本文中，您學習了如何將 Azure 高級威脅保護連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。


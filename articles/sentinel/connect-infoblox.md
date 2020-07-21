---
title: 將 Infoblox 網路身分識別作業系統（NIOS）資料連線至 Azure Sentinel |Microsoft Docs
description: 瞭解如何將 Infoblox Network Identity 作業系統（NIOS）資料連線至 Azure Sentinel。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: ed4f2d769dbda3dec7b353fddfd1e5e0f3d00f9b
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531264"
---
# <a name="connect-your-infoblox-nios-to-azure-sentinel"></a>將您的 Infoblox NIOS 連線至 Azure Sentinel

本文說明如何將您的[Infoblox 網路身分識別作業系統（NIOS）應用裝置](https://www.infoblox.com/glossary/network-identity-operating-system-nios/)連線到 Azure Sentinel。 Infoblox NIOS 資料連線器可讓您輕鬆地將 Infoblox 記錄與 Azure Sentinel 連線、查看儀表板、建立自訂警示，以及改善調查。 Infoblox NIOS 與 Azure Sentinel 之間的整合會使用 Syslog。

> [!NOTE]
> 資料將會儲存在您執行 Azure Sentinel 之工作區的地理位置中。

## <a name="forward-infoblox-logs-to-the-syslog-agent"></a>將 Infoblox 記錄轉送到 Syslog 代理程式  

設定 Infoblox，以透過 Syslog 代理程式將 Syslog 訊息轉送至您的 Azure 工作區。

1. 在 Azure Sentinel 入口網站中，按一下 [**資料連線器**]，然後選取 [ **Infoblox NIOS**連接器]。

1. 選取 [**開啟連接器] 頁面**。

1. 依照 [ **INFOBLOX NIOS** ] 頁面上的指示進行。

## <a name="find-your-data"></a>尋找您的資料

建立成功的連線之後，資料會出現在 Syslog 底下的 Log Analytics 中。

## <a name="validate-connectivity"></a>驗證連線能力

可能需要20分鐘的時間，您的記錄才會開始出現在 Log Analytics 中。 

## <a name="next-steps"></a>後續步驟

在本檔中，您已瞭解如何將 Infoblox NIOS 連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：

- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。
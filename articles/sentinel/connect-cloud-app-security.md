---
title: 將 Cloud App Security 資料連線到 Azure Sentinel |Microsoft Docs
description: 瞭解如何將 Cloud App Security 資料連線到 Azure Sentinel。
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
ms.date: 10/23/2019
ms.author: yelevin
ms.openlocfilehash: 348576fbbdd1037f9e2e792218b96bbbecf36668
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588360"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>從 Microsoft Cloud App Security 連接資料 



您只要按一下，就可以將[Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security)的記錄串流至 Azure Sentinel。 此連接可讓您將 Cloud App Security 的警示串流至 Azure Sentinel。 

## <a name="prerequisites"></a>Prerequisites

- 具有全域管理員或安全性系統管理員許可權的使用者
- 若要將 Cloud Discovery 記錄串流至 Azure Sentinel，請[在 Microsoft Cloud App Security 中啟用 Azure Sentinel 作為您的 SIEM](https://aka.ms/AzureSentinelMCAS)。

> [!IMPORTANT]
> 內嵌 Cloud Discovery 記錄目前處於公開預覽狀態。
> 這項功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
 
## <a name="connect-to-cloud-app-security"></a>連接到 Cloud App Security

如果您已經有 Cloud App Security，請確定已[在您的網路上啟用該功能](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security)。
如果 Cloud App Security 已部署並內嵌您的資料，則可以輕鬆地將警示資料串流至 Azure Sentinel。


1. 在 Azure Sentinel 中，選取 [**資料連線器**]，按一下 [ **Cloud App Security** ] 磚，然後選取 [**開啟連接器] 頁面**。

1. 選取您想要串流至 Azure Sentinel 的記錄檔，您可以選擇 [**警示**] 和 [ **Cloud Discovery 記錄**（預覽）]。 

1. 按一下 [ **連接**]。

1. 若要在 Log Analytics 中針對 Cloud App Security 警示使用相關的架構，請搜尋**SecurityAlert**。




## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將 Microsoft Cloud App Security 連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats.md)。

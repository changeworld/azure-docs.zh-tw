---
title: 將 Okta 單一 Sign-On 資料連線至 Azure Sentinel |Microsoft Docs
description: 瞭解如何將 Okta 單一 Sign-On 資料連線到 Azure Sentinel。
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
ms.openlocfilehash: 05a9b8009d896a2ee87df3e1c4493d249a887566
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87083917"
---
# <a name="connect-your-okta-single-sign-on-to-azure-sentinel-with-azure-function"></a>使用 Azure Function 將 Okta 單一 Sign-On 連接到 Azure Sentinel

> [!IMPORTANT]
> Azure Sentinel 中的 Okta 單一 Sign-On 資料連線器目前處於公開預覽狀態。
> 這項功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Okta 單一 Sign-On (SSO) 連接器，可讓您輕鬆地將所有 [Okta 單一 Sign-On (](https://www.okta.com/products/single-sign-on/) 使用) 的 sso Azure Sentinel 安全性解決方案記錄，以查看儀表板、建立自訂警示，以及改進調查。 Okta 單一 Sign-On 與 Azure Sentinel 之間的整合，會利用 Azure Functions 使用 REST API 來提取記錄資料。

> [!NOTE]
> 資料會儲存在您執行 Azure Sentinel 之工作區的地理位置。

## <a name="configure-and-connect-okta-single-sign-on"></a>設定和連接 Okta 單一 Sign-On

Azure Functions 可以直接從 Okta 單一 Sign-On 整合和提取事件和記錄，並將其轉送至 Azure Sentinel。

1. 在 Azure Sentinel 入口網站中，按一下 [ **資料連線器** ]，然後選取 [ **Okta 單一登入** 連接器]。

1. 選取 [ **開啟連接器] 頁面**。

1. 遵循 **Okta 單一登入** 頁面上的指示進行。

## <a name="find-your-data"></a>尋找您的資料

建立成功的連接之後，資料會出現在 Log Analytics 的 **Okta_CL** 資料表下。

## <a name="validate-connectivity"></a>驗證連線能力

可能需要20分鐘的時間，記錄才會開始出現在 Log Analytics 中。

## <a name="next-steps"></a>接下來的步驟

在本檔中，您已瞭解如何使用 Azure 函數應用程式將 Okta 單一 Sign-On 連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：

- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。


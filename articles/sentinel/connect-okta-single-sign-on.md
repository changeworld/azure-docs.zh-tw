---
title: 將 Okta 單一登入資料連線到 Azure Sentinel |Microsoft Docs
description: 瞭解如何將 Okta 單一登入資料連線到 Azure Sentinel。
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
ms.openlocfilehash: 37ade037b7f3c88f5ff33d7fc4640b19f366fe7a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531208"
---
# <a name="connect-your-okta-single-sign-on-to-azure-sentinel-with-azure-function"></a>使用 Azure Function 將您的 Okta 單一登入連線至 Azure Sentinel

Okta 單一登入（SSO）連接器可讓您輕鬆地連接所有使用 Azure Sentinel 的[Okta 單一登入（sso）](https://www.okta.com/products/single-sign-on/)安全性解決方案記錄，以查看儀表板、建立自訂警示及改善調查。 Okta 單一登入和 Azure Sentinel 之間的整合會利用 Azure Functions，使用 REST API 來提取記錄資料。

> [!NOTE]
> 資料將會儲存在您執行 Azure Sentinel 之工作區的地理位置中。

## <a name="configure-and-connect-okta-single-sign-on"></a>設定及連接 Okta 單一登入

Azure Functions 可以直接從 Okta 單一登入整合和提取事件和記錄檔，並將它們轉送到 Azure Sentinel。

1. 在 Azure Sentinel 入口網站中，按一下 [**資料連線器**]，然後選取 [ **Okta 單一登入**連接器]。

1. 選取 [**開啟連接器] 頁面**。

1. 依照 [ **Okta 單一登入**] 頁面上的指示進行。

## <a name="find-your-data"></a>尋找您的資料

建立成功的連線之後，資料會顯示在 Log Analytics 的 [ **Okta_CL** ] 資料表下。

## <a name="validate-connectivity"></a>驗證連線能力

可能需要20分鐘的時間，您的記錄才會開始出現在 Log Analytics 中。

## <a name="next-steps"></a>後續步驟

在本檔中，您已瞭解如何使用 Azure 函式應用程式，將 Okta 單一登入連線到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：

- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。


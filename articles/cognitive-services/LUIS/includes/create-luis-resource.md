---
title: 建立 LUIS 資源
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/20/2020
ms.author: aahi
ms.openlocfilehash: ee7fd384a198c5eff672b14b6cb479aac26cfe54
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95972502"
---
<a name="create-luis-resources"></a>

## <a name="create-luis-resources-in-the-azure-portal"></a>在 Azure 入口網站中建立 LUIS 資源

1. 使用[此連結](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)開始在 Azure 入口網站中建立 LUIS 資源。

1. 輸入所有必要的設定：

    |名稱|目的|
    |--|--|
    |訂用帳戶 | 要作為資源收費對象的訂用帳戶。|
    |資源群組| 您選擇或建立的自訂資源群組名稱。 資源群組可讓您將 Azure 資源分組以方便存取和管理。|
    |名稱| 您選擇的自訂名稱。 這是用來作為製作和預測端點查詢的自訂子網域。|
    |製作位置|與您模型相關聯的區域。|
    |製作定價層|決定每秒和每月的交易上限。|
    |預測位置|與您發佈的預測端點執行階段相關聯的區域。|
    |預測定價層|決定每秒和每月的交易上限。|

    > [!div class="mx-imgBorder"]
    > [![螢幕擷取畫面：顯示 [建立] 下的 [基本] 索引標籤。](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png)](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png#lightbox)

1. 選取 [檢閱 +建立]，並等待系統建立資源。
1. 建立這兩個資源之後，仍然在 Azure 入口網站中選取新的製作資源。 然後選取 [金鑰和端點] 取得製作 **端點 URL** 和 **金鑰**，以程式設計方式進行製作。

> [!TIP]
> 若要使用資源，請在 LUIS 入口網站中，[指派資源](../luis-how-to-azure-subscription.md#assign-an-authoring-resource-in-the-luis-portal-for-all-apps)。

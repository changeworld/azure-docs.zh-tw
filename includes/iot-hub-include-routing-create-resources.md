---
title: 包含檔案
description: 包含檔案
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 5680bd1c3fadab8edcde1634c708ca8683c0e99c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "76021010"
---
## <a name="create-base-resources"></a>建立基底資源

若要設定訊息路由，您必須先建立 IoT 中樞、儲存體帳戶和服務匯流排佇列。 您可以使用本教學課程第 1 部分所提供四篇文章的其中一篇來建立這些資源：Azure 入口網站、Azure Resource Manager 範本、Azure CLI 或 Azure PowerShell。

請將所有資源建立在相同的資源群組和位置。 如此一來，您就可以在結束時，透過刪除資源群組的單一步驟來移除所有資源。

以下會摘要介紹後續各節所要執行的步驟： 

1. 建立[資源群組](../articles/azure-resource-manager/management/overview.md)。

2. 在 S1 層建立 IoT 中樞。 將取用者群組新增至 IoT 中樞。 Azure 串流分析會在擷取資料時使用取用者群組。

   > [!NOTE]
   > 您必須使用付費層中的 IoT 中樞，才能完成本教學課程。 免費層只能讓您設定一個端點，而本教學課程需要多個端點。
   > 

3. 透過 Standard_LRS 複寫來建立標準 V1 儲存體帳戶。

4. 建立服務匯流排命名空間與佇列。

5. 為傳送訊息至中樞的模擬裝置建立裝置身分識別。 儲存測試階段的金鑰。 (如果建立 Resource Manager 範本，則會在部署範本後進行)。
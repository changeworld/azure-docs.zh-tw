---
title: 在 Azure 遷移中添加遷移工具
description: 瞭解如何在 Azure 遷移中添加遷移工具。
author: rayne-wiselman
ms.service: azure-migrate
ms.manager: carmonm
ms.topic: article
ms.date: 11/18/2019
ms.author: raynew
ms.openlocfilehash: 6bcb65912627356215769ccc8380ee1a7404f6f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185969"
---
# <a name="add-migration-tools"></a>新增移轉工具

本文介紹如何在[Azure 遷移](migrate-overview.md)中添加遷移工具。

Azure 遷移提供了用於評估和遷移到 Azure 的工具中心。 它包括本機工具、其他 Azure 服務提供的工具和協力廠商獨立軟體廠商 （ISV） 產品。

如果要添加遷移工具但尚未設置 Azure 遷移專案，請按照[本文操作](how-to-add-tool-first-time.md)。



## <a name="selecting-an-isv-tool"></a>選取 ISV 工具

如果您選擇[ISV 工具](migrate-services-overview.md#isv-integration)進行遷移，則可以根據 ISV 策略從獲取許可證或註冊免費試用開始。 每個工具中都會有連線至 Azure Migrate 的選項。 部署該工具，並按照工具說明和文檔將工具工作區與 Azure 遷移連接。 

## <a name="select-a-migration-scenario"></a>選擇遷移方案

1. 在 Azure Migrate 專案中，按一下 [概觀]****。
2. 選擇要使用的遷移方案：

    - 要將電腦和工作負荷遷移到 Azure，請選擇 **"評估和遷移伺服器**"。
    - 要遷移本地 SQL 電腦，請選擇 **"評估和遷移資料庫**"。
    - 要遷移本地 Web 應用，請選擇"**評估和遷移 Web 應用**"。
    - 要在離線模式下將大量本地資料移轉到 Azure，請選擇 **"訂購資料框**"。

    ![評估方案](./media/how-to-migrate/assess-scenario.png)

## <a name="select-a-server-migration-tool"></a>選擇伺服器遷移工具

1. 按一下 **"評估和遷移伺服器**"。
2. 在**Azure 遷移 - 伺服器**中，如果尚未添加遷移工具，請在**遷移工具**下選擇 **"按一下此處添加遷移工具**"。 如果已添加遷移工具，請在 **"添加更多遷移工具"** 中，選擇 **"更改**"。

    > [!NOTE]
    > 如果需要導航到其他專案，請在**Azure 遷移 - 伺服器**中，在 **"查看其他遷移專案的詳細資訊"** 旁邊，**按一下此處**。

3. 在**Azure 遷移中**，選擇要使用的遷移工具。
    - 如果使用 Azure 遷移伺服器遷移，則可以直接在 Azure 遷移專案中設置和運行遷移。
    - 如果使用協力廠商評估工具，請導航到為 ISV 提供的連結，然後按照它們提供的說明運行遷移。

## <a name="select-a-database-migration-tool"></a>選擇資料庫移轉工具

1. 按一下 **"評估和遷移資料庫**"
2. 在 **"資料庫"** 中，按一下"**添加工具**"。
3. 在添加工具>**選擇遷移工具**中，選擇要用於遷移資料庫的工具。

## <a name="select-a-web-app-migration-tool"></a>選擇 Web 應用遷移工具

1. 按一下 **"評估和遷移 Web 應用**"。
2. 按照 Azure 應用服務的遷移工具的連結進行操作。 使用遷移工具：

    - **線上評估應用**：您可以使用 Azure 應用服務遷移助手線上評估和遷移具有公共 URL 的應用。
    - **.NET/PHP**：對於內部 .NET 和 PHP 應用，您可以下載並運行遷移助手。

## <a name="order-an-azure-data-box"></a>訂購 Azure 資料框

要將大量資料移轉到 Azure，可以訂購 Azure DAta Box 進行離線資料傳輸。

1. 按一下 **"訂購資料框**"。
2. 在 **"選擇 Azure 資料框"中**，指定訂閱。 
3. 傳輸將導入到 Azure。 指定資料來源和資料的 Azure 區域目標。

## <a name="next-steps"></a>後續步驟

嘗試使用 Azure 遷移伺服器遷移進行[超 V](tutorial-migrate-hyper-v.md)或[VMware](tutorial-migrate-vmware.md) VM。

---
title: 在 Azure 遷移中添加評估工具
description: 瞭解如何在 Azure 遷移中添加評估工具。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.manager: carmonm
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 64af78abd8f82b41d4a03fbb56c96e3038cef5a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185917"
---
# <a name="add-assessment-tools"></a>新增評量工具

本文介紹如何在[Azure 遷移](migrate-overview.md)中添加評估工具。

Azure 遷移提供了用於評估和遷移到 Azure 的工具中心。 它包括 Azure 遷移工具，以及其他工具和獨立的軟體供應商 （ISV） 產品。

如果要添加評估工具，但尚沒有 Azure 遷移專案，請按照[本文操作](how-to-add-tool-first-time.md)。

## <a name="select-a-tool"></a>選取工具

如果選擇非 Azure 遷移工具進行評估，則根據工具策略從獲取許可證或註冊免費試用開始。 工具可以選擇連接到 Azure 遷移。 按照說明和文檔操作，將該工具連接到 Azure 遷移。 [瞭解有關工具的更多](migrate-services-overview.md)詳細資訊。


## <a name="select-an-assessment-scenario"></a>選擇評估方案

1. 在 Azure Migrate 專案中，按一下 [概觀]****。
2. 選擇要使用的評估方案：

    - 要發現和評估用於遷移到 Azure 的電腦和工作負荷，請選擇 **"評估和遷移伺服器**"。
    - 要評估本地 SQL 電腦，請選擇 **"評估和遷移資料庫**"。
    - 要評估本地 Web 應用，請選擇"**評估和遷移 Web 應用**"。

    ![評估方案](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-server-assessment-tool"></a>選擇伺服器評估工具 

1. 按一下 **"評估和遷移伺服器**"。
2. 在**Azure 遷移 - 伺服器**中，如果尚未添加評估工具，請在 **"評估工具**"下選擇 **"按一下此處添加評估工具**"。 如果已添加評估工具，請在**添加更多評估工具**中，選擇 **"更改**"。

    > [!NOTE]
    > 如果需要導航到其他專案，請在**Azure 遷移 - 伺服器**中，在 **"查看其他遷移專案的詳細資訊"** 旁邊，**按一下此處**。

3. 在**Azure 遷移中**，選擇要使用的評估工具。

    - 如果使用 Azure 遷移伺服器評估，則可以直接在 Azure 遷移專案中設置、運行和查看評估。
    - 如果您使用不同的評估工具，請導航到為其網站提供的連結，並按照他們提供的說明運行評估。


## <a name="select-a-database-assessment-tool"></a>選擇資料庫評估工具

1. 按一下 **"評估和遷移資料庫**"
2. 在 **"資料庫"** 中，按一下"**添加工具**"。
3. 在添加工具>**選擇評估工具**中，選擇要用於評估資料庫的工具。

## <a name="select-a-web-app-assessment-tool"></a>選擇 Web 應用評估工具

1. 按一下 **"評估和遷移 Web 應用**"。
2. 按照 Azure 應用服務的遷移工具的連結進行操作。 使用遷移工具：

    - **線上評估應用**：您可以使用 Azure 應用服務遷移助手線上評估具有公共 URL 的應用。
    - **.NET/PHP**：對於內部 .NET 和 PHP 應用，您可以下載並運行遷移助手。



## <a name="next-steps"></a>後續步驟

嘗試使用 Azure 遷移伺服器評估[VMware](tutorial-prepare-vmware.md) [VM、Hyper-V](tutorial-prepare-hyper-v.md)或[物理伺服器](tutorial-prepare-physical.md)進行評估

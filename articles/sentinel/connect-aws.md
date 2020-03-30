---
title: 將 AWS 雲跟蹤連接到 Azure 哨兵 |微軟文檔
description: 瞭解如何將 AWS 雲跟蹤資料連線到 Azure 哨兵。
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
ms.openlocfilehash: 5cbef1f31ea7088d4fab4888f5630af1b765a910
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588649"
---
# <a name="connect-azure-sentinel-to-aws-cloudtrail"></a>將 Azure 哨兵連接到 AWS 雲跟蹤

使用 AWS 連接器將所有 AWS 雲跟蹤事件資料流到 Azure 哨兵中。 此連接進程將 Azure Sentinel 的存取權限委派到 AWS 資源日誌，從而在 AWS CloudTrail 和 Azure Sentinel 之間創建信任關係。 這是通過在 AWS 上創建一個角色來實現的，該角色授予 Azure Sentinel 訪問 AWS 日誌的許可權。

## <a name="prerequisites"></a>Prerequisites

您必須在 Azure Sentinel 工作區上具有寫入權限。

> [!NOTE]
> Azure 哨兵從所有區域收集雲跟蹤事件。 建議您不要將事件從一個區域資料流到另一個區域。

## <a name="connect-aws"></a>連線 AWS 


1. 在 Azure 哨兵中，選擇 **"資料連線器**"，然後在表格中和右側的 AWS 窗格中選擇**Amazon Web 服務**行，按一下 **"打開連接器"頁**。

1. 使用以下步驟按照**配置**下的說明進行操作。
 
1.  在您的亞馬遜 Web 服務主控台中 **，在"安全、身份&合規性**"下，選擇**IAM**。

    ![AWS1](./media/connect-aws/aws-1.png)

1.  選擇**角色**並選擇 **"創建角色**"。

    ![AWS2](./media/connect-aws/aws-2.png)

1.  選擇**另一個 AWS 帳戶。** 在 **"帳戶 ID"** 欄位中，輸入可在 Azure Sentinel 門戶中的 AWS 連接器頁面中找到的**Microsoft 帳戶 ID** **（123412341234）。**

    ![AWS3](./media/connect-aws/aws-3.png)

1.  確保選擇了 **"需要外部 ID"，** 然後輸入可在 Azure Sentinel 門戶中的 AWS 連接器頁面中找到的外部 ID（工作區 ID）。

    ![AWS4](./media/connect-aws/aws-4.png)

1.  在**附加權限原則**下選擇**AWSCloudTrailRead 僅訪問**。

    ![AWS5](./media/connect-aws/aws-5.png)

1.  輸入標籤（可選）。

    ![AWS6](./media/connect-aws/aws-6.png)

1.  然後，輸入**角色名稱**並選擇"**創建角色"** 按鈕。

    ![AWS7](./media/connect-aws/aws-7.png)

1.  在"角色"清單中，選擇您創建的角色。

    ![AWS8](./media/connect-aws/aws-8.png)

1.  複製**角色 ARN**。 在 Azure Sentinel 門戶中，在 Amazon Web 服務連接器螢幕中，將其粘貼到 **"角色"中以添加**欄位，然後按一下"**添加**"。

    ![AWS9](./media/connect-aws/aws-9.png)

1. 要在日誌分析中使用相關的架構進行 AWS 事件，請搜索**AWSCloudTrail**。



## <a name="next-steps"></a>後續步驟
在本文中，您學習了如何將 AWS 雲跟蹤連接到 Azure 哨兵。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。


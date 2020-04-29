---
title: 將 AWS CloudTrail 連接到 Azure Sentinel |Microsoft Docs
description: 瞭解如何將 AWS CloudTrail 資料連線到 Azure Sentinel。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588649"
---
# <a name="connect-azure-sentinel-to-aws-cloudtrail"></a>將 Azure Sentinel 連線至 AWS CloudTrail

使用 AWS 連接器將所有 AWS CloudTrail 事件串流至 Azure Sentinel。 此連線程式會將 Azure Sentinel 的存取權委派給 AWS 資源記錄，並建立 AWS CloudTrail 與 Azure Sentinel 之間的信任關係。 這是在 AWS 上完成，藉由建立可授與存取 AWS 記錄 Azure Sentinel 許可權的角色。

## <a name="prerequisites"></a>先決條件

您必須擁有 Azure Sentinel 工作區的 [寫入] 許可權。

> [!NOTE]
> Azure Sentinel 會收集來自所有區域的 CloudTrail 事件。 建議您不要將事件從一個區域串流至另一個區域。

## <a name="connect-aws"></a>連線 AWS 


1. 在 Azure Sentinel 中選取 [**資料連線器**]，然後選取資料表中的**Amazon Web Services**行，然後在右側的 [AWS] 窗格中，按一下 [**開啟連接器] 頁面**。

1. **使用下列**步驟，遵循 [設定] 底下的指示。
 
1.  在 Amazon Web Services 主控台中的 [**安全性]、[識別 & 相容性**] 底下，選取 [ **IAM**]。

    ![AWS1](./media/connect-aws/aws-1.png)

1.  選擇 [**角色**]，然後選取 [**建立角色**]。

    ![AWS2](./media/connect-aws/aws-2.png)

1.  選擇**另一個 AWS 帳戶。** 在 [**帳戶識別碼**] 欄位中，輸入可在 Azure Sentinel 入口網站的 [AWS 連接器] 頁面中找到的**Microsoft 帳戶識別碼**（**123412341234**）。

    ![AWS3](./media/connect-aws/aws-3.png)

1.  請確定已選取 [**需要外部識別碼**]，然後輸入可在 Azure Sentinel 入口網站的 [AWS 連接器] 頁面中找到的外部識別碼（工作區識別碼）。

    ![AWS4](./media/connect-aws/aws-4.png)

1.  在 [**附加許可權原則**] 底下，選取 [ **AWSCloudTrailReadOnlyAccess**]。

    ![AWS5](./media/connect-aws/aws-5.png)

1.  輸入標記（選擇性）。

    ![AWS6](./media/connect-aws/aws-6.png)

1.  然後，輸入**角色名稱**，然後選取 [**建立角色**] 按鈕。

    ![AWS7](./media/connect-aws/aws-7.png)

1.  在 [角色] 清單中，選擇您建立的角色。

    ![AWS8](./media/connect-aws/aws-8.png)

1.  複製 [**角色 ARN**]。 在 Azure Sentinel 入口網站的 [Amazon Web Services 連接器] 畫面中，將它貼到 [**要新增的角色**] 欄位中，然後按一下 [**新增**]。

    ![AWS9](./media/connect-aws/aws-9.png)

1. 若要在 Log Analytics 中使用適用于 AWS 事件的相關架構，請搜尋**AWSCloudTrail**。



## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將 AWS CloudTrail 連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)來監視您的資料。


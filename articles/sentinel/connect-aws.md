---
title: 將 AWS CloudTrail 連接到 Azure Sentinel |Microsoft Docs
description: 使用 AWS 連接器將 Azure Sentinel 存取權委派給 AWS 資源記錄，並在 AWS CloudTrail 和 Sentinel 之間建立信任關係。
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
ms.date: 05/27/2020
ms.author: yelevin
ms.openlocfilehash: a7405824d2477d2d39c45a56ae545e58a090c321
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96436601"
---
# <a name="connect-azure-sentinel-to-aws-cloudtrail"></a>將 Azure Sentinel 連線至 AWS CloudTrail

使用 AWS 連接器，將 AWS CloudTrail 管理事件串流至 Azure Sentinel。 此連接程式會將 Azure Sentinel 的存取權委派給您的 AWS 資源記錄，並在 AWS CloudTrail 和 Azure Sentinel 之間建立信任關係。 這是在 AWS 上完成，方法是建立角色，以授與存取您 AWS 記錄的 Azure Sentinel 許可權。

> [!NOTE]
> AWS CloudTrail 在其 LookupEvents API 中有內 [建的限制](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/WhatIsCloudTrail-Limits.html) 。 每秒不允許超過兩筆交易 (TPS) 每個帳戶，而每個查詢最多可傳回50筆記錄。 因此，如果單一租使用者在一個區域中每秒持續產生超過100筆記錄，將會導致資料內嵌中的待處理專案和延遲。

## <a name="prerequisites"></a>必要條件

您必須具備 Azure Sentinel 工作區的寫入權限。

> [!NOTE]
> Azure Sentinel 從所有區域收集 CloudTrail 管理事件。 建議您不要將事件從某個區域串流至另一個區域。

## <a name="connect-aws"></a>連接 AWS 


1. 在 Azure Sentinel 中，選取 [ **資料連線器** ]，然後選取資料表中的 **Amazon Web Services** 行，然後在右側的 [AWS] 窗格中，按一下 [ **開啟連接器] 頁面**。

1. **使用下列** 步驟，依照設定下的指示進行。
 
1.  在 Amazon Web Services 主控台的 [ **安全性、身分識別 & 合規性**] 下，選取 [ **IAM**]。

    ![AWS1](./media/connect-aws/aws-1.png)

1.  選擇 [ **角色** ]，然後選取 [ **建立角色**]。

    ![AWS2](./media/connect-aws/aws-2.png)

1.  選擇 **另一個 AWS 帳戶。** 在 [ **帳戶識別碼** ] 欄位中，輸入可在 Azure Sentinel 入口網站的 [AWS 連接器] 頁面中找到的 **Microsoft 帳戶識別碼** (**123412341234**) 。

    ![AWS3](./media/connect-aws/aws-3.png)

1.  確定已選取 [ **需要外部識別碼** ]，然後在 Azure Sentinel 入口網站的 [AWS 連接器] 頁面中，輸入可在 [連接器] 頁面中找到的 [外部識別碼] (工作區識別碼) 。

    ![AWS4](./media/connect-aws/aws-4.png)

1.  在 [ **附加許可權原則** ] 底下，選取 [ **AWSCloudTrailReadOnlyAccess**]。

    ![AWS5](./media/connect-aws/aws-5.png)

1.  輸入 (選用) 的標記。

    ![AWS6](./media/connect-aws/aws-6.png)

1.  然後，輸入 **角色名稱** ，然後選取 [ **建立角色** ] 按鈕。

    ![AWS7](./media/connect-aws/aws-7.png)

1.  在 [角色] 清單中，選擇您建立的角色。

    ![AWS8](./media/connect-aws/aws-8.png)

1.  複製 **角色 ARN**。 在 Azure Sentinel 入口網站的 [Amazon Web Services 連接器] 畫面中，將它貼到 [ **要新增的角色** ] 欄位中，然後按一下 [ **新增**]。

    ![AWS9](./media/connect-aws/aws-9.png)

1. 若要在 Log Analytics 中針對 AWS 事件使用相關的架構，請搜尋 **AWSCloudTrail**。

    > [!IMPORTANT]
    > 從2020年12月1日起， **AwsRequestId** 欄位已被 **AwsRequestId_** 欄位取代 (請注意新增的底線) 。 舊 **AwsRequestId** 欄位中的資料將會保留到客戶指定的資料保留期間結束。

## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將 AWS CloudTrail 連線至 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。

---
title: 聯合國H 2.5 分段在EDIFACT電給
description: 使用企業集成包在 Azure 邏輯應用中使用 UNH2.5 段解析 EDIFACT 消息
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/27/2017
ms.openlocfilehash: ad50cbb423f8c60f1caad159bc1a20cf96ed98aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792532"
---
# <a name="handle-edifact-documents-with-unh25-segments-in-azure-logic-apps"></a>在 Azure Logic Apps 使用 UNH 2.5 區段處理 EDIFACT 文件

如果 EDIFACT 文檔中存在 UNH2.5 段，則該段用於架構查找。 例如，在此示例 EDIFACT 消息中，UNH 欄位`EAN008`為 ：

`UNH+SSDD1+ORDERS:D:03B:UN:EAN008`

要處理此消息，請按照下面介紹的步驟操作：

1. 更新架構。

1. 檢查協定設置。

## <a name="update-the-schema"></a>更新結構描述

要處理消息，您需要部署具有 UNH2.5 根節點名稱的架構。 例如，示例 UNH 欄位的架構根名稱為`EFACT_D03B_ORDERS_EAN008`。 對於具有`D03B_ORDERS`不同 UNH2.5 段的每個架構，必須部署單個架構。

## <a name="add-schema-to-edifact-agreement"></a>將架構添加到 EDIFACT 協定

### <a name="edifact-decode"></a>EDIFACT 解碼

要解碼傳入消息，請設置 EDIFACT 協定接收設置中的架構：

1. 在[Azure 門戶](https://portal.azure.com)中，打開集成帳戶。

1. 將架構添加到集成帳戶。

1. 在 EDIFACT 協定的接收設置中配置架構。

1. 選擇 EDIFACT 協定，然後選擇 **"編輯為 JSON**"。 將 UNH2.5 值添加到接收協定部分`schemaReferences`：

   ![添加 UNH2.5 以接收協定](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png)

### <a name="edifact-encode"></a>EDIFACT 編碼

要對傳入消息進行編碼，請在 EDIFACT 協定發送設置中配置架構

1. 在[Azure 門戶](https://portal.azure.com)中，打開集成帳戶。

1. 將架構添加到集成帳戶。

1. 在 EDIFACT 協定的發送設置中配置架構。

1. 選取 EDIFACT 合約，然後按一下 [編輯為 JSON]****。  在傳送合約中新增 UNH2.5 值 **schemaReferences**

1. 選擇 EDIFACT 協定，然後選擇 **"編輯為 JSON**"。 將 UNH2.5 值添加到發送協定的`schemaReferences`部分：

   ![添加 UNH2.5 以發送協定](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png)

## <a name="next-steps"></a>後續步驟

* 瞭解有關[集成帳戶協定的更多內容](../logic-apps/logic-apps-enterprise-integration-agreements.md)
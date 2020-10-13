---
title: EDIFACT 訊息中的 UNH 2.5 區段
description: 使用 Enterprise Integration Pack Azure Logic Apps 中的 UNH 2.5 區段來解析 EDIFACT 訊息
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/27/2017
ms.openlocfilehash: c6d95cb938a341b94a32989e82c42d37956c0a80
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89179839"
---
# <a name="handle-edifact-documents-with-unh25-segments-in-azure-logic-apps"></a>在 Azure Logic Apps 使用 UNH 2.5 區段處理 EDIFACT 文件

如果 EDIFACT 檔中存在 UNH 2.5 區段，則會使用區段進行架構查閱。 例如，在此範例 EDIFACT 訊息中，UNH 欄位是 `EAN008` ：

`UNH+SSDD1+ORDERS:D:03B:UN:EAN008`

若要處理此訊息，請遵循下列步驟：

1. 更新架構。

1. 檢查合約設定。

## <a name="update-the-schema"></a>更新結構描述

若要處理訊息，您需要部署具有 UNH 2.5 根節點名稱的架構。 例如，範例 UNH 欄位的架構根名稱為 `EFACT_D03B_ORDERS_EAN008` 。 針對每個 `D03B_ORDERS` 具有不同 unh 2.5 區段的，您必須部署個別的架構。

## <a name="add-schema-to-edifact-agreement"></a>將架構新增至 EDIFACT 合約

### <a name="edifact-decode"></a>EDIFACT 解碼

若要解碼內送訊息，請在 EDIFACT 合約的 [接收設定] 中設定架構：

1. 在 [Azure 入口網站](https://portal.azure.com)中，開啟您的整合帳戶。

1. 將架構新增至您的整合帳戶。

1. 在 EDIFACT 合約的 [接收設定] 中設定架構。

1. 選取 EDIFACT 合約，然後選取 [ **編輯為 JSON**]。 將 UNH 2.5 值新增至接收合約的 `schemaReferences` 區段：

   ![新增 UNH 2.5 以接收合約](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png)

### <a name="edifact-encode"></a>EDIFACT 編碼

若要將內送訊息編碼，請在 EDIFACT 合約傳送設定中設定架構

1. 在 [Azure 入口網站](https://portal.azure.com)中，開啟您的整合帳戶。

1. 將架構新增至您的整合帳戶。

1. 設定 EDIFACT 合約傳送設定中的架構。

1. 選取 EDIFACT 合約，然後選取 [ **編輯為 JSON**]。 將 UNH 2.5 值新增至傳送合約的 `schemaReferences` 區段：

   ![新增 UNH 2.5 以傳送合約](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png)

## <a name="next-steps"></a>後續步驟

* 深入瞭解 [整合帳戶協定](../logic-apps/logic-apps-enterprise-integration-agreements.md)

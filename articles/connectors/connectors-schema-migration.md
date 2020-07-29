---
title: 將應用程式遷移至最新的架構
description: 如何將邏輯應用程式工作流程 JSON 定義遷移至最新的工作流程定義語言架構版本
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
ms.openlocfilehash: 114b8b32d4abb1fd9b7e641625cd1b132470bafd
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87281441"
---
# <a name="migrate-logic-apps-to-latest-schema-version"></a>將邏輯應用程式遷移至最新的結構描述版本

若要將現有的邏輯應用程式移至最新的結構描述，請依照下列步驟操作︰ 

1. 在 [Azure 入口網站](https://portal.azure.com)的邏輯應用程式設計工具中，開啟邏輯應用程式。

2. 在邏輯應用程式功能表上，選擇 [概觀]****。 在工具列上，選擇 [更新結構描述]****。

   > [!NOTE]
   > 當您選擇 [更新結構描述]**** 時，Azure Logic Apps 會自動執行移轉步驟，並為您提供程式碼輸出。 您可以將此輸出用於更新邏輯應用程式定義。 不過，請務必遵循如下列**最佳做法**一節所述的最佳做法。

   ![更新結構描述](./media/connectors-schema-migration/update-schema.png)

   [更新結構描述] 頁面便會出現並顯示文件連結，該文件說明新結構描述的改進功能。

## <a name="best-practices"></a>最佳作法

以下是將邏輯應用程式遷移至最新結構描述版本的一些最佳做法︰

* 將已遷移的指令碼複製到新的邏輯應用程式。 在完成測試並確認已遷移的應用程式如預期般運作後，再覆寫舊版本。

* 進入生產環境之前，請**先**測試您的邏輯應用程式。

* 移轉完成後，開始更新邏輯應用程式，盡可能使用[受控 API](../connectors/apis-list.md)。 例如，在您使用 DropBox v1 的每個地方開始使用 Dropbox v2。

## <a name="next-steps"></a>後續步驟

* 了解如何[手動遷移邏輯應用程式](../logic-apps/logic-apps-schema-2016-04-01.md)


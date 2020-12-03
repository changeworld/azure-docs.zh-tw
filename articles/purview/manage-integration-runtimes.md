---
title: 建立及管理整合執行時間
description: 本文說明在 Azure 範疇中建立和管理整合執行時間的步驟。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/16/2020
ms.openlocfilehash: 0d365787ea3603ef0adb8ad0b48bef9792ffb003
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96551949"
---
# <a name="create-and-manage-a-self-hosted-integration-runtime"></a>建立及管理自我裝載整合執行時間

本文說明如何建立和管理自我裝載整合執行時間 (SHIR) ，以協助掃描資料來源。

## <a name="create-a-self-hosted-integration-runtime"></a>建立自我裝載整合執行階段

1. 在範疇 Studio 的首頁上，從左側導覽窗格中選取 [ **管理中心** ]。

2. 在左窗格的 [ **來源] 和 [掃描** ] 下，選取 [ **整合運行** 時間]，然後選取 [ **+ 新增**]。

    :::image type="content" source="media/manage-integration-runtimes/select-integration-runtimes.png" alt-text="按一下 [IR]。":::

3. 在 [ **整合執行時間設定** ] 頁面上，選取 [ **自我** 裝載] 以建立 Self-Hosted IR，然後選取 [ **繼續**]。

    :::image type="content" source="media/manage-integration-runtimes/select-self-hosted-ir.png" alt-text="建立新的 SHIR。":::

4. 輸入 IR 的名稱，然後選取 [建立]。

5. 在 [ **Integration Runtime 設定** ] 頁面上，依照 **手動設定** 一節中的步驟執行。 您必須將整合執行時間從下載網站下載到您想要執行它的 VM 或電腦上。

    :::image type="content" source="media/manage-integration-runtimes/integration-runtime-settings.png" alt-text="取得金鑰":::

    a. 複製並貼上驗證金鑰。
        
    b. 從本機 Windows 電腦上的 [Azure Data Factory Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717) 下載自我裝載整合執行時間。 執行安裝程式。
        
    c. 在 [ **註冊 Integration Runtime (自我裝載)** ] 頁面上，貼上您稍早儲存的2個索引鍵中的其中一個，然後選取 [ **註冊**]。

    :::image type="content" source="media/manage-integration-runtimes/register-integration-runtime.png" alt-text="輸入索引鍵。":::

    d. 在 [新增 Integration Runtime (自我裝載) 節點] 頁面上，選取 [完成]。

6. 自我裝載整合執行時間成功註冊之後，您會看到下列視窗：

    :::image type="content" source="media/manage-integration-runtimes/successfully-registered.png" alt-text="已成功註冊。":::

## <a name="manage-a-self-hosted-integration-runtime"></a>管理自我裝載整合執行時間

您可以流覽至 [**管理中心**] 中的 [**整合運行** 時間]，選取 IR，然後按一下 [編輯]，以編輯自我裝載的整合執行時間。 您現在可以更新描述、複製金鑰或重新產生新的金鑰。

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime.png" alt-text="編輯 IR。":::

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime-settings.png" alt-text="編輯 IR 詳細資料。":::

您可以藉由流覽至 [管理中心] 中的 [ **整合運行** 時間]、選取 IR，然後按一下 [ **刪除**]，來刪除自我裝載的整合執行時間。 一旦將 IR 刪除，任何依賴它的進行中掃描都將會失敗。

## <a name="next-steps"></a>後續步驟

* [掃描如何偵測已刪除的資產](concept-detect-deleted-assets.md)

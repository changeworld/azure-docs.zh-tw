---
title: 建立 Azure 遠端呈現帳戶
description: 描述為 Azure 遠端呈現建立帳號的步驟
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.openlocfilehash: b9b72fb9e80c588eb3e6642d0228bffa50b35c6e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681294"
---
# <a name="create-an-azure-remote-rendering-account"></a>建立 Azure 遠端呈現帳戶

本章將指導您完成為**Azure 遠端呈現**服務創建帳戶的步驟。 完成任何快速入門或教程必須使用有效的帳戶。

## <a name="create-an-account"></a>建立帳戶

為 Azure 遠端呈現服務建立帳戶需要以下步驟:

1. 跳到[混合實境預覽頁面](https://aka.ms/MixedRealityPrivatePreview)
1. 點選「建立資源」按鈕
1. 在搜索欄位("搜索市場")中,鍵入"遠端渲染"並點擊"輸入"。
1. 在結果清單中,按下「遠端渲染」磁貼
1. 在下一個螢幕中,按兩下"創建"按鈕。 將開啟一個表單以建立新的遠端呈現帳號:
    1. 將「資源名稱」設定為帳戶的名稱
    1. 如果需要,更新"訂閱"
    1. 將「資源群組」設定為您選擇的資源群組
1. 建立帳戶後,導航到該帳戶,並:
    1. 在 *"概述'* 選項卡中,請注意"帳戶 ID"
    1. 在 *"設定>存取金鑰*"選項卡中,請注意"主金鑰" - 這是帳戶的秘密帳戶金鑰

### <a name="retrieve-the-account-information"></a>檢索帳戶資訊

範例和教學要求您提供帳戶 ID 和金鑰。 例如,在用於 PowerShell 範例文稿的**arrconfig.json**檔中:

```json
    "accountSettings": {
        "arrAccountId": "<fill in the account ID from the Azure portal>",
        "arrAccountKey": "<fill in the account key from the Azure portal>",
        "region": "<select from available regions>"
    },
```

請參閱[可用區域清單](../reference/regions.md)以填寫*區域*選項。

的值**`arrAccountId`****`arrAccountKey`** 可以在門戶中找到,如以下步驟所述:

* 跳到[Azure 門戶](https://www.portal.azure.com)
* 尋找您的 **「遠端呈現帳戶」** ── 它應位於 **「最新資源」** 清單中。 您還可以在頂部的搜索欄中搜索它。 在這種情況下,請確保在「預設訂閱」篩選器(搜索欄旁邊的篩選器圖示)中選擇了要使用的訂閱:

![訂閱篩選器](./media/azure-subscription-filter.png)

點選您的帳戶會將您帶到此螢幕,該螢幕會立即顯示**帳號 ID:**

![Azure 帳號 ID](./media/azure-account-id.png)

對於鍵,選擇左側面板中的 **「訪問鍵**」。 下一頁顯示主鍵與輔助鍵:

![Azure 存取金鑰](./media/azure-account-primary-key.png)

的值**`arrAccountKey`** 可以是主鍵或輔助鍵。

## <a name="link-storage-accounts"></a>連結儲存帳戶

本段說明如何將存儲帳戶連結到遠端呈現帳戶。 連結存儲帳戶時,不必在每次要與帳戶中的數據交互時(例如載入模型時)生成 SAS URI。 相反,您可以直接使用儲存帳戶名稱,如[載入模型部分](../concepts/models.md#loading-models)中所述。

必須針對應使用此替代訪問方法的每個存儲帳戶執行此段落中的步驟。 如果尚未創建存儲帳戶,則可以在[轉換模型以呈現快速啟動](../quickstarts/convert-model.md#storage-account-creation)時遍歷相應的步驟。

現在假定您有一個存儲帳戶。 瀏覽到門戶中的儲存帳戶,然後轉到該儲存帳戶的存取**控制 (IAM)** 選項卡:

![儲存帳戶 IAM](./media/azure-storage-account.png)

 確保對此存儲帳戶具有擁有者許可權,以確保可以添加角色分配。 如果您沒有存取權限,將停用 **「添加角色分配**」選項。

 您需要添加三個不同的角色,如下步驟中所述。 如果不提供所有三個級別的訪問,則在嘗試訪問存儲帳戶時將存在許可權問題。

 點選「新增角色分配」磁貼中的 **「新增**」按鈕以新增第一個角色:

![儲存帳戶 IAM](./media/azure-add-role-assignment.png)

* 第一個要分配的角色是**所有者**,如上圖所示。 
* 從 +**分配存取到**下拉清單中選擇**遠端呈現帳戶**。
* 在上一個下拉清單中選擇訂閱和遠端呈現帳戶。

重複新增新角色兩次,以便從**角色**下拉清單的下拉進行相應的選擇:
* **儲存體帳戶參與者**
* **儲存體 Blob 資料參與者**

其他下拉清單選擇在第一步中。

如果添加了所有三個角色,則 Azure 遠端呈現帳戶可以使用系統分配的託管服務標識存取記憶體帳戶。

## <a name="next-steps"></a>後續步驟

* [驗證][](authentication.md)
* [使用 Azure 前端 API 進行認證](frontend-apis.md)
* [電源外殼文稿範例](../samples/powershell-example-scripts.md)

---
title: 建立 Azure 遠端轉譯帳戶
description: 說明為 Azure 遠端呈現建立帳戶的步驟
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.openlocfilehash: b9b72fb9e80c588eb3e6642d0228bffa50b35c6e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681294"
---
# <a name="create-an-azure-remote-rendering-account"></a>建立 Azure 遠端轉譯帳戶

本章會引導您完成為**Azure 遠端**轉譯服務建立帳戶的步驟。 您必須具備有效的帳戶，才能完成任何快速入門或教學課程。

## <a name="create-an-account"></a>建立帳戶

建立 Azure 遠端轉譯服務的帳戶時，需要執行下列步驟：

1. 前往[混合現實預覽頁面](https://aka.ms/MixedRealityPrivatePreview)
1. 按一下 [建立資源] 按鈕
1. 在搜尋欄位中（「搜尋 marketplace」），輸入「遠端轉譯」，然後按 [enter]。
1. 在結果清單中，按一下 [遠端轉譯] 磚
1. 在下一個畫面中，按一下 [建立] 按鈕。 隨即會開啟表單，以建立新的遠端轉譯帳戶：
    1. 將 [資源名稱] 設定為帳戶的名稱
    1. 視需要更新「訂用帳戶」
    1. 將 [資源群組] 設為您選擇的資源群組
1. 建立帳戶之後，請流覽至它並：
    1. 在 [*總覽*] 索引標籤中，記下 [帳戶識別碼]
    1. 在 [*設定] > [存取金鑰*] 索引標籤中，注意 [主要金鑰]-這是帳戶的秘密帳戶金鑰

### <a name="retrieve-the-account-information"></a>取得帳戶資訊

範例和教學課程會要求您提供帳戶識別碼和金鑰。 例如，在用於 PowerShell 範例腳本的 **: arrconfig**檔案中：

```json
    "accountSettings": {
        "arrAccountId": "<fill in the account ID from the Azure portal>",
        "arrAccountKey": "<fill in the account key from the Azure portal>",
        "region": "<select from available regions>"
    },
```

請參閱填寫*區域*選項的[可用區域清單](../reference/regions.md)。

**`arrAccountId`** 和的值可以**`arrAccountKey`** 在入口網站中找到，如下列步驟所述：

* 移至 [Azure 入口網站](https://www.portal.azure.com)
* 尋找您的「**遠端轉譯帳戶**」-它應該在 [**最近使用的資源]** 清單中。 您也可以在頂端的搜尋列中搜尋它。 在此情況下，請確定已在預設的訂用帳戶篩選中選取您想要使用的訂用帳戶（[搜尋列] 旁的篩選圖示）：

![訂閱篩選器](./media/azure-subscription-filter.png)

按一下您的帳戶可帶您前往此畫面，這會立即顯示**帳戶識別碼**：

![Azure 帳戶識別碼](./media/azure-account-id.png)

針對金鑰，選取左側面板中的 [**存取金鑰**]。 下一個頁面會顯示主要和次要索引鍵：

![Azure 存取金鑰](./media/azure-account-primary-key.png)

的值**`arrAccountKey`** 可以是主要或次要索引鍵。

## <a name="link-storage-accounts"></a>連結儲存體帳戶

這段說明如何將儲存體帳戶連結到您的遠端轉譯帳戶。 當儲存體帳戶連結時，您不需要在每次想要與帳戶中的資料互動時產生 SAS URI，例如載入模型時。 相反地，您可以直接使用儲存體帳戶名稱，如[載入模型一節](../concepts/models.md#loading-models)中所述。

此段落中的步驟必須針對應該使用此替代存取方法的每個儲存體帳戶執行。 如果您尚未建立儲存體帳戶，您可以逐步完成[轉換模型以進行轉譯快速入門](../quickstarts/convert-model.md#storage-account-creation)中的個別步驟。

現在假設您有儲存體帳戶。 流覽至入口網站中的儲存體帳戶，然後移至該儲存體帳戶的 [**存取控制（IAM）** ] 索引標籤：

![儲存體帳戶 IAM](./media/azure-storage-account.png)

 請確定您具有此儲存體帳戶的擁有者許可權，以確保您可以新增角色指派。 如果您沒有存取權，將會停用 [**新增角色指派**] 選項。

 您需要新增三個不同的角色，如下一個步驟所述。 如果您未提供這三個層級的存取權，則在嘗試存取儲存體帳戶時，將會發生許可權問題。

 按一下 [新增角色指派] 磚中的 [**新增**] 按鈕，以新增第一個角色：

![儲存體帳戶 IAM](./media/azure-add-role-assignment.png)

* 第一個要指派的角色是「**擁有**者」，如上面的螢幕擷取畫面所示。 
* 從 [**指派存取權給**] 下拉式清單選取 [**遠端轉譯帳戶**]。
* 在最後一個下拉式清單中選取您的訂用帳戶和遠端轉譯帳戶。

再次針對 [**角色**] 下拉式清單中的個別選項重複新增角色兩次：
* **儲存體帳戶參與者**
* **儲存體 Blob 資料參與者**

另一個下拉式清單會在第一個步驟中選取。

如果您已新增這三個角色，您的 Azure 遠端轉譯帳戶就可以使用系統指派的受控服務識別來存取儲存體帳戶。

## <a name="next-steps"></a>後續步驟

* [驗證](authentication.md)
* [使用 Azure 前端 Api 進行驗證](frontend-apis.md)
* [PowerShell 指令碼範例](../samples/powershell-example-scripts.md)

---
title: 建立 Azure 遠端轉譯帳戶
description: 說明為 Azure 遠端轉譯建立帳戶的步驟
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.openlocfilehash: 83bd4a7ae0082d24f7ac617719e628f4db4baeb9
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98197622"
---
# <a name="create-an-azure-remote-rendering-account"></a>建立 Azure 遠端轉譯帳戶

本章節會引導您完成為 **Azure 遠端轉譯** 服務建立帳戶的步驟。 您必須具備有效帳戶，才能完成任何快速入門或教學課程。

## <a name="create-an-account"></a>建立帳戶

在建立 Azure 遠端轉譯服務的帳戶時，您需要執行下列步驟：

1. 移至[混合實境預覽頁面](https://aka.ms/MixedRealityPrivatePreview)
1. 按一下 [建立資源] 按鈕
1. 在搜尋欄位中 (「搜尋市集」)，輸入「遠端轉譯」，然後按一下 [Enter] 鍵。
1. 在結果清單中，按一下 [遠端轉譯] 圖格
1. 在下一個畫面中，按一下 [建立] 按鈕。 隨即會開啟表單以建立新的遠端轉譯帳戶：
    1. 將 [資源名稱] 設為帳戶名稱
    1. 如有需要，請更新「訂閱」
    1. 將「資源群組」設為您要選擇的資源群組
    1. 從 [位置] 下拉式清單中選取要用來建立此資源的區域。 請參閱下方 [帳戶區域](create-an-account.md#account-regions) 的備註。
1. 建立帳戶之後，請瀏覽至該頁面並：
    1. 在 [總覽] 索引標籤中，記下「帳戶識別碼」
    1. 在 [設定] > [存取金鑰] 索引標籤中，記下 [主要金鑰] - 這是帳戶的秘密帳戶金鑰

### <a name="account-regions"></a>帳戶區域
帳戶建立期間所指定的位置，會決定帳戶資源指派至哪個區域。 建立之後就無法變更。 但是，不論帳戶的位置為何，帳戶都可以用來連接到任何 [支援區域](./../reference/regions.md)中的遠端轉譯會話。

### <a name="retrieve-the-account-information"></a>擷取帳戶資訊

範例和教學課程會要求您提供帳戶識別碼和金鑰。 例如，在用於 PowerShell 範例指令碼的 **arrconfig.json** 檔案中：

```json
"accountSettings": {
    "arrAccountId": "<fill in the account ID from the Azure portal>",
    "arrAccountKey": "<fill in the account key from the Azure portal>",
    "region": "<select from available regions>"
},
```

如需填寫 [區域] 選項，請參閱[可用區域清單](../reference/regions.md)。

如下列步驟所述，您可以在入口網站中找到 **`arrAccountId`** 和 **`arrAccountKey`** 的值：

* 移至 [Azure 入口網站](https://www.portal.azure.com)
* 找到您的 **「遠端轉譯帳戶」** - 它應該位於 **「最近使用的資源」** 清單中。 您也可以在頁面頂端的搜尋列中搜尋帳戶。 在此情況下，請確定您已在預設訂閱篩選器中選取想要使用的訂閱 ([搜尋列] 旁的篩選器圖示)：

![訂閱篩選器](./media/azure-subscription-filter.png)

按一下您的帳戶可帶您前往此畫面，隨即會顯示 **帳戶識別碼**：

![Azure 帳戶識別碼](./media/azure-account-id.png)

針對金鑰，選取左側面板中的 [存取金鑰]。 下一個頁面會顯示主要和次要金鑰：

![Azure 存取金鑰](./media/azure-account-primary-key.png)

**`arrAccountKey`** 的值可以是主要金鑰或次要金鑰。

## <a name="link-storage-accounts"></a>連結儲存體帳戶

本段說明如何將儲存體帳戶連結至您的遠端轉譯帳戶。 在儲存體帳戶完成連結後，您就不需要在每次想要與帳戶中的資料互動時都產生一個 SAS URI，例如在載入模型時。 相對地，您可以直接使用儲存體帳戶名稱，如[載入模型](../concepts/models.md#loading-models)一節中所述。

必須針對每個應使用此存取方法的儲存體帳戶執行此段落中的步驟。 如果您尚未建立儲存體帳戶，您可以逐步執行[快速入門：轉換模型以進行轉譯](../quickstarts/convert-model.md#storage-account-creation)中的個別步驟。

現在假設您有儲存體帳戶。 瀏覽至入口網站中的儲存體帳戶，然後移至該儲存體帳戶的 [存取控制 (IAM)] 索引標籤：

![儲存體帳戶 IAM](./media/azure-storage-account.png)

請確定您具有此儲存體帳戶的擁有者存取權限，以確保您可以新增角色指派。 若您沒有指派角色權限，[新增角色指派] 選項將會被停用。

按一下 [新增角色指派] 圖格中的 [ **新增** ] 按鈕，以新增角色。

![儲存體帳戶 IAM 新增角色指派](./media/azure-add-role-assignment.png)

* 指派 **儲存體 Blob 資料參與者** 角色，如上面的螢幕擷取畫面所示。
* 從 [**指派存取權給**] 下拉式清單中，選取 [**遠端轉譯帳戶** 系統指派的受控識別]。
* 在最後一個下拉式清單中，選取您的訂閱和遠端轉譯帳戶。
* 按一下 [儲存] 以儲存您的變更。

> [!WARNING]
> 如果系統未列出您的遠端轉譯帳戶，請參閱此[疑難排解一節](../resources/troubleshoot.md#cant-link-storage-account-to-arr-account)。

> [!IMPORTANT]
> Azure 角色指派會由 Azure 儲存體快取，因此當您授與存取權給遠端轉譯帳戶，以及可用來存取您的儲存體帳戶時，可能會有最多30分鐘的延遲。 如需詳細資料，請參閱 [ (AZURE RBAC) 檔中的 azure 角色型存取控制](../../role-based-access-control/troubleshooting.md#role-assignment-changes-are-not-being-detected) 。

## <a name="next-steps"></a>後續步驟

* [驗證](authentication.md)
* [使用 Azure 前端 API 進行驗證](frontend-apis.md) (機器翻譯)
* [PowerShell 指令碼範例](../samples/powershell-example-scripts.md)
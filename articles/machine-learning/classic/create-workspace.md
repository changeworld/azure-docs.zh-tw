---
title: ML Studio (傳統) ：建立工作區-Azure
description: 若要使用 Azure Machine Learning Studio (傳統) ，您需要有 Machine Learning Studio (傳統的) 工作區。 此工作區包含您建立、管理及發行實驗所需的工具。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 12/07/2017
ms.openlocfilehash: 700cc461563f145f58e02f7ed9a09b2899a4eb5b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91342154"
---
# <a name="create-and-share-an-machine-learning-studio-classic-workspace"></a>建立和共用 Machine Learning Studio (傳統) 工作區

**適用於：** ![是](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (傳統版)![否](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../compare-azure-ml-to-studio-classic.md)

若要使用 Azure Machine Learning Studio (傳統) ，您需要有 Machine Learning Studio (傳統的) 工作區。 此工作區包含您建立、管理及發行實驗所需的工具。

## <a name="create-a-studio-classic-workspace"></a>建立 Studio (傳統) 工作區

若要在 Machine Learning Studio (傳統) 中開啟工作區，您必須登入您用來建立工作區的 Microsoft 帳戶，或者您需要收到來自擁有者的邀請，才能加入工作區。 您可以從 Azure 入口網站管理工作區，其中包括設定存取的能力。

1. 登入 [Azure 入口網站](https://portal.azure.com/)

    > [!NOTE]
    > 若要登入並建立 Studio (傳統) 工作區，您必須是 Azure 訂用帳戶管理員。 
    >
    > 

2. 按一下 [ **+ 新增**]

3. 在 [搜尋] 方塊中，輸入 **Machine Learning Studio (傳統) 工作區** ，然後選取相符的專案。 然後按一下頁面底部的 [建立]****。

4. 輸入您的工作區資訊：

   - 工作區名稱** 可能最多 260 個字元，結尾不可為空格。 名稱不能包含下列字元︰`< > * % & : \ ? + /`
   - 會使用您選擇 (或建立) 的 Web 服務方案**，以及您選取的相關聯定價層**，如果您從此工作區中部署 web 服務。

     ![建立新的 Studio (傳統) 工作區](./media/create-workspace/create-new-workspace.png)

5. 按一下頁面底部的 [新增] 。

   機器學習服務目前可用於數量有限的區域。 如果您的訂用帳戶未包含其中一個區域，您可能會看到錯誤訊息：「您在允許的區域中沒有任何訂用帳戶」。  為了要求將區域新增至您的訂用帳戶，請從 Azure 入口網站中建立新的 Microsoft 支援要求，選取 [計費]**** 做為問題類型，並遵照提示來提交您的要求。


> [!NOTE]
> Machine Learning Studio (傳統) 會依賴您提供的 Azure 儲存體帳戶，以在執行工作流程時儲存中繼資料。 建立工作區之後，如果儲存體帳戶遭到刪除，或存取金鑰變更，工作區會停止運作，並且該工作區中的所有實驗將會失敗。
如果您不小心刪除儲存體帳戶，請使用與所刪除儲存體帳戶相同的區域中的相同名稱來重新建立儲存體帳戶並重新同步存取金鑰。 如果您變更了儲存體帳戶存取金鑰，請使用 Azure 入口網站在工作區中重新同步處理存取金鑰。

部署工作區之後，您可以在 Machine Learning Studio (傳統) 中開啟該工作區。

1. 流覽至 Machine Learning Studio (傳統) [https://studio.azureml.net/](https://studio.azureml.net/) 。

2. 選取您右上角的工作區。

    ![選取工作區](./media/create-workspace/open-workspace.png)

3. 按一下 [我的實驗]****。

    ![開啟實驗](./media/create-workspace/my-experiments.png)

如需有關管理 Studio (傳統) 工作區的詳細資訊，請參閱 [管理 Azure Machine Learning studio (傳統) 工作區](manage-workspace.md)。
如果您在建立工作區時遇到問題，請參閱 [疑難排解指南：建立及連接至 Machine Learning Studio (傳統) 工作區](index.yml)。


## <a name="share-an-azure-machine-learning-studio-classic-workspace"></a>共用 Azure Machine Learning Studio (傳統) 工作區
一旦建立 Machine Learning Studio (傳統) 工作區，您就可以邀請使用者加入您的工作區，以共用您的工作區和其所有實驗、資料集等的存取權。您可以用下列其中一種角色來新增使用者：

* **使用者** - 工作區使用者可以在工作區中建立、開啟、修改和刪除實驗、資料集等。
* **擁有者** - 除了使用者可以執行的動作以外，擁有者可以邀請和移除工作區中的使用者。

> [!NOTE]
> 建立工作區的系統管理員帳戶會自動以工作區擁有者身分新增至工作區。 不過，其他的系統管理員或該訂用帳戶中的使用者不會自動授與工作區的存取權 - 您必須先明確邀請他們。
> 
> 

### <a name="to-share-a-studio-classic-workspace"></a>共用 Studio (傳統) 工作區

1. 登入 Machine Learning Studio (傳統) [https://studio.azureml.net/Home](https://studio.azureml.net/Home)

2. 按一下左面板中的 [設定]****

3. 按一下 [ **使用者** ] 索引標籤

4. 按一下頁面底部的 [邀請更多使用者]****

    ![Studio 設定](./media/create-workspace/settings.png)

5. 輸入一或多個電子郵件地址。 使用者需要有效的 Microsoft 帳戶或組織帳戶 (來自 Azure Active Directory)。

6. 選取您是否想要將使用者新增為擁有者或使用者。

7. 按一下 [確定]**** 核取記號按鈕。

您新增的每個使用者會收到一封電子郵件，其中含有如何登入共用工作區的指示。

> [!NOTE]
> 若要讓使用者能夠部署或管理此工作區中的 web 服務，它們必須為參與者或 Azure 訂用帳戶中的系統管理員。 

## <a name="troubleshoot-storage-accounts"></a>針對儲存體帳戶進行疑難排解


機器學習服務需要儲存體帳戶來儲存資料。 您可以使用現有的儲存體帳戶，也可以在建立新的 Machine Learning Studio (傳統) 工作區時，建立新的儲存體帳戶 (如果您有建立新儲存體帳戶) 的配額。

建立新的 Machine Learning Studio (傳統) 工作區之後，您可以使用您用來建立工作區的 (，登入 Machine Learning Studio) 傳統 Microsoft 帳戶。 如果您遇到錯誤訊息：「找不到工作區」 (類似下列螢幕擷取畫面) ，請使用下列步驟來刪除瀏覽器 cookie。

![找不到工作區](media/troubleshooting-creating-ml-workspace/screen3.png)

**刪除瀏覽器 Cookie**

1. 如果您是使用 Internet Explorer，請按一下右上角的 [工具]**** 按鈕，然後選取 [網際網路選項]****。  

   ![網際網路選項](media/troubleshooting-creating-ml-workspace/screen4.png)

2. 在 [一般]**** 索引標籤下，按一下 [刪除...]****

   ![一般索引標籤](media/troubleshooting-creating-ml-workspace/screen5.png)

3. 在 [刪除瀏覽歷程記錄]**** 對話方塊中，確定已選取 [Cookie 與網站資料]****，然後按一下 [刪除]****。

   ![刪除 cookie](media/troubleshooting-creating-ml-workspace/screen6.png)

刪除 cookie 之後，請重新開機瀏覽器，然後移至 [Microsoft Azure Machine Learning Studio (傳統) ](https://studio.azureml.net) ] 頁面。 出現輸入使用者名稱和密碼的提示時，請輸入建立工作區所使用的同一個 Microsoft 帳戶。


## <a name="next-steps"></a>後續步驟

如需管理工作區的詳細資訊，請參閱 [管理 Azure Machine Learning Studio (傳統) 工作區](manage-workspace.md)。

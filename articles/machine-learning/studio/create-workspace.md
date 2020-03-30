---
title: 建立工作區
titleSuffix: ML Studio (classic) - Azure
description: 要使用 Azure 機器學習工作室（經典），需要有機器學習工作室（經典）工作區。 此工作區包含您建立、管理及發行實驗所需的工具。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 12/07/2017
ms.openlocfilehash: 1a391a7a061d1382b5e07b45625c44fc0f5dec54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204455"
---
# <a name="create-and-share-an-azure-machine-learning-studio-classic-workspace"></a>創建和共用 Azure 機器學習工作室（經典）工作區

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

要使用 Azure 機器學習工作室（經典），需要有機器學習工作室（經典）工作區。 此工作區包含您建立、管理及發行實驗所需的工具。

## <a name="create-a-studio-classic-workspace"></a>創建工作室（經典）工作區

1. 登錄到 Azure[門戶](https://portal.azure.com/)

    > [!NOTE]
    > 要登錄並創建 Studio（經典）工作區，您需要是 Azure 訂閱管理員。 
    >
    > 

2. 按一下 **"新建"**

3. 在搜索框中，鍵入**機器學習工作室（經典）工作區**並選擇匹配項。 然後按一下頁面底部的 [建立]****。

4. 輸入您的工作區資訊：

   - 工作區名稱** 可能最多 260 個字元，結尾不可為空格。 名稱不能包含下列字元︰`< > * % & : \ ? + /`
   - 會使用您選擇 (或建立) 的 Web 服務方案**，以及您選取的相關聯定價層**，如果您從此工作區中部署 web 服務。

     ![創建新的 Studio（經典）工作區](./media/create-workspace/create-new-workspace.png)

5. 按一下 **[建立]**。

> [!NOTE]
> 機器學習工作室（經典版）依賴于在執行其工作流時提供的 Azure 存儲帳戶以保存中間資料。 建立工作區之後，如果儲存體帳戶遭到刪除，或存取金鑰變更，工作區會停止運作，並且該工作區中的所有實驗將會失敗。
如果您不小心刪除儲存體帳戶，請使用與所刪除儲存體帳戶相同的區域中的相同名稱來重新建立儲存體帳戶並重新同步存取金鑰。 如果您變更了儲存體帳戶存取金鑰，請使用 Azure 入口網站在工作區中重新同步處理存取金鑰。

部署工作區後，您可以在機器學習工作室（經典）中打開它。

1. 流覽到機器學習工作室（經典）在[https://studio.azureml.net/](https://studio.azureml.net/)。

2. 選取您右上角的工作區。

    ![選取工作區](./media/create-workspace/open-workspace.png)

3. 按一下 [我的實驗]****。

    ![開啟實驗](./media/create-workspace/my-experiments.png)

有關管理工作室（經典）工作區的資訊，請參閱管理[Azure 機器學習工作室（經典）工作區](manage-workspace.md)。
如果您在創建工作區時遇到問題，請參閱[故障排除指南：創建並連接到機器學習工作室（經典）工作區](troubleshooting-creating-ml-workspace.md)。


## <a name="share-an-azure-machine-learning-studio-classic-workspace"></a>共用 Azure 機器學習工作室（經典）工作區
創建機器學習工作室（經典）工作區後，您可以邀請使用者到工作區共用對工作區及其所有實驗、資料集、筆記本等的存取權限。您可以在兩個角色之一中添加使用者：

* **使用者** - 工作區使用者可以在工作區中建立、開啟、修改和刪除實驗、資料集等。
* **擁有者** - 除了使用者可以執行的動作以外，擁有者可以邀請和移除工作區中的使用者。

> [!NOTE]
> 建立工作區的系統管理員帳戶會自動以工作區擁有者身分新增至工作區。 不過，其他的系統管理員或該訂用帳戶中的使用者不會自動授與工作區的存取權 - 您必須先明確邀請他們。
> 
> 

### <a name="to-share-a-studio-classic-workspace"></a>共用工作室（經典）工作區

1. 登錄機器學習工作室（經典）[https://studio.azureml.net/Home](https://studio.azureml.net/Home)

2. 按一下左面板中的 [設定]****

3. 按一下 **"使用者"** 選項卡

4. 按一下頁面底部的 [邀請更多使用者]****

    ![Studio 設定](./media/create-workspace/settings.png)

5. 輸入一或多個電子郵件地址。 使用者需要有效的 Microsoft 帳戶或組織帳戶 (來自 Azure Active Directory)。

6. 選取您是否想要將使用者新增為擁有者或使用者。

7. 按一下 [確定]**** 核取記號按鈕。

您新增的每個使用者會收到一封電子郵件，其中含有如何登入共用工作區的指示。

> [!NOTE]
> 若要讓使用者能夠部署或管理此工作區中的 web 服務，它們必須為參與者或 Azure 訂用帳戶中的系統管理員。 




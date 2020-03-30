---
title: 對工作區進行故障排除
titleSuffix: ML Studio (classic) - Azure
description: 本指南為設置 Azure 機器學習工作室（經典）工作區時遇到的一些常見挑戰提供了解決方案。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 58ccd63e16382aca8e16eb67efba951a055eb254
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217826"
---
# <a name="troubleshooting-guide-create-and-connect-to-an-azure-machine-learning-studio-classic-workspace"></a>故障排除指南：創建並連接到 Azure 機器學習工作室（經典）工作區

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

本指南為設置 Azure 機器學習工作室（經典）工作區時遇到的一些常見挑戰提供了解決方案。

## <a name="workspace-owner"></a>工作區擁有者
要在機器學習工作室（經典版）中打開工作區，您必須登錄到用於創建工作區的 Microsoft 帳戶，或者需要收到擁有者的邀請才能加入工作區。 您可以從 Azure 入口網站管理工作區，其中包括設定存取的能力。

有關管理工作區的詳細資訊，請參閱[管理 Azure 機器學習工作室（經典）工作區]。

[管理 Azure 機器學習工作室（經典）工作區]: manage-workspace.md

## <a name="allowed-regions"></a>允許區域
機器學習服務目前可用於數量有限的區域。 如果您的訂閱不包括這些區域之一，您可能會看到錯誤訊息"在允許的區域中沒有訂閱"。

為了要求將區域新增至您的訂用帳戶，請從 Azure 入口網站中建立新的 Microsoft 支援要求，選取 [計費]**** 做為問題類型，並遵照提示來提交您的要求。

## <a name="storage-account"></a>儲存體帳戶
機器學習服務需要儲存體帳戶來儲存資料。 您可以使用現有存儲帳戶，也可以創建新的存儲帳戶，當您創建新的機器學習工作室（經典）工作區（如果您有配額來創建新的存儲帳戶）。

創建新的機器學習工作室（經典）工作區後，您可以使用用於創建工作區的 Microsoft 帳戶登錄到機器學習工作室（經典）。 如果您遇到錯誤訊息"找不到工作區"（類似于以下螢幕截圖），請使用以下步驟刪除您的瀏覽器 Cookie。

![找不到工作區](media/troubleshooting-creating-ml-workspace/screen3.png)

**刪除瀏覽器 Cookie**

1. 如果您是使用 Internet Explorer，請按一下右上角的 [工具]**** 按鈕，然後選取 [網際網路選項]****。  

   ![網際網路選項](media/troubleshooting-creating-ml-workspace/screen4.png)

2. 在 [一般]**** 索引標籤下，按一下 [刪除...]****

   ![一般索引標籤](media/troubleshooting-creating-ml-workspace/screen5.png)

3. 在 [刪除瀏覽歷程記錄]**** 對話方塊中，確定已選取 [Cookie 與網站資料]****，然後按一下 [刪除]****。

   ![刪除 cookie](media/troubleshooting-creating-ml-workspace/screen6.png)

刪除 Cookie 後，重新開機瀏覽器，然後轉到[Microsoft Azure 機器學習工作室（經典）](https://studio.azureml.net)頁面。 出現輸入使用者名稱和密碼的提示時，請輸入建立工作區所使用的同一個 Microsoft 帳戶。

## <a name="comments"></a>註解

我們的目標是讓機器學習服務經驗盡可能完美。 請將任何建議或問題貼在 [Azure Machine Learning 論壇](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) 中，以協助我們為您提供更好的服務。

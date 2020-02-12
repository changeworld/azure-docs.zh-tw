---
title: 針對工作區進行疑難排解
titleSuffix: ML Studio (classic) - Azure
description: 本指南提供一些在您設定 Azure Machine Learning Studio （傳統）工作區時經常遇到之挑戰的解決方案。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: a4385d88912e7c4d391baa8f5bc50be1e07f4fa7
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152783"
---
# <a name="troubleshooting-guide-create-and-connect-to-an-azure-machine-learning-studio-classic-workspace"></a>疑難排解指南：建立及連接到 Azure Machine Learning Studio （傳統）工作區
本指南提供一些在您設定 Azure Machine Learning Studio （傳統）工作區時經常遇到之挑戰的解決方案。



## <a name="workspace-owner"></a>工作區擁有者
若要在 Machine Learning Studio （傳統）中開啟工作區，您必須登入您用來建立工作區的 Microsoft 帳戶，或者您需要收到來自擁有者的邀請，才能加入工作區。 您可以從 Azure 入口網站管理工作區，其中包括設定存取的能力。

如需管理工作區的詳細資訊，請參閱[管理 Azure Machine Learning Studio （傳統）工作區]。

[管理 Azure Machine Learning Studio （傳統）工作區]: manage-workspace.md

## <a name="allowed-regions"></a>允許區域
機器學習服務目前可用於數量有限的區域。 如果您的訂用帳戶並未包含這其中一個區域，您可能會看見錯誤訊息「您在允許的區域中沒有任何訂用帳戶」。

為了要求將區域新增至您的訂用帳戶，請從 Azure 入口網站中建立新的 Microsoft 支援要求，選取 [計費] 做為問題類型，並遵照提示來提交您的要求。

## <a name="storage-account"></a>儲存體帳戶
機器學習服務需要儲存體帳戶來儲存資料。 您可以使用現有的儲存體帳戶，也可以在建立新的 Machine Learning Studio （傳統）工作區時建立新的儲存體帳戶（如果您有建立新儲存體帳戶的配額）。

建立新的 Machine Learning Studio （傳統）工作區之後，您可以使用您用來建立工作區的 Microsoft 帳戶登入 Machine Learning Studio （傳統）。 如果您遇到錯誤訊息「找不到工作區」(類似於下列螢幕擷取畫面)，請使用下列步驟來刪除您的瀏覽器 Cookie。

![找不到工作區](media/troubleshooting-creating-ml-workspace/screen3.png)

**刪除瀏覽器 Cookie**

1. 如果您是使用 Internet Explorer，請按一下右上角的 [工具] 按鈕，然後選取 [網際網路選項]。  

   ![網際網路選項](media/troubleshooting-creating-ml-workspace/screen4.png)

2. 在 [一般] 索引標籤下，按一下 [刪除...]

   ![[一般] 索引標籤](media/troubleshooting-creating-ml-workspace/screen5.png)

3. 在 [刪除瀏覽歷程記錄] 對話方塊中，確定已選取 [Cookie 與網站資料]，然後按一下 [刪除]。

   ![刪除 cookie](media/troubleshooting-creating-ml-workspace/screen6.png)

刪除 cookie 之後，請重新開機瀏覽器，然後移至 [ [Microsoft Azure Machine Learning Studio （傳統）](https://studio.azureml.net) ] 頁面。 出現輸入使用者名稱和密碼的提示時，請輸入建立工作區所使用的同一個 Microsoft 帳戶。

## <a name="comments"></a>註解

我們的目標是讓機器學習服務經驗盡可能完美。 請將任何建議或問題貼在 [Azure Machine Learning 論壇](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) 中，以協助我們為您提供更好的服務。

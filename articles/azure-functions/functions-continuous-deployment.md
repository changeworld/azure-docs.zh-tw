---
title: Azure Functions 的持續部署
description: 使用 Azure 應用服務的連續部署功能發佈函數。
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.topic: conceptual
ms.date: 09/25/2019
ms.openlocfilehash: cc1e100a0c2e652ab081869409fd24dbf88017a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277020"
---
# <a name="continuous-deployment-for-azure-functions"></a>Azure Functions 的持續部署

可以使用 Azure 函數使用[原始程式碼管理集成](functions-deployment-technologies.md#source-control)連續部署代碼。 原始程式碼管理集成支援一個工作流，其中代碼更新將觸發部署到 Azure。 如果您是 Azure 函數的新功能，則通過查看[Azure 函數概述](functions-overview.md)開始。

對於集成多個頻繁貢獻的專案而言，持續部署是一個不錯的選擇。 使用連續部署時，您可以為代碼維護一個真實來源，從而允許團隊輕鬆協作。 可以從以下原始程式碼位置配置 Azure 函數中的連續部署：

* [Azure 存儲庫](https://azure.microsoft.com/services/devops/repos/)
* [GitHub](https://github.com)
* [比特桶](https://bitbucket.org/)

Azure 中函數的部署單元是函數應用。 同時部署函數應用中的所有函數。 啟用連續部署後，對 Azure 門戶中的函數代碼的訪問配置為*唯讀，* 因為真相源設置為位於其他位置。

## <a name="requirements-for-continuous-deployment"></a>持續部署的要求

要使連續部署成功，目錄結構必須與 Azure 函數期望的基本資料夾結構相容。

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

>[!NOTE]  
> 在消費計畫上運行的 Linux 應用尚不支援持續部署。 

## <a name="set-up-continuous-deployment"></a><a name="credentials"></a>設定連續部署

要配置現有功能應用的連續部署，請完成這些步驟。 這些步驟演示了與 GitHub 存儲庫的集成，但類似的步驟適用于 Azure 存儲庫或其他原始程式碼存儲庫。

1. 在[Azure 門戶](https://portal.azure.com)中的函數應用中，選擇**平臺功能** > **部署中心**。

    ![開放式部署中心](./media/functions-continuous-deployment/platform-features.png)

2. 在**部署中心**中，選擇**GitHub，** 然後選擇 **"授權**"。 如果您已授權 GitHub，請選擇"**繼續**"。 

    ![Azure 應用服務部署中心](./media/functions-continuous-deployment/github.png)

3. 在 GitHub 中，選擇 **"授權 AzureApp 服務"** 按鈕。 

    ![授權 Azure 應用服務](./media/functions-continuous-deployment/authorize.png)
    
    在 Azure 門戶中的**部署中心**中，選擇 **"繼續**"。

4. 選擇以下生成提供程式之一：

    * **應用服務生成服務**：當您不需要生成或需要通用生成時，最佳。
    * **Azure 管道（預覽）：** 當您需要對生成進行更多控制時，最佳。 此提供程式當前處於預覽狀態。

    ![選擇生成提供程式](./media/functions-continuous-deployment/build.png)

5. 配置特定于您指定的原始程式碼管理選項的資訊。 對於 GitHub，必須輸入或選擇**組織**、**存儲庫**和**分支**的值。 這些值基於代碼的位置。 然後，選取 [繼續]****。

    ![設定 GitHub](./media/functions-continuous-deployment/github-specifics.png)

6. 查看所有詳細資訊，然後選擇 **"完成"** 以完成部署配置。

    ![總結](./media/functions-continuous-deployment/summary.png)

該過程完成後，指定源中的所有代碼將部署到你的應用。 此時，部署源中的更改將觸發這些更改部署到 Azure 中的函數應用。

## <a name="deployment-scenarios"></a>部署案例

<a name="existing"></a>

### <a name="move-existing-functions-to-continuous-deployment"></a>將現有函式移至持續部署

如果在[Azure 門戶](https://portal.azure.com)中已寫入函數，並且希望在切換到連續部署之前下載應用的內容，請轉到函數應用的 **"概述"** 選項卡。 選擇 **"下載應用內容"** 按鈕。

![下載應用內容](./media/functions-continuous-deployment/download.png)

> [!NOTE]
> 配置連續集成後，您無法在"功能"門戶中編輯原始檔案。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Azure Functions 的最佳做法](functions-best-practices.md)

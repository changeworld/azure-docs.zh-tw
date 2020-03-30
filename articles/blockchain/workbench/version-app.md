---
title: 區塊鏈應用版本控制 - Azure 區塊鏈工作臺
description: 如何在 Azure 區塊鏈工作臺預覽中使用應用程式版本。
ms.date: 11/20/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 45219790cf0cd064e0fcd456e262b2f93aa03ac9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74323917"
---
# <a name="azure-blockchain-workbench-preview-application-versioning"></a>Azure 區塊鏈工作臺預覽應用程式版本控制

您可以創建和使用 Azure 區塊鏈工作臺預覽應用的多個版本。 如果上傳相同應用程式的多個版本，就可以取得版本歷程記錄，而且使用者可以選擇他們想要使用哪一個版本。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisites

* Blockchain Workbench 部署。 有關詳細資訊，請參閱[Azure 區塊鏈工作臺部署](deploy.md)，瞭解有關部署的詳細資訊
* 已在 Blockchain Workbench 中部署的區塊鏈應用程式。 請參閱[在 Azure Blockchain Workbench 中建立區塊鏈應用程式](create-app.md)

## <a name="add-an-app-version"></a>新增應用程式版本

若要加入新版本，請將新設定和智慧型合約檔案上傳到 Blockchain Workbench。

1. 在網頁瀏覽器中瀏覽至 Blockchain Workbench 的網址。 例如，`https://{workbench URL}.azurewebsites.net/` 如需如何尋找 Blockchain Workbench 網址的資訊，請參閱 [Blockchain Workbench Web URL](deploy.md#blockchain-workbench-web-url)
2. 以[區塊鏈工作臺管理員](manage-users.md#manage-blockchain-workbench-administrators)身份登錄。
3. 選取您想要用另一個版本來更新的區塊鏈應用程式。
4. 選取 [新增版本]****。 [新增版本]**** 窗格隨即顯示。
5. 選擇要上傳的新版合約設定和合約程式碼檔案。 系統會自動驗證組態檔。 先修正任何驗證錯誤再部署應用程式。
6. 選取 [新增版本]**** 來加入新的區塊鏈應用程式版本。

    ![加入新版本](media/version-app/add-version.png)

區塊鏈應用程式的部署可能需要數分鐘的時間。 當部署完成時，請重新整理應用程式頁面。 選擇應用程式，然後選取 [版本歷程記錄]**** 按鈕會顯示應用程式的版本歷程記錄。

> [!IMPORTANT]
> 應用程式的先前版本會停用。 您可以個別重新啟用過去的版本。
>
> 如果在新版本中對應用程式角色進行了變更，則您可能需要重新新增成員到應用程式角色。

## <a name="using-app-versions"></a>使用應用程式版本

根據預設，應用程式最新已啟用的版本會用於 Blockchain Workbench 中。 如果您想要使用應用程式的先前版本，您需要先從應用程式頁面中選擇版本。

1. 在 Blockchain Workbench 應用程式區段中，選取包含您想要使用的合約之應用程式核取方塊。 如果已啟用先前的版本，[版本歷程記錄] 按鈕就可供使用。
2. 選取 [版本歷程記錄]**** 按鈕。
3. 在 [版本歷程記錄] 窗格中，藉由選取 [修改日期]** 欄中的連結來選擇應用程式的版本。

    ![選擇先前版本](media/version-app/use-version.png)

    您可以建立新的合約，或在先前版本的合約上採取動作。 應用程式的版本會顯在應用程式名稱之後，並且會顯示舊版相關的警示。

## <a name="next-steps"></a>後續步驟

* [Azure Blockchain Workbench 疑難排解](troubleshooting.md)

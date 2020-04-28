---
title: 區塊鏈應用程式版本設定-Azure Blockchain Workbench
description: 如何使用 Azure Blockchain Workbench Preview 中的應用程式版本。
ms.date: 11/20/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 45219790cf0cd064e0fcd456e262b2f93aa03ac9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74323917"
---
# <a name="azure-blockchain-workbench-preview-application-versioning"></a>Azure Blockchain Workbench 預覽應用程式版本設定

您可以建立和使用多個版本的 Azure Blockchain Workbench Preview 應用程式。 如果上傳相同應用程式的多個版本，就可以取得版本歷程記錄，而且使用者可以選擇他們想要使用哪一個版本。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisites

* Blockchain Workbench 部署。 如需詳細資訊，請參閱[Azure Blockchain Workbench 部署](deploy.md)，以取得部署的詳細資料
* 已在 Blockchain Workbench 中部署的區塊鏈應用程式。 請參閱[在 Azure Blockchain Workbench 中建立區塊鏈應用程式](create-app.md)

## <a name="add-an-app-version"></a>新增應用程式版本

若要加入新版本，請將新設定和智慧型合約檔案上傳到 Blockchain Workbench。

1. 在網頁瀏覽器中瀏覽至 Blockchain Workbench 的網址。 例如，`https://{workbench URL}.azurewebsites.net/` 如需如何尋找 Blockchain Workbench 網址的資訊，請參閱 [Blockchain Workbench Web URL](deploy.md#blockchain-workbench-web-url)
2. 以[Blockchain Workbench 系統管理員](manage-users.md#manage-blockchain-workbench-administrators)身分登入。
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

---
title: 視覺化工作室代碼的 Azure 策略擴展
description: 瞭解如何使用 Visual Studio 代碼的 Azure 策略擴展來查找資源管理器別名。
ms.date: 03/07/2020
ms.topic: how-to
ms.openlocfilehash: 0e9123f2975bda0d61e9d6e9bf894ecd359e6c5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264670"
---
# <a name="use-azure-policy-extension-for-visual-studio-code"></a>使用 Azure 策略擴展對視覺化工作室代碼

> 應用於 Azure 策略擴展版本**0.0.21**和更高版本

瞭解如何使用 Visual Studio 代碼的 Azure 策略擴展來查找[別名](../concepts/definition-structure.md#aliases)並查看資源和策略。 首先，我們將介紹如何在視覺化工作室代碼中安裝 Azure 策略擴展。 然後，我們將演練如何查找別名。

視覺化工作室代碼的 Azure 策略擴展可以安裝在視覺工作室代碼支援的所有平臺上。 此支援包括 Windows、Linux 和 macOS。

> [!NOTE]
> 對視覺化工作室代碼的 Azure 策略擴展中查看的策略進行了本地更改，不會同步到 Azure。

## <a name="prerequisites"></a>Prerequisites

若要完成此文章中的步驟，將會需要下列項目：

- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。
- [視覺工作室代碼](https://code.visualstudio.com)。

## <a name="install-azure-policy-extension"></a>安裝 Azure 策略擴展

滿足先決條件後，可以通過以下步驟安裝 Visual Studio 代碼的 Azure 策略擴展：

1. 開啟 Visual Studio Code。

1. 從功能表列轉到 **"查看** > **擴展"。**

1. 在搜索框中，輸入**Azure 策略**。

1. 從搜尋結果中選擇**Azure 策略**，然後選擇 **"安裝**"。

1. 必要時選擇 **"重新載入**"。

## <a name="set-the-azure-environment"></a>設定 Azure 環境

對於國家雲使用者，請按照以下步驟首先設置 Azure 環境：

1. 選擇**檔_首選項\設置**。

1. 搜索以下字串 _：Azure：雲_

1. 從清單中選擇國家雲：

   ![為視覺化工作室代碼設置預設 Azure 雲登錄](../media/extension-for-vscode/set-default-azure-cloud-sign-in.png)

## <a name="connect-to-an-azure-account"></a>連接到 Azure 帳戶

要評估資源和查找別名，必須連接到 Azure 帳戶。 按照以下步驟從視覺化工作室代碼連接到 Azure：

1. 從 Azure 策略擴展或命令調色板登錄到 Azure。

   - Azure 策略擴展

     從 Azure 策略擴展中，選擇"**登錄到 Azure**"。

     ![Azure 雲登錄從 Azure 策略擴展外掛程式視覺化工作室代碼](../media/extension-for-vscode/azure-cloud-sign-in-policy-extension.png)

   - 命令選擇區

     從功能表列轉到 **"查看** > **命令調色板**"，然後輸入**Azure：登錄**。

     ![從命令調色板為視覺化工作室代碼的 Azure 雲登錄](../media/extension-for-vscode/azure-cloud-sign-in-command-palette.png)

1. 按照登錄說明登錄到 Azure。 連接後，Azure 帳戶名稱將顯示在視覺化工作室代碼視窗底部的狀態列上。

## <a name="select-subscriptions"></a>選取訂用帳戶

首次登錄時，Azure 策略擴展僅載入預設訂閱資源和策略。 要從顯示資源和策略添加或刪除訂閱，請按照以下步驟操作：

1. 從命令調色板或視窗頁啟動訂閱命令。

   - 命令調色板： 

     從功能表列轉到 **"查看** > **命令調色板**"，然後輸入**Azure：選擇訂閱**。

   - 視窗頁腳

     在螢幕底部的視窗頁腳中，選擇與 Azure 匹配的細分 **：\<您的帳戶\>**。

1. 使用篩選器框按名稱快速查找訂閱。 然後，檢查或刪除每個訂閱中的檢查，以設置 Azure 策略擴展顯示的訂閱。 添加或刪除要顯示的訂閱後，選擇 **"確定**"。

## <a name="search-for-and-view-resources"></a>搜索和查看資源

Azure 策略擴展按資來源提供者和 **"資源"** 窗格中的資源組列出所選訂閱中的資源。 樹狀檢視包括所選訂閱或訂閱級別的以下資源分組：

- **資來源提供者**
  - 每個註冊的資來源提供者，具有具有策略別名的資源和相關子資源
- **資源組**
  - 按其所位於的資源組進行的所有資源

預設情況下，擴展按具有策略別名的現有資源和資源篩選"資來源提供者"部分。 在 **"設置** > **擴展** > **Azure 策略**"中更改此行為，以查看所有資來源提供者，而無需篩選。

在單個訂閱中具有數百或數千個資源的客戶可能更喜歡可搜索的方式來查找其資源。 Azure 策略擴展允許使用以下步驟搜索特定資源：

1. 從 Azure 策略擴展或命令調色板啟動搜索介面。

   - Azure 策略擴展

     從 Azure 策略擴展中，將滑鼠懸停在 **"資源"** 面板上並選擇省略號，然後選擇 **"搜索資源**"。

   - 命令調色板：

     從功能表列轉到 **"查看**>**命令調色板**"，然後輸入 **"資源：搜索資源**"。

1. 如果選擇了多個訂閱進行顯示，請使用篩選器選擇要搜索的訂閱。

1. 使用篩選器選擇要搜索的資源組是以前選擇的訂閱的一部分。

1. 使用篩選器選擇要顯示的資源。 篩選器適用于資源名稱和資源類型。

## <a name="discover-aliases-for-resource-properties"></a>發現資源屬性的別名

選擇資源時，無論是通過搜索介面還是通過在樹狀檢視中選擇資源，Azure 策略副檔名將打開 JSON 檔，表示該資源及其所有資源管理器屬性值。

打開資源後，將滑鼠懸停在資源管理器屬性名稱或值上，如果存在 Azure 策略別名。 在此示例中，資源是`Microsoft.Compute/virtualMachines`資源類型，**屬性.storageProfile.imageReference.offer**屬性將懸停在上。 懸停顯示匹配的別名。

![Azure 策略擴展懸停顯示資源管理器屬性別名](../media/extension-for-vscode/extension-hover-shows-property-alias.png)

## <a name="search-for-and-view-policies-and-assignments"></a>搜索和查看策略和分配

Azure 策略擴展將策略類型和策略分配列為選擇在 **"策略"** 窗格中顯示的訂閱的樹狀檢視。 在單個訂閱中具有數百或數千個策略或分配的客戶可能更喜歡可搜索的方式來查找其策略或分配。 Azure 策略擴展允許使用以下步驟搜索特定策略或分配：

1. 從 Azure 策略擴展或命令調色板啟動搜索介面。

   - Azure 策略擴展

     從 Azure 策略擴展中，將滑鼠懸停在 **"策略"** 面板上並選擇省略號，然後選擇 **"搜索策略**"。

   - 命令調色板：

     從功能表列轉到 **"查看**>**命令調色板**"，然後輸入 **"策略：搜索策略**"。

1. 如果選擇了多個訂閱進行顯示，請使用篩選器選擇要搜索的訂閱。

1. 使用篩選器選擇要搜索的策略類型或分配，該策略類型或分配屬於以前選擇的訂閱的一部分。

1. 使用篩選器選擇要顯示的策略或顯示哪個策略。 篩選器適用于策略定義或策略分配的_顯示名稱_。

選擇策略或分配時，無論是通過搜索介面還是通過在樹狀檢視中選擇策略，Azure 策略擴展將打開表示策略或賦值及其所有資源管理器屬性值的 JSON。 擴展可以驗證打開的 Azure 策略 JSON 架構。

## <a name="sign-out"></a>登出

從功能表列轉到 **"查看** > **命令調色板**"，然後輸入**Azure：登出**。

## <a name="next-steps"></a>後續步驟

- 查看[Azure 策略示例](../samples/index.md)中的示例。
- 檢閱 [Azure 原則定義結構](../concepts/definition-structure.md)。
- 回顧[瞭解政策效果](../concepts/effects.md)。
- 瞭解如何[以程式設計方式創建策略](programmatically-create.md)。
- 瞭解如何[修復不合規資源](remediate-resources.md)。
- 使用[Azure 管理組查看](../../management-groups/overview.md)管理組的內容。請組織資源。
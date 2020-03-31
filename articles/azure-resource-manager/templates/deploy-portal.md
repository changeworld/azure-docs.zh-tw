---
title: 使用 Azure 門戶部署資源
description: 使用 Azure 門戶和 Azure 資源管理將資源部署到訂閱中的資源組。
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: ea2faf810b3a5d1b8fa46575201022a501cc7d58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153432"
---
# <a name="deploy-resources-with-arm-templates-and-azure-portal"></a>使用 ARM 範本和 Azure 門戶部署資源

瞭解如何將 Azure[門戶](https://portal.azure.com)與[Azure 資源管理器 （ARM） 範本](overview.md)一起部署 Azure 資源。 要瞭解如何管理資源，請參閱使用[Azure 門戶管理 Azure 資源](../management/manage-resources-portal.md)。

使用 Azure 門戶部署 Azure 資源通常涉及兩個步驟：

- 建立資源群組。
- 將資源部署到資源組。

此外，還可以部署 ARM 範本來創建 Azure 資源。

本文章示範這兩種方法。

## <a name="create-a-resource-group"></a>建立資源群組

1. 要創建新資源組，請從[Azure 門戶](https://portal.azure.com)中選擇**資源組**。

   ![選取資源群組](./media/deploy-portal/select-resource-groups.png)

1. 在"資源組"下，選擇 **"添加**"。

   ![新增資源群組](./media/deploy-portal/add-resource-group.png)

1. 選擇或輸入以下屬性值：

    - **訂閱**：選擇 Azure 訂閱。
    - **資源組**：為資源組指定名稱。
    - **區域**：指定 Azure 位置。 這是資源組存儲有關資源的中繼資料的位置。 為了符合法規，您可能會想要指定中繼資料的儲存位置。 一般情況下，我們建議您指定大部分資源所在的位置。 使用相同位置可簡化範本。

   ![設定群組值](./media/deploy-portal/set-group-properties.png)

1. 選擇 **"審閱" = 創建**。
1. 查看值，然後選擇 **"創建**"。
1. 選擇 **"刷新**"，然後才能在清單中看到新資源組。

## <a name="deploy-resources-to-a-resource-group"></a>將資源部署到資源組

創建資源組後，可以從應用商店將資源部署到該組。 Marketplace 針對常見的案例提供預先定義的解決方案。

1. 要啟動部署，請選擇從[Azure 門戶](https://portal.azure.com)**創建資源**。

   ![新增資源](./media/deploy-portal/new-resources.png)

1. 尋找您想要部署的資源類型。 資源按類別組織。 如果沒有看到要部署的特定解決方案，您可以在 Marketplace 搜尋。 以下螢幕截圖顯示 Ubuntu 伺服器已選中。

   ![選取 [資源類型]](./media/deploy-portal/select-resource-type.png)

1. 根據所選資源的類型，您必須在部署前設定相關的屬性集合。 對於所有類型，您必須選取目的地資源群組。 下圖演示如何創建 Linux 虛擬機器並將其部署到您創建的資源組。

   ![建立資源群組](./media/deploy-portal/select-existing-group.png)

   或者，您也可以決定在部署資源時建立資源群組。 選取 [新建] **** 並提供資源群組的名稱。

1. 您的部署隨即開始。 部署可能需要幾分鐘時間。 某些資源比其他資源需要更長的時間。 部署完成後，您就會看到通知。 選擇 **"轉到資源**以打開"

   ![檢視通知](./media/deploy-portal/view-notification.png)

1. 部署您的資源之後，您可以選取 [新增]****，將更多資源新增至資源群組。

   ![新增資源](./media/deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>從自訂範本部署資源

如果您想要執行部署，但不使用 Marketplace 中的任何範本，您可建立自訂範本以定義您的解決方案的基礎結構。 要瞭解如何創建範本，請參閱瞭解[ARM 範本的結構和語法](template-syntax.md)。

> [!NOTE]
> 入口網站介面不支援參考 [Key Vault 中的祕密](key-vault-parameter.md)。 請改用 [PowerShell](deploy-powershell.md) 或 [Azure CLI](deploy-cli.md)，在本機或從外部 URI 部署您的範本。

1. 要通過門戶部署自訂範本，請選擇"**創建資源**"，然後搜索**範本**。 然後選擇**範本部署**。

   ![搜尋範本部署](./media/deploy-portal/search-template.png)

1. 選取 [建立]****。
1. 您將看到幾個用於創建範本的選項：

    - **在編輯器中構建您自己的範本**：使用門戶範本編輯器創建範本。  編輯器能夠添加資源範本架構。
    - **常見範本**：有四個用於創建 Linux 虛擬機器、Windows 虛擬機器、Web 應用程式和 Azure SQL 資料庫的常見範本。
    - **載入 GitHub 快速入門範本**：使用現有的[快速入門範本](https://azure.microsoft.com/resources/templates/)。

   ![檢視選項](./media/deploy-portal/see-options.png)

    本教程提供了載入快速入門範本的說明。

1. 在 **"載入 GitHub 快速啟動"範本**下，鍵入或選擇**101 存儲帳戶創建**。

    您有兩個選擇：

    - **選擇範本**：部署範本。
    - **編輯範本**：在部署快速入門範本之前對其進行編輯。

1. 選擇 **"編輯"範本**以流覽門戶範本編輯器。 範本載入到編輯器中。 請注意，有兩個參數：**存儲帳戶類型**和**位置**。

   ![建立範本](./media/deploy-portal/show-json.png)

1. 對範本進行細微更改。 例如，將**存儲帳戶名稱**變數更新為：

    ```json
    "storageAccountName": "[concat('azstore', uniquestring(resourceGroup().id))]"
    ```

1. 選取 [儲存]****。 現在，您將看到門戶範本部署介面。 請注意您在範本中定義的兩個參數。
1. 輸入或選擇屬性值：

    - **訂閱**：選擇 Azure 訂閱。
    - **資源組**：選擇 **"創建新"** 並指定名稱。
    - **位置**：選擇 Azure 位置。
    - **存儲帳戶類型**：使用預設值。
    - **位置**：使用預設值。
    - **我同意上方所述的條款及條件**：(選取)

1. 選取 [購買]****。

## <a name="next-steps"></a>後續步驟

- 若要檢視稽核記錄，請參閱 [使用 Resource Manager 來稽核作業](../management/view-activity-logs.md)。
- 若要針對部署錯誤進行疑難排解，請參閱[檢視部署作業](deployment-history.md)。
- 要從部署或資源組匯出範本，請參閱匯出[ARM 範本](export-template-portal.md)。
- 要跨多個區域安全地推出服務，請參閱 Azure[部署管理員](deployment-manager-overview.md)。

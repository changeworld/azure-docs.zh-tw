---
title: 探索資源屬性
description: 說明如何搜尋資源屬性。
ms.topic: conceptual
ms.date: 06/10/2020
ms.openlocfilehash: c8bbe0dcb1bc9dc9751a1dc0d0b98a6368473546
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91327743"
---
# <a name="discover-resource-properties"></a>探索資源屬性

建立 Resource Manager 範本之前，您必須先瞭解哪些資源類型可供使用，以及要在範本中使用的值。 本文將說明一些您可以在範本中找到要包含屬性的方式。

## <a name="find-resource-provider-namespaces"></a>尋找資源提供者命名空間

ARM 範本中的資源會使用資源提供者命名空間和資源類型來定義。 例如，Microsoft. Storage/storageAccounts 是儲存體帳戶資源類型的完整名稱。 Microsoft. 儲存體是命名空間。 如果您還不知道您想要使用之資源類型的命名空間，請參閱 [Azure 服務的資源提供者](../management/azure-services-resource-providers.md)。

![Resource Manager 資源提供者命名空間對應](./media/view-resources/resource-provider-namespace-and-azure-service-mapping.png)

## <a name="export-templates"></a>匯出範本

若要取得現有資源的範本屬性，最簡單的方式就是匯出範本。 如需詳細資訊，請參閱 [Azure 入口網站中的單一和多重資源匯出至範本](./export-template-portal.md)。

## <a name="use-resource-manager-tools-extension"></a>使用 Resource Manager 工具延伸模組

Visual Studio Code 和 Azure Resource Manager 工具延伸模組可協助您瞭解每個資源類型所需的屬性。 它們提供 intellisense 和程式碼片段，以簡化您在範本中定義資源的方式。 如需詳細資訊，請參閱 [快速入門：使用 Visual Studio Code 建立 Azure Resource Manager 範本](./quickstart-create-templates-use-visual-studio-code.md#add-an-azure-resource)。

下列螢幕擷取畫面顯示儲存體帳戶資源已新增至範本：

![Resource Manager 工具延伸模組程式碼片段](./media/view-resources/resource-manager-tools-extension-snippets.png)

延伸模組也會提供設定屬性的選項清單。

![Resource Manager 工具延伸模組可設定值](./media/view-resources/resource-manager-tools-extension-configurable-properties.png)

## <a name="use-template-reference"></a>使用範本參考

Azure Resource Manager 範本參考是範本架構中最完整的資源。 您可以找到 API 版本、範本格式和屬性資訊。

1. 流覽至 [Azure Resource Manager 範本參考](/azure/templates/)。
1. 從左側導覽中，選取 [ **儲存體**]，然後選取 [ **所有資源**]。 [所有資源] 頁面會摘要說明資源類型和版本。

    ![範本參考資源版本](./media/view-resources/resource-manager-template-reference-resource-versions.png)

    如果您知道資源類型，可以使用下列 URL 格式直接移至此頁面： `https://docs.microsoft.com/azure/templates/{provider-namespace}/{resource-type}` 。

1. 選取最新版本。 建議使用最新的 API 版本。

    [ **範本格式** ] 區段會列出儲存體帳戶的所有屬性。 **sku** 位於清單中：

    ![範本參考儲存體帳戶格式](./media/view-resources/resource-manager-template-reference-storage-account-sku.png)

    在 [**屬性值**] 區段中，向下滾動以查看**Sku 物件**。 本文顯示 SKU 名稱允許的值：

    ![範本參考儲存體帳戶 SKU 值](./media/view-resources/resource-manager-template-reference-storage-account-sku-values.png)

    在頁面的結尾，[ **快速入門範本** ] 區段會列出一些包含資源類型的 Azure 快速入門範本：

    ![範本參考儲存體帳戶快速入門範本](./media/view-resources/resource-manager-template-reference-quickstart-templates.png)

範本參考會連結至每個 Azure 服務檔網站。  例如， [Key Vault 檔網站](../../key-vault/general/overview.md)：

![Resource Manager 範本參考 Key Vault](./media/view-resources/resource-manager-template-reference-key-vault.png)

## <a name="use-resource-explorer"></a>使用資源總管

資源總管內嵌在 Azure 入口網站中。 使用這個方法之前，您需要儲存體帳戶。 如果您沒有帳戶，請選取下列按鈕以建立一個：

[![部署至 Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-storage-account-create%2fazuredeploy.json)

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在搜尋方塊中，輸入 **resource explorer**，然後選取 [ **資源總管**]。

    ![螢幕擷取畫面顯示在 Azure 入口網站中搜尋資源總管。](./media/view-resources/azure-portal-resource-explorer.png)

1. 從左側展開 [訂用帳戶]， **然後展開您**的 Azure 訂用帳戶。 您可以在 **提供者** 或 **ResourceGroups**下找到儲存體帳戶。

    ![Azure 入口網站資源總管](./media/view-resources/azure-portal-resource-explorer-home.png)

    - **提供者**：展開**提供者**  ->  **Microsoft. 儲存體**  ->  **storageAccounts**，然後選取您的儲存體帳戶。
    - **ResourceGroups**：選取包含儲存體帳戶的資源群組，選取 [ **資源**]，然後選取儲存體帳戶。

    在右側，您會看到現有儲存體帳戶的 SKU 設定，如下所示：

    ![Azure 入口網站資源總管儲存體帳戶 sku](./media/view-resources/azure-portal-resource-explorer-sku.png)

## <a name="use-resourcesazurecom"></a>使用 Resources.azure.com

Resources.azure.com 是具有 Azure 訂用帳戶的任何人都可以存取的公用網站。 它目前為預覽狀態。  請考慮改用 [資源總管](#use-resource-explorer) 。 這項工具提供下列功能：

- 探索 Azure 資源管理 Api。
- 取得 API 檔和架構資訊。
- 直接在您自己的訂用帳戶中進行 API 呼叫。

若要示範如何使用此工具來取得架構資訊，您需要儲存體帳戶。 如果您沒有帳戶，請選取下列按鈕以建立一個：

[![部署至 Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-storage-account-create%2fazuredeploy.json)

1. 流覽至 [resources.azure.com](https://resources.azure.com/)。 工具需要幾分鐘的時間，才會在左窗格中受歡迎。
1. 選取 [ **訂閱**]。

    ![resource.azure.com api 對應](./media/view-resources/resources-azure-com-api-mapping.png)

    左邊的節點符合右邊的 API 呼叫。 您可以選取 [ **取得** ] 按鈕來進行 API 呼叫。
1. 從左側展開 [訂用帳戶]， **然後展開您**的 Azure 訂用帳戶。 您可以在 **提供者** 或 **ResourceGroups**下找到儲存體帳戶。

    - **提供者**：展開**提供者**  ->  **Microsoft. 儲存體**  ->  **storageAccounts**，然後流覽至儲存體帳戶。
    - **ResourceGroups**：選取包含儲存體帳戶的資源群組，然後選取 [ **資源**]。

    在右側，您會看到現有儲存體帳戶的 sku 設定，如下所示：

    ![Azure 入口網站資源總管儲存體帳戶 sku](./media/view-resources/azure-portal-resource-explorer-sku.png)

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何尋找範本架構資訊。 若要深入瞭解如何建立 Resource Manager 範本，請參閱 [瞭解 ARM 範本的結構和語法](./template-syntax.md)。

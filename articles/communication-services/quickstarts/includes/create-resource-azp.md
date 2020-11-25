---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: 051f903c0b203045886b80f72d868adc1fa85f73
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2020
ms.locfileid: "94886089"
---
## <a name="prerequisites"></a>必要條件

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/dotnet/)。

## <a name="create-azure-communication-resource"></a>建立 Azure 通訊資源

若要建立 Azure 通訊服務資源，請先登入 [Azure 入口網站](https://portal.azure.com)。 在頁面的左上角，選取 [+ 建立資源]。 

:::image type="content" source="../media/create-a-communication-resource/create-resource-plus-sign.png" alt-text="醒目提示 Azure 入口網站中建立資源按鈕的螢幕擷取畫面。":::

在 [搜尋 Marketplace] 輸入或入口網站頂端的搜尋列中，輸入 [通訊]。

:::image type="content" source="../media/create-a-communication-resource/searchbar-communication-portal.png" alt-text="顯示在搜尋列中搜尋通訊服務的螢幕擷取畫面。":::

在結果中選取 [通訊服務]，然後選取 [新增]。

:::image type="content" source="../media/create-a-communication-resource/add-communication-portal.png" alt-text="顯示 Azure 面板的螢幕擷取畫面，其中醒目提示新增按鈕。":::

您現在可以設定您的通訊服務資源。 在建立程序的第一個頁面上，系統會要求您指定：

* 訂用帳戶
* 資源群組 (您可以建立新的資源群組或選擇現有的資源群組)
* 通訊服務資源的名稱
* 資源要產生關聯的地理位置

在下一個步驟中，您可以將標記指派給資源。 標記可以用來組織您的 Azure 資源。 如需標記的詳細資訊，請參閱[資源標記文件](../../../azure-resource-manager/management/tag-resources.md)。

最後，您可以檢閱您的設定，並 **建立** 資源。 請注意，部署需要數分鐘的時間才能完成。

## <a name="manage-your-communication-services-resource"></a>管理您的通訊服務資源

若要管理您的通訊服務資源，請移至 [Azure 入口網站](https://portal.azure.com)，然後搜尋並選取 **Azure 通訊服務**。

在 [通訊服務] 頁面上，選取您的資源名稱。

資源的 [概觀] 頁面，其中包含瀏覽、停止、啟動、重新啟動和刪除等基本管理選項。 您可以在資源頁面的左側功能表中找到更多設定選項。
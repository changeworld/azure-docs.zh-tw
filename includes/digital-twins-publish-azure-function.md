---
author: baanders
description: 包含從 Visual Studio 發佈 Azure 函式之程式的檔案
ms.service: digital-twins
ms.topic: include
ms.date: 1/21/2021
ms.author: baanders
ms.openlocfilehash: 63b393f519ad29baa05fef046ee1e8ba9e5330d8
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98701149"
---
若要將專案發佈至 Azure 中的函數應用程式，請以滑鼠右鍵選取 *方案總管* 中的專案，然後選擇 [ **發行**]。

> [!IMPORTANT] 
> 在 Azure 中發佈至函式應用程式會在您的訂用帳戶上產生額外費用，與 Azure 數位 Twins 無關。

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-1.png" alt-text="顯示以滑鼠右鍵選取方案功能表 Visual Studio 的螢幕擷取畫面。功能表中的 [發行] 會反白顯示。":::

在接下來的 [發佈] 頁面中，保留選取預設目標 **Azure**，然後按 [下一步]。 

針對特定的目標，選擇 [Azure 函數應用程式 (Windows)]，然後按 [下一步]。

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-2.png" alt-text="[發佈 Azure 函數] 對話方塊中 Visual Studio 的螢幕擷取畫面。在特定目標頁面上選取了 Azure 函數應用程式 (Windows) 。":::

在 [Functions 執行個體] 頁面中，選擇您的訂用帳戶。 這應該會用您訂用帳戶的「資源群組」填入方塊。

選取您執行個體的資源群組，然後按 *+* 以建立新的 Azure 函式。

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-3.png" alt-text="[發佈 Azure 函數] 對話方塊中 Visual Studio 的螢幕擷取畫面。在 [函數實例] 頁面上，會反白顯示用來建立新函式的 + 按鈕。":::

在 [函數應用程式 (Windows) - 新建] 視窗中，填寫下列欄位：
* [名稱] 是 Azure 將用來裝載 Azure Functions 應用程式的使用情況方案名稱。 這也會成為裝載您實際函式的函數應用程式名稱。 您可以選擇您自己的唯一值，或保留預設建議。
* [訂用帳戶] 請務必與您要使用的訂用帳戶一致 
* [資源群組] 一定要是您希望使用的資源群組
* 將 [方案類型] 保留為 [使用]
* 在 [位置] 中，選擇符合您資源群組的位置
* 使用 [新增...] 連結，建立新的 **Azure 儲存體** 資源。 設定符合您資源群組的 [位置]，使用其他預設值，然後按 [確定]。

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-4.png" alt-text="[發佈 Azure 函數] 對話方塊中 Visual Studio 的螢幕擷取畫面。新函數應用程式的詳細資料會填入，包括名稱、訂用帳戶、資源群組、方案類型、位置和 Azure 儲存體。":::

然後，選取 [Create] \(建立\)。

這應該會帶您回到 [Functions 執行個體] 頁面，此時您的新函數應用程式會出現在您的資源群組底下。 按 [完成]。

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-5.png" alt-text="在 Visual Studio 中發佈 Azure 函式：Functions 執行個體 (在函數應用程式之後)":::

回到在主 Visual Studio 視窗中開啟的「發佈」 窗格中，確認所有資訊都正確無誤，然後選取 [發佈]。

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-6.png" alt-text="[發佈 Azure 函數] 對話方塊中 Visual Studio 的螢幕擷取畫面。新的函式應用程式會顯示在函數應用程式清單中，而且會有 [完成] 按鈕。":::

> [!NOTE]
> 如果您看到類似下面的彈出 :::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-7.png" alt-text="視窗： Visual Studio 的快顯視窗的螢幕擷取畫面，稱為「發行認證」。它包含使用者名稱和密碼的欄位，以及嘗試從 Azure 取出認證的按鈕。" border="false":::
> 請選取 [嘗試從 Azure 擷取認證] 和 [儲存]。
>
> 如果您看到關於 *升級 Azure 上的 Azure Functions 版本* 或 *您的函式執行階段版本不符合 Azure 中執行的版本* 的警告：
>
> 請遵循提示來升級至最新的 Azure Functions 執行階段版本。 如果您使用較舊版本的 Visual Studio，可能會發生此問題。

為了讓您的函式應用程式能夠存取 Azure 數位 Twins，它必須有系統管理的身分識別，而且有權存取您的 Azure 數位 Twins 實例。 接下來您要設定。

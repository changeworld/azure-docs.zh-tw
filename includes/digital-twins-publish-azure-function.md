---
author: baanders
description: 包含從 Visual Studio 發佈 Azure 函式之程式的檔案
ms.service: digital-twins
ms.topic: include
ms.date: 1/21/2021
ms.author: baanders
ms.openlocfilehash: 069a29f49172cf3bad6b7f7b6aca28f32f5d63b3
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98683838"
---
若要將專案發佈至 Azure 中的函數應用程式，請以滑鼠右鍵選取 *方案總管* 中的專案，然後選擇 [ **發行**]。

> [!IMPORTANT] 
> 在 Azure 中發佈至函式應用程式會在您的訂用帳戶上產生額外費用，與 Azure 數位 Twins 無關。

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-1.png" alt-text="Visual Studio：發佈專案":::

在接下來的 [發佈] 頁面中，保留選取預設目標 **Azure**，然後按 [下一步]。 

針對特定的目標，選擇 [Azure 函數應用程式 (Windows)]，然後按 [下一步]。

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-2.png" alt-text="在 Visual Studio 中發佈 Azure 函式：特定的目標":::

在 [Functions 執行個體] 頁面中，選擇您的訂用帳戶。 這應該會用您訂用帳戶的「資源群組」填入方塊。

選取您執行個體的資源群組，然後按 *+* 以建立新的 Azure 函式。

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-3.png" alt-text="在 Visual Studio 中發佈 Azure 函式：Functions 執行個體 (在函數應用程式之前)":::

在 [函數應用程式 (Windows) - 新建] 視窗中，填寫下列欄位：
* [名稱] 是 Azure 將用來裝載 Azure Functions 應用程式的使用情況方案名稱。 這也會成為裝載您實際函式的函數應用程式名稱。 您可以選擇您自己的唯一值，或保留預設建議。
* [訂用帳戶] 請務必與您要使用的訂用帳戶一致 
* [資源群組] 一定要是您希望使用的資源群組
* 將 [方案類型] 保留為 [使用]
* 在 [位置] 中，選擇符合您資源群組的位置
* 使用 [新增...] 連結，建立新的 **Azure 儲存體** 資源。 設定符合您資源群組的 [位置]，使用其他預設值，然後按 [確定]。

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-4.png" alt-text="在 Visual Studio 中發佈 Azure 函式：函數應用程式 (Windows) - 新建":::

然後，選取 [Create] \(建立\)。

這應該會帶您回到 [Functions 執行個體] 頁面，此時您的新函數應用程式會出現在您的資源群組底下。 按 [完成]。

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-5.png" alt-text="在 Visual Studio 中發佈 Azure 函式：Functions 執行個體 (在函數應用程式之後)":::

回到在主 Visual Studio 視窗中開啟的「發佈」 窗格中，確認所有資訊都正確無誤，然後選取 [發佈]。

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-6.png" alt-text="在 Visual Studio 中發佈 Azure 函式：發佈":::

> [!NOTE]
> 如果您看到如下的快顯：:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-7.png" alt-text="在 Visual Studio 中發佈 Azure 函式：發佈認證" border="false":::
> 請選取 [嘗試從 Azure 擷取認證] 和 [儲存]。
>
> 如果您看到關於 *升級 Azure 上的 Azure Functions 版本* 或 *您的函式執行階段版本不符合 Azure 中執行的版本* 的警告：
>
> 請遵循提示來升級至最新的 Azure Functions 執行階段版本。 如果您使用較舊版本的 Visual Studio，可能會發生此問題。

為了讓您的函式應用程式能夠存取 Azure 數位 Twins，它必須有系統管理的身分識別，而且有權存取您的 Azure 數位 Twins 實例。 接下來您要設定。

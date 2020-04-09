---
title: 從 Azure 入口網站建立函數應用程式
description: 從門戶在 Azure 中創建新的函數應用。
ms.topic: how-to
ms.date: 08/29/2019
ms.custom: mvc
ms.openlocfilehash: 001b4e4f0ea7fbacd232b2a87abfe353f34919bd
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985009"
---
# <a name="create-a-function-app-from-the-azure-portal"></a>從 Azure 入口網站建立函數應用程式

本主題介紹如何使用 Azure 函數在 Azure 門戶中創建函數應用。 函數應用程式是裝載個別函式執行的容器。 

## <a name="create-a-function-app"></a>建立函數應用程式

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

函數應用程式建立之後，您可以使用一或多個不同的語言建立個別的函式。 使用[入口網站](functions-create-first-azure-function.md#create-function)、[連續部署](functions-continuous-deployment.md)或者[透過 FTP 上傳](https://github.com/projectkudu/kudu/wiki/Accessing-files-via-ftp)來建立函式。

## <a name="service-plans"></a>服務方案

Azure 函數有三種不同的服務計劃:消耗計劃、高級計劃和專用(應用服務)計劃。 創建函數應用時,必須選擇服務計劃,隨後無法更改。 如需詳細資訊，請參閱[選擇 Azure Functions 主控方案](functions-scale.md)。

如果您計劃在專用(應用服務)計劃上運行 JavaScript 函數,則應選擇內核較少的計畫。 如需詳細資訊，請參閱 [JavaScript 函式參考資料](functions-reference-node.md#choose-single-vcpu-app-service-plans)。

<a name="storage-account-requirements"></a>

## <a name="storage-account-requirements"></a>儲存體帳戶的需求

建立函數應用時,必須創建或連結到支援 Blob、佇列和表存儲的通用 Azure 儲存帳戶。 Azure Functions 會在內部使用「Azure 儲存體」來進行作業，例如管理觸發程序和記錄函式執行。 有些儲存體帳戶並不支援佇列和表格，例如僅限 Blob 的儲存體帳戶、Azure 進階儲存體和搭配 ZRS 複寫的一般用途儲存體帳戶。 建立函數應用程式時，[儲存體帳戶] 刀鋒視窗中會過濾掉這些帳戶。

>[!NOTE]
>當使用「使用情況主控方案」時，您的函式程式碼和繫結組態檔會儲存在主要儲存體帳戶中的 Azure 檔案儲存體中。 當您刪除主要儲存體帳戶時，會刪除此內容且無法復原。

要瞭解有關儲存帳戶類型詳細資訊,請參閱[介紹 Azure 儲存服務](../storage/common/storage-introduction.md#core-storage-services)。 

## <a name="next-steps"></a>後續步驟

雖然 Azure 入口網站可以讓建立及試用 Functions 變得容易，但是我們建議[本機開發](functions-develop-local.md)。 在入口網站中建立函數應用程式之後，您仍然必須新增函式。 

> [!div class="nextstepaction"]
> [新增由 HTTP 觸發的函式](functions-create-first-azure-function.md#create-function)

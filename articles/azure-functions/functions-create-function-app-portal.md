---
title: 從 Azure 入口網站建立函數應用程式
description: 從入口網站在 Azure 中建立新的函數應用程式。
ms.topic: how-to
ms.date: 08/29/2019
ms.custom: mvc
ms.openlocfilehash: 001b4e4f0ea7fbacd232b2a87abfe353f34919bd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "80985009"
---
# <a name="create-a-function-app-from-the-azure-portal"></a>從 Azure 入口網站建立函數應用程式

本主題說明如何使用 Azure Functions 在 Azure 入口網站中建立函數應用程式。 函數應用程式是裝載個別函式執行的容器。 

## <a name="create-a-function-app"></a>建立函數應用程式

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

函數應用程式建立之後，您可以使用一或多個不同的語言建立個別的函式。 使用[入口網站](functions-create-first-azure-function.md#create-function)、[連續部署](functions-continuous-deployment.md)或者[透過 FTP 上傳](https://github.com/projectkudu/kudu/wiki/Accessing-files-via-ftp)來建立函式。

## <a name="service-plans"></a>服務方案

Azure Functions 有三個不同的服務方案：取用方案、高階方案和專用 (App Service) 方案。 建立函數應用程式時，您必須選擇您的服務方案，而且之後無法變更。 如需詳細資訊，請參閱[選擇 Azure Functions 主控方案](functions-scale.md)。

如果您打算在專用 (App Service) 方案上執行 JavaScript 函式，您應該選擇較少核心的方案。 如需詳細資訊，請參閱 [JavaScript 函式參考資料](functions-reference-node.md#choose-single-vcpu-app-service-plans)。

<a name="storage-account-requirements"></a>

## <a name="storage-account-requirements"></a>儲存體帳戶的需求

建立函數應用程式時，您必須建立或連結支援 Blob、「佇列」及「表格」儲存體的一般用途「Azure 儲存體」帳戶。 Azure Functions 會在內部使用「Azure 儲存體」來進行作業，例如管理觸發程序和記錄函式執行。 有些儲存體帳戶並不支援佇列和表格，例如僅限 Blob 的儲存體帳戶、Azure 進階儲存體和搭配 ZRS 複寫的一般用途儲存體帳戶。 建立函數應用程式時，[儲存體帳戶] 刀鋒視窗中會過濾掉這些帳戶。

>[!NOTE]
>當使用「使用情況主控方案」時，您的函式程式碼和繫結組態檔會儲存在主要儲存體帳戶中的 Azure 檔案儲存體中。 當您刪除主要儲存體帳戶時，會刪除此內容且無法復原。

若要深入了解儲存體帳戶類型，請參閱 [Azure 儲存體服務簡介](../storage/common/storage-introduction.md#core-storage-services)。 

## <a name="next-steps"></a>後續步驟

雖然 Azure 入口網站可以讓建立及試用 Functions 變得容易，但是我們建議[本機開發](functions-develop-local.md)。 在入口網站中建立函數應用程式之後，您仍然必須新增函式。 

> [!div class="nextstepaction"]
> [新增由 HTTP 觸發的函式](functions-create-first-azure-function.md#create-function)

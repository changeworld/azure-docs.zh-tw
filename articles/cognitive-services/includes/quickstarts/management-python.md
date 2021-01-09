---
title: 快速入門：適用於 Python 的 Azure 管理用戶端程式庫
description: 在本快速入門中，開始使用適用於 Python 的 Azure 管理用戶端程式庫。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: 616ad26e28db46f8c28aaa3fb763e2f0acf15409
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/23/2020
ms.locfileid: "97745691"
---
[參考文件](/python/api/azure-mgmt-cognitiveservices/azure.mgmt.cognitiveservices?view=azure-python) | [程式庫來源程式碼](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-mgmt-cognitiveservices) | [套件 (PyPi)](https://pypi.org/project/azure-mgmt-cognitiveservices/) | [範例](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-mgmt-cognitiveservices/tests)

## <a name="python-prerequisites"></a>Python 必要條件

* 有效的 Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)。
* [Python 3.x](https://www.python.org/)

[!INCLUDE [Create a service principal](./create-service-principal.md)]

[!INCLUDE [Create a resource group](./create-resource-group.md)]

## <a name="create-a-new-python-application"></a>建立新的 Python 應用程式

在您慣用的編輯器或 IDE 中建立新的 Python 應用程式，並在主控台視窗中瀏覽至您的專案。

### <a name="install-the-client-library"></a>安裝用戶端程式庫

您可以使用下列命令來安裝用戶端程式庫：

```console
pip install azure-mgmt-cognitiveservices
```

如果您使用 Visual Studio IDE，則可以取得可下載 NuGet 套件形式的用戶端程式庫。

### <a name="import-libraries"></a>匯入程式庫

開啟 Python 指令碼，並匯入下列程式庫。

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_imports)]

## <a name="authenticate-the-client"></a>驗證用戶端

將下列欄位新增至指令碼的根目錄，並使用您所建立的服務主體和您的 Azure 帳戶資訊填入其值。

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_constants)]

然後新增下列程式碼，以建構 **CognitiveServicesManagementClient** 物件。 您所有的 Azure 管理作業都需要此物件。

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_auth)]

## <a name="create-a-cognitive-services-resource"></a>建立認知服務資源

若要建立並訂閱新的認知服務資源，請使用 **Create** 函式。 這個函式會將新的可計費資源新增至您傳入的資源群組。 建立新的資源時，必須知道您要使用的服務「種類」，以及其定價層 (或 SKU) 和 Azure 位置。 下列函式會將這些項目全部當作引數，並建立資源。

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_create)]

### <a name="choose-a-service-and-pricing-tier"></a>選擇服務和定價層

建立新的資源時，必須知道您要使用的服務「種類」，以及想要的[定價層](https://azure.microsoft.com/pricing/details/cognitive-services/) (或 SKU)。 建立資源時，您將使用此資訊和其他資訊作為參數。 下列函式會列出可用的認知服務「種類」。

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

[!INCLUDE [SKUs and pricing](./sku-pricing.md)]

## <a name="view-your-resources"></a>檢視資源

若要檢視您 Azure 帳戶下的所有資源 (跨所有資源群組)，請使用下列函式：

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_list)]

## <a name="delete-a-resource"></a>刪除資源

下列函式會從給定的資源群組中刪除指定的資源。

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_delete)]

## <a name="call-management-functions"></a>呼叫體管理函式

將下列程式碼新增到指令碼底部，以呼叫上述函式。 此程式碼會列出可用的資源、建立範例資源、列出您擁有的資源，然後刪除範例資源。

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_calls)]

## <a name="run-the-application"></a>執行應用程式

使用 `python` 命令，從命令列執行您的應用程式。

```console
python <your-script-name>.py
```

## <a name="see-also"></a>另請參閱

* [Azure 管理 SDK 參考文件](/python/api/azure-mgmt-cognitiveservices/azure.mgmt.cognitiveservices?view=azure-python)
* [Azure 認知服務是什麼？](../../what-are-cognitive-services.md)
* [驗證 Azure 認知服務要求](../../authentication.md)
* [使用 Azure 入口網站建立新的資源](../../cognitive-services-apis-create-account.md)
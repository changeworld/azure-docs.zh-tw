---
title: 更改 Azure IoT 中心應用程式設定 |微軟文檔
description: 作為管理員，如何通過更改應用程式名稱、URL、上載映射和刪除應用程式來管理 Azure IoT 中心應用程式
author: viv-liu
ms.author: viviali
ms.date: 11/27/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 7b77ea9e0e1f322cb5ef0bc63885c3ccce1b76f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158671"
---
# <a name="change-iot-central-application-settings"></a>更改 IoT 中心應用程式設定



本文介紹作為管理員，如何通過更改應用程式名稱和 URL、上載映射和刪除 Azure IoT 集中應用程式中的應用程式來管理應用程式。

若要存取並使用 [管理]**** 區段，您必須屬於 Azure IoT Central 應用程式的**管理員**角色。 如果您建立 Azure IoT Central 應用程式，就會自動將您指派給該應用程式的**管理員**角色。

## <a name="change-application-name-and-url"></a>變更應用程式名稱和 URL

在 [應用程式設定]**** 頁面上，您可以變更應用程式的名稱和 URL，然後選擇 [儲存]****。

![[應用程式設定] 頁面](media/howto-administer/image0-a.png)

如果管理員為應用程式創建自訂主題，則此頁包括一個選項，用於在 UI 中隱藏**應用程式名稱**。 如果自訂主題中的應用程式徽標包含應用程式名稱，則此選項很有用。 有關詳細資訊，請參閱自訂[Azure IoT 中央 UI](./howto-customize-ui.md)。

> [!Note]
> 如果您變更 URL，舊的 URL 可能會被其他 Azure IoT Central 客戶所取用。 如果發生這種情況，您將無法再使用該 URL。 當您變更 URL 時，舊的 URL 就不再運作，而您必須通知使用者應使用新的 URL。

## <a name="delete-an-application"></a>刪除應用程式

使用 [刪除]**** 按鈕可永久刪除您的 IoT Central 應用程式。 此操作將永久刪除與應用程式關聯的所有資料。

> [!Note]
> 若要刪除應用程式，您必須在建立應用程式時所選擇的 Azure 訂用帳戶中具有刪除資源的權限。 若要深入了解，請參閱[使用角色型存取控制來管理 Azure 訂用帳戶資源的存取](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)。

## <a name="manage-programmatically"></a>以程式設計方式管理

IoT Central 的 Azure Resource Manager SDK 套件適用於 Node、Python、C#、Ruby、Java 與 Go。 您可以使用這些包創建、列出、更新或刪除 IoT 中心應用程式。 這些套裝程式包括管理身份驗證和錯誤處理的協助程式。

您可以在 中找到如何使用 Azure 資源管理器 SDK 的示例[https://github.com/emgarten/iotcentral-arm-sdk-examples](https://github.com/emgarten/iotcentral-arm-sdk-examples)。

要瞭解更多資訊，請參閱以下 GitHub 存儲庫和包：

| 語言 | Repository | Package |
| ---------| ---------- | ------- |
| 節點 | [https://github.com/Azure/azure-sdk-for-node](https://github.com/Azure/azure-sdk-for-node) | [https://www.npmjs.com/package/azure-arm-iotcentral](https://www.npmjs.com/package/azure-arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Go | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>後續步驟

現在，您已經瞭解了如何管理 Azure IoT 中心應用程式，建議的下一步是瞭解 Azure IoT Central 中的[管理使用者和角色](howto-manage-users-roles.md)。

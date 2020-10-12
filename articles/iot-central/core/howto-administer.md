---
title: 變更 Azure IoT Central 的應用程式設定 |Microsoft Docs
description: 以系統管理員身分，藉由變更應用程式名稱、URL、上傳影像和刪除應用程式，來管理您的 Azure IoT Central 應用程式
author: viv-liu
ms.author: viviali
ms.date: 11/27/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 37a3725fb7684a9e87f1936ef0ce8cc79de03894
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89594657"
---
# <a name="change-iot-central-application-settings"></a>變更 IoT Central 的應用程式設定



本文描述如何以系統管理員的身分，藉由變更應用程式名稱和 URL、上傳影像，以及刪除 Azure IoT Central 應用程式中的應用程式，來管理應用程式。

若要存取並使用 [管理]**** 區段，您必須屬於 Azure IoT Central 應用程式的**管理員**角色。 如果您建立 Azure IoT Central 應用程式，就會自動將您指派給該應用程式的**管理員**角色。

## <a name="change-application-name-and-url"></a>變更應用程式名稱和 URL

在 [應用程式設定]**** 頁面上，您可以變更應用程式的名稱和 URL，然後選擇 [儲存]****。

![[應用程式設定] 頁面](media/howto-administer/image0-a.png)

如果您的系統管理員為您的應用程式建立自訂主題，此頁面會包含在 UI 中隱藏 **應用程式名稱** 的選項。 如果自訂主題中的應用程式標誌包含應用程式名稱，這個選項就很有用。 如需詳細資訊，請參閱 [自訂 AZURE IOT CENTRAL UI](./howto-customize-ui.md)。

> [!Note]
> 如果您變更 URL，舊的 URL 可能會被其他 Azure IoT Central 客戶所取用。 如果發生這種情況，您將無法再使用該 URL。 當您變更 URL 時，舊的 URL 就不再運作，而您必須通知使用者應使用新的 URL。

## <a name="delete-an-application"></a>刪除應用程式

使用 [刪除]**** 按鈕可永久刪除您的 IoT Central 應用程式。 此動作會永久刪除所有與應用程式相關聯的資料。

> [!Note]
> 若要刪除應用程式，您必須在建立應用程式時所選擇的 Azure 訂用帳戶中具有刪除資源的權限。 若要深入了解，請參閱[使用角色型存取控制來管理 Azure 訂用帳戶資源的存取](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)。

## <a name="manage-programmatically"></a>以程式設計方式管理

IoT Central 的 Azure Resource Manager SDK 套件適用於 Node、Python、C#、Ruby、Java 與 Go。 您可以使用這些套件來建立、列出、更新或刪除 IoT Central 的應用程式。 這些套件包含管理驗證和錯誤處理的協助程式。

您可以在中找到如何使用 Azure Resource Manager Sdk 的範例 [https://github.com/Azure-Samples/azure-iot-central-arm-sdk-samples](https://github.com/Azure-Samples/azure-iot-central-arm-sdk-samples) 。

若要深入瞭解，請參閱下列 GitHub 存放庫和套件：

| Language | Repository | Package |
| ---------| ---------- | ------- |
| 節點 | [https://github.com/Azure/azure-sdk-for-node](https://github.com/Azure/azure-sdk-for-node) | [https://www.npmjs.com/package/azure-arm-iotcentral](https://www.npmjs.com/package/azure-arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Go | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>後續步驟

現在您已瞭解如何管理您的 Azure IoT Central 應用程式，建議的下一個步驟是瞭解如何在 Azure IoT Central 中 [管理使用者和角色](howto-manage-users-roles.md) 。

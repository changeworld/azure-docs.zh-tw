---
title: 管理您的 Azure IoT Central 應用程式 |Microsoft Docs
description: 身為系統管理員，如何藉由變更應用程式名稱、URL、上傳影像、複製和刪除應用程式來管理您的 Azure IoT Central 應用程式
author: viv-liu
ms.author: viviali
ms.date: 08/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 89c4dd294cbbf0953545e1055e32adfc5f7cce28
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990788"
---
# <a name="manage-your-iot-central-application"></a>管理您的 IoT Central 應用程式

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

本文說明如何以系統管理員的身分，藉由變更應用程式名稱和 URL 來管理應用程式，以及上傳影像，您也可以瞭解如何在 Azure IoT Central 應用程式中複製和刪除應用程式。

若要存取並使用 [管理] 區段，您必須屬於 Azure IoT Central 應用程式的**管理員**角色。 如果您建立 Azure IoT Central 應用程式，就會自動將您指派給該應用程式的**管理員**角色。 

## <a name="change-application-name-and-url"></a>變更應用程式名稱和 URL

在 [應用程式設定] 頁面上，您可以變更應用程式的名稱和 URL，然後選擇 [儲存]。

![[應用程式設定] 頁面](media/howto-administer/image0-a.png)

如果您的系統管理員為您的應用程式建立自訂主題，此頁面會包含在 UI 中隱藏**應用程式名稱**的選項。 如果自訂主題中的應用程式標誌包含應用程式名稱，這會很有用。 如需詳細資訊，請參閱[自訂 Azure IOT CENTRAL UI](./howto-customize-ui.md)。

> [!Note]
> 如果您變更 URL，舊的 URL 可能會被其他 Azure IoT Central 客戶所取用。 如果發生這種情況，您將無法再使用該 URL。 當您變更 URL 時，舊的 URL 就不再運作，而您必須通知使用者應使用新的 URL。

## <a name="prepare-and-upload-image"></a>準備及上傳映像

若要變更應用程式影像，請參閱[準備及上傳影像至您的 Azure IoT Central 應用程式](howto-prepare-images.md)。

## <a name="copy-an-application"></a>複製應用程式

您可以建立任何應用程式複本，並減去其中的任何裝置執行個體、裝置資料歷程記錄和使用者資料。 此複本會使用您將支付費用的標準定價方案。 您無法以這種方式建立使用免費定價方案的應用程式。

選取 [**複製**]。 在 [] 對話方塊中，輸入新應用程式的詳細資料。 然後選取 [**複製**] 以確認您想要繼續。 在[建立應用程式](quick-deploy-iot-central.md)快速入門中，深入了解此表單中的欄位。

![[應用程式設定] 頁面](media/howto-administer/appcopy2.png)

應用程式複製作業成功之後，您可以使用連結流覽至新的應用程式。

![[應用程式設定] 頁面](media/howto-administer/appcopy3a.png)

複製應用程式也會複製規則的定義和電子郵件動作。 某些動作（例如 Flow、Logic Apps 等）會透過規則識別碼系結至特定規則。 當規則複製到不同的應用程式時，它會取得自己的規則識別碼。 在此情況下，使用者將必須建立新的動作，然後將新的規則與它建立關聯。 一般來說，檢查規則和動作以確保它們在新應用程式中是最新狀態是個不錯的主意。

> [!WARNING]
> 如果儀表板包含顯示特定裝置相關資訊的磚，這些磚會顯示在新的應用程式中**找不到所要求的資源**。 您必須重新設定這些磚，才能在新的應用程式中顯示裝置的相關資訊。

## <a name="delete-an-application"></a>刪除應用程式

使用 [刪除] 按鈕可永久刪除您的 IoT Central 應用程式。 此動作會永久刪除與應用程式相關聯的所有資料。

> [!Note]
> 若要刪除應用程式，您必須在建立應用程式時所選擇的 Azure 訂用帳戶中具有刪除資源的權限。 若要深入了解，請參閱[使用角色型存取控制來管理 Azure 訂用帳戶資源的存取](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)。


## <a name="manage-programatically"></a>以程式設計方式管理

IoT Central 的 Azure Resource Manager SDK 套件適用於 Node、Python、C#、Ruby、Java 與 Go。 您可以使用這些封裝來建立、列出、更新或刪除 IoT Central 應用程式。 這些套件包含管理驗證和錯誤處理的協助程式。

您可以在 [https://github.com/emgarten/iotcentral-arm-sdk-examples](https://github.com/emgarten/iotcentral-arm-sdk-examples) 上找到如何使用 Azure Resource Manager SDK 的範例。

若要深入瞭解，請參閱下列 GitHub 存放庫和套件：

| 語言 | Repository | Package |
| ---------| ---------- | ------- |
| 節點 | [https://github.com/Azure/azure-sdk-for-node](https://github.com/Azure/azure-sdk-for-node) | [https://www.npmjs.com/package/azure-arm-iotcentral](https://www.npmjs.com/package/azure-arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| 拼音 | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Go | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>後續步驟
 
既然您已瞭解如何管理 Azure IoT Central 應用程式，建議的下一個步驟是瞭解如何管理 Azure IoT Central 中的[使用者和角色](howto-manage-users-roles.md)。
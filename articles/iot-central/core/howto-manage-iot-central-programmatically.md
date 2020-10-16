---
title: 以程式設計的方式管理 IoT Central |Microsoft Docs
description: 本文說明如何以程式設計的方式建立和管理您的 IoT Central。 您可以使用多種語言 SDK (例如，JavaScript、Python、C#、Ruby 和 Go) 來檢視、修改和移除應用程式。
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 05/19/2020
ms.topic: how-to
ms.openlocfilehash: 773793c7681890098fea1a37cc5b9912c0ecb75c
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92122989"
---
# <a name="manage-iot-central-programmatically"></a>以程式設計方式管理 IoT Central

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

您可以使用 Azure SDK，以程式設計的方式來管理您的應用程式，而非從 [Azure IoT Central 應用程式管理員](https://aka.ms/iotcentral)網站上建立和管理 IoT Central 應用程式。 支援的語言包括 JavaScript、Python、C#、Ruby 和 Go。

## <a name="install-the-sdk"></a>安裝 SDK

下表列出 SDK 存放庫和封裝安裝命令：

| SDK 存放庫 | 安裝套件 |
| -------------- | ------------ |
| [適用於 JavaScript 的 Azure IotCentralClient SDK](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/iotcentral/arm-iotcentral) | `npm install @azure/arm-iotcentral` |
| [Microsoft Azure SDK for Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/iothub/azure-mgmt-iotcentral/azure/mgmt/iotcentral) | `pip install azure-mgmt-iotcentral` |
| [Azure SDK for .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/iotcentral/Microsoft.Azure.Management.IotCentral) | `dotnet add package Microsoft.Azure.Management.IotCentral` |
| [適用於 Ruby 的 Microsoft Azure SDK - 資源管理 (預覽)](https://github.com/Azure/azure-sdk-for-ruby/tree/master/management/azure_mgmt_iot_central/lib/2018-09-01/generated/azure_mgmt_iot_central) | `gem install azure_mgmt_iot_central` |
| [Azure SDK for Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/iotcentral) | [Maven 封裝](https://search.maven.org/search?q=a:azure-mgmt-iotcentral) |
| [Azure SDK for Go](https://github.com/Azure/azure-sdk-for-go/tree/master/services/iotcentral/mgmt/2018-09-01/iotcentral) | [封裝版本](https://github.com/Azure/azure-sdk-for-go/releases) |

## <a name="samples"></a>範例

[Azure IoT Central ARM SDK 範例](/samples/azure-samples/azure-iot-central-arm-sdk-samples/azure-iot-central-arm-sdk-samples/)存放庫中有多種程式設計語言的程式碼範例，可為您示範如何建立、更新、列出及刪除 Azure IoT Central 應用程式。

## <a name="next-steps"></a>後續步驟

現在您已了解如何以程式設計的方式管理 Azure IoT Central 應用程式，建議您接下來繼續深入了解 [Azure Resource Manager](../../azure-resource-manager/management/overview.md) 服務。
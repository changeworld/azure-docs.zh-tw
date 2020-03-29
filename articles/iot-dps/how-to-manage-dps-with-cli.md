---
title: 使用 Azure CLI & IoT 擴展管理 IoT 中心設備佈建服務
description: 瞭解如何使用 Azure CLI 和 IoT 擴展來管理 IoT 中心設備佈建服務 （DPS）
author: chrissie926
ms.author: menchi
ms.date: 01/17/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 03ec0b41ad910ff0d1dcdc17148e01ec94ea9fb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78674522"
---
# <a name="how-to-use-azure-cli-and-the-iot-extension-to-manage-the-iot-hub-device-provisioning-service"></a>如何使用 Azure CLI 和 IoT 擴充功能來管理 IoT 中樞裝置佈建服務

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) 是一個開放原始碼跨平台命令列工具，用來管理 Azure 資源 (例如 IoT Edge)。 Azure CLI 適用於 Windows、Linux 和 MacOS。 Azure CLI 可讓您管理 Azure IoT 中樞資源、裝置佈建服務執行個體，以及現成的連結中樞。

IoT 擴充功能以裝置管理和完整 IoT Edge 功能來擴充 Azure CLI 的功能。

在本教學課程中，您會先完成設定 Azure CLI 和 IoT 擴充功能的步驟。 然後您會了解如何執行 CLI 命令，以執行基本裝置佈建服務作業。 

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="installation"></a>安裝 

### <a name="install-python"></a>安裝 Python

需要 [Python 2.7x 或 Python 3.x](https://www.python.org/downloads/)。

### <a name="install-the-azure-cli"></a>安裝 Azure CLI

請遵循[安裝指示](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)在您的環境中設定 Azure CLI。 至少，Azure CLI 版本必須為 2.0.70 或以上。 使用 `az –version` 進行驗證。 這個版本支援 az 擴充命令並引進 Knack 命令架構。 在 Windows 上進行安裝的最簡單方式，就是下載並安裝 [MSI](https://aka.ms/InstallAzureCliWindows)。

### <a name="install-iot-extension"></a>安裝 IoT 擴展

[IoT 擴充功能讀我檔案](https://github.com/Azure/azure-iot-cli-extension)說明安裝此擴充功能的數種方式。 最簡單的方式就是執行 `az extension add --name azure-iot`。 安裝之後，您可以使用 `az extension list` 來驗證目前安裝的擴充功能，或使用 `az extension show --name azure-iot` 來查看有關 IoT 擴充功能的詳細資料。 若要移除此擴充功能，您可以使用 `az extension remove --name azure-iot`。


## <a name="basic-device-provisioning-service-operations"></a>基本裝置佈建服務作業

此範例說明如何登入您的 Azure 帳戶、建立 Azure 資源群組 (可保存 Azure 解決方案相關資源的容器)、建立 IoT 中樞、建立裝置佈建服務、列出現有的裝置佈建服務，以及使用 CLI 命令建立連結的 IoT 中樞。 

開始之前，請先完成先前所述的安裝步驟。 如果您沒有 Azure 帳戶，可以立即[建立一個免費帳戶](https://azure.microsoft.com/free/?v=17.39a)。 


### <a name="1-log-in-to-the-azure-account"></a>1. 登錄到 Azure 帳戶
  
    az login

![login](./media/how-to-manage-dps-with-cli/login.jpg)

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2. 在東部創建資源組 IoTHubBlogDemo

    az group create -l eastus -n IoTHubBlogDemo

![建立資源群組](./media/how-to-manage-dps-with-cli/create-resource-group.jpg)


### <a name="3-create-two-device-provisioning-services"></a>3. 創建兩個設備佈建服務

    az iot dps create --resource-group IoTHubBlogDemo --name demodps

![建立裝置佈建服務](./media/how-to-manage-dps-with-cli/create-dps.jpg)

    az iot dps create --resource-group IoTHubBlogDemo --name demodps2

### <a name="4-list-all-the-existing-device-provisioning-services-under-this-resource-group"></a>4. 列出此資源組下的所有現有設備佈建服務

    az iot dps list --resource-group IoTHubBlogDemo

![列出裝置佈建服務](./media/how-to-manage-dps-with-cli/list-dps.jpg)


### <a name="5-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>5. 在新創建的資源組下創建 IoT 中心博客DemoHub

    az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo

![建立 IoT 中樞](./media/how-to-manage-dps-with-cli/create-hub.jpg)

### <a name="6-link-one-existing-iot-hub-to-a-device-provisioning-service"></a>6. 將一個現有 IoT 中心連結到設備預配服務

    az iot dps linked-hub create --resource-group IoTHubBlogDemo --dps-name demodps --connection-string <connection string> -l westus

![連結中樞](./media/how-to-manage-dps-with-cli/create-hub.jpg)

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 註冊裝置
> * 啟動裝置
> * 確認裝置已註冊

前進到下一個教學課程，以了解如何跨負載平衡中樞來佈建多個裝置。 

> [!div class="nextstepaction"]
> [跨負載平衡 IoT 中樞來佈建裝置](./tutorial-provision-multiple-hubs.md)

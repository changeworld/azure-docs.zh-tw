---
title: 部署自訂的裝置模擬解決方案映像 - Azure | Microsoft Docs
description: 在本操作指南中，您會了解如何將裝置模擬解決方案的自訂 Docker 映像部署至 Azure。
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: c1f321f452b65016c11cb66d08ebab108509cc62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "61448389"
---
# <a name="deploy-a-custom-device-simulation-docker-image"></a>建立自訂的裝置模擬 Docker 映像

您可以修改裝置模擬解決方案，來新增自訂功能。 例如，[使用 Protocol Buffers 將遙測資料序列化](iot-accelerators-device-simulation-protobuf.md)一文會示範如何將自訂裝置新增至使用 Protocol Buffers (Protobuf) 的解決方案以傳送遙測資料。 在本機測試您的變更之後，下一個步驟則是將變更部署到您在 Azure 中的裝置模擬執行個體。 若要完成這項工作，您需要建立並部署包含您所修改服務的 Docker 映像。

本操作指南中的步驟說明如何：

1. 準備開發環境
1. 產生新的 Docker 映像
1. 設定裝置模擬以使用新的 Docker 映像
1. 使用新的映像執行模擬

## <a name="prerequisites"></a>Prerequisites

若要完成本操作指南中的步驟，您需要：

* 已部署[裝置模擬](quickstart-device-simulation-deploy.md)執行個體。
* Docker。 下載適用於您平台的 [Docker Community Edition](https://www.docker.com/products/docker-engine#/download)。
* [Docker Hub 帳戶](https://hub.docker.com/)，您可以將 Docker 映像上傳至此。 在您的 Docker Hub 帳戶中，建立稱為 **device-simulation** 的公用存放庫。
* 本機電腦上要有經過修改和測試的[裝置模擬解決方案](https://github.com/Azure/device-simulation-dotnet/archive/master.zip)。 例如，您可以修改解決方案以[使用 Protocol Buffers 將遙測資料序列化](iot-accelerators-device-simulation-protobuf.md)。
* 可以執行 SSH 的殼層。 如果您安裝 Git For Windows，會一併安裝 **bash** 殼層供您使用。 您也可以使用 [Azure Cloud Shell](https://shell.azure.com/)。

本文中的指示假設您使用的是 Windows。 如果您使用其他作業系統，則可能需要調整某些檔案路徑和命令以符合環境。

## <a name="create-a-new-docker-image"></a>建立新的 Docker 映像

若要將您自己的變更部署到裝置模擬服務，您需要編輯 **scripts\docker** 資料夾中的組建和部署指令碼，將容器上傳到您的 docker-hub 帳戶

### <a name="modify-the-docker-scripts"></a>修改 Docker 指令碼

使用您的 Docker Hub 存放庫資訊，修改 **scripts\docker** 資料夾中的 Docker **build.cmd**、**publish.cmd** 及 **run.cmd** 指令碼。 這些步驟假設您已建立稱為 **device-simulation** 的公用存放庫：

`DOCKER_IMAGE={your-docker-hub-username}/device-simulation`

更新 **docker-compose.yml** 檔案，如下所示：

`image: {your-docker-hub-username}/device-simulation`

### <a name="configure-the-solution-to-include-any-new-files"></a>設定解決方案以包含任何的新檔案

如果您已新增任何的新裝置型號檔案，您需要明確將它們包含在解決方案中。 針對要包含的每個額外檔案在 **services/services.csproj** 中新增一個項目。 例如，如果您已完成[使用 Protocol Buffers 將遙測資料序列化](iot-accelerators-device-simulation-protobuf.md)操作，請新增下列項目：

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

### <a name="generate-new-docker-images-and-push-to-docker-hub"></a>產生新的 Docker 映像並推送至 Docker Hub

使用您的 docker hub 帳戶，將新的 Docker 映像發佈到 Docker Hub：

1. 開啟命令提示字元，然後瀏覽到裝置模擬存放庫的本機複本。

1. 瀏覽到 **docker** 資料夾：

    ```cmd
    cd scripts\docker
    ```

1. 執行下列命令以建置 Docker 映像：

    ```cmd
    build.cmd
    ```

1. 執行下列命令，以將 Docker 映像發佈到 Docker Hub 存放庫。 使用 Docker Hub 認證登入 Docker：

    ```cmd
    docker login
    publish.cmd
    ```

<!-- TODO fix heading levels working include -->

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="update-the-service"></a>更新服務

若要更新裝置模擬容器，以使用自訂映像，請完成下列步驟：

* 使用 SSH 連線到裝載裝置模擬執行個體的虛擬機器。 使用您在上一節中記下的 IP 位址與密碼：

    ```sh
    ssh azureuser@{your vm ip address}
    ```

* 瀏覽到 **/app** 目錄：

    ```sh
    cd /app
    ```

* 編輯 **docker-compose.yml** 檔案：

    ```sh
    sudo nano docker-compose.yml
    ```

    修改 **image**，以將您上傳的自訂 **device-simulation** 映像指向 Docker Hub 存放庫：

    ```yml
    image: {your-docker-hub-username}/device-simulation
    ```

    儲存您的變更。

* 執行下列命令，重新啟動微服務：

    ```sh
    sudo start.sh
    ```

## <a name="run-your-simulation"></a>執行模擬

您現在可以使用自訂的裝置模擬解決方案來執行模擬：

1. 從 [Microsoft Azure IoT 解決方案加速器](https://www.azureiotsolutions.com/Accelerators#dashboard)啟動您的裝置模擬 Web UI。

1. 使用 Web UI 設定和執行模擬。 如果您先前已完成[使用 Protocol Buffers 將遙測資料序列化](iot-accelerators-device-simulation-protobuf.md)，就可以使用自訂的裝置型號。

## <a name="next-steps"></a>後續步驟

現在您已了解如何部署自訂的裝置模擬映像，您可能想要了解如何[使用現有的 IoT 中樞搭配裝置模擬解決方案加速器](iot-accelerators-device-simulation-choose-hub.md)。
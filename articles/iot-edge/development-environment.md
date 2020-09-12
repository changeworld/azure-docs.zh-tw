---
title: Azure IoT Edge 開發環境 | Microsoft Docs
description: 了解支援的系統以及第一方開發工具，協助您建立 IoT Edge 模組
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: bee1f95e1f56b65912895fec8af2512c6cd7b85a
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89300369"
---
# <a name="prepare-your-development-and-test-environment-for-iot-edge"></a>準備 IoT Edge 適用的開發和測試環境

Azure IoT Edge 會將您現有的商務邏輯移到在邊緣運作的裝置。 若要準備您的應用程式和工作負載，以 [IoT Edge 模組](iot-edge-modules.md)的身分執行，您需要將它們當作容器來建置。 本文提供如何設定開發環境，讓您可以成功建立 IoT Edge 解決方案的指引。 一旦您設定開發環境之後，就可以學習如何[開發您自己的 IoT Edge 模組](module-development.md)。

任何 IoT Edge 解決方案中都需要考慮至少兩部機器。 其中一部是 IoT Edge 裝置本身，它會執行 IoT Edge 模組。 另一部則是您用來建置、測試及部署模組的開發電腦。 本文主要著重在開發電腦。 基於測試目的，兩部機器也可以是同一部。 您可以在開發電腦上執行 IoT Edge，並將模組部署到該電腦。

## <a name="operating-system"></a>作業系統

Azure IoT Edge 會在一組特定的[支援的作業系統](support.md)上執行。 若要針對 IoT Edge 開發，您可以使用大部分能夠執行容器引擎的作業系統。 容器引擎是在開發電腦上，將模組建置為容器，並將其推送到容器登錄的必要條件。

如果您的開發電腦無法執行 Azure IoT Edge，請繼續閱讀本文，以了解可協助您在本機測試及偵錯的[測試工具](#testing-tools)。

開發電腦的作業系統不一定要符合 IoT Edge 裝置的作業系統。 不過，容器的作業系統在開發電腦和 IoT Edge 裝置之間必須一致。 例如，您可以在 Windows 電腦上開發模組，並將其部署至 Linux 裝置。 Windows 電腦必須執行 Linux 容器，才能建置適用於 Linux 裝置的模組。

## <a name="container-engine"></a>容器引擎

IoT Edge 的中心概念是，您可以將商務和雲端邏輯封裝至容器，以便將其從遠端部署到裝置。 若要建置容器，在開發電腦上需要有一個容器引擎。

在生產環境中，唯一支援 IoT Edge 裝置的容器引擎是 Moby。 不過，與 Open Container Initiative 相容的任何容器引擎 (如 Docker)，都能建置 IoT Edge 模組映像。

## <a name="development-tools"></a>開發工具

Visual Studio 和 Visual Studio Code 的附加元件擴充功能都可以協助開發 IoT Edge 解決方案。 這些擴充功能提供語言專屬的範本，以協助建立及部署新的 IoT Edge 案例。 Visual Studio 和 Visual Studio Code 的 Azure IoT Edge 擴充功能可協助您為 IoT Edge 解決方案撰寫程式碼、建置、部署和偵錯。 您可以建立包含多個模組的完整 IoT Edge 解決方案，而且擴充功能會自動使用每個新增的模組，更新部署資訊清單範本。 您也可以使用擴充功能，從 Visual Studio 或 Visual Studio Code 中管理您的 IoT 裝置。 將模組部署到裝置、監視狀態，並檢視模組抵達 IoT 中樞的訊息。 這兩個擴充功能也都會使用 [IoT EdgeHub 開發人員工具](#iot-edgehub-dev-tool)，在開發電腦上對模組啟用本機執行和偵錯。

如果您想要使用其他編輯器或從 CLI 開發，Azure IoT Edge 開發人員工具會提供命令，讓您可以從命令列開發及測試。 您可以建立新的 IoT Edge 案例、建置模組映像、使用模擬器執行模組，以及監視傳送到 IoT 中樞的訊息。

### <a name="visual-studio-code-extension"></a>Visual Studio Code 擴充功能

Visual Studio Code 的 Azure IoT Edge 擴充功能提供針對程式設計語言 (包括 C、C#、Java、Node.js 和 Python) 而建置的 IoT Edge 模組範本，以及使用 C# 的 Azure 函式。

如需詳細資訊及下載，請參閱[適用於 Visual Studio Code 的 Azure IoT 工具組](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) (英文)。

除了 IoT Edge 擴充功能之外，您可能也會發現它在安裝用於開發的其他擴充功能時很有幫助。 例如，您可以使用[適用於 Visual Studio Code 的 Docker 支援](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)來管理映像、容器和登錄。 此外，所有主要支援的語言都有適用於 Visual Studio Code 的擴充功能，可在您開發模組時提供協助。

#### <a name="prerequisites"></a>必要條件

某些語言和服務的模組範本具備使用 Visual Studio Code 在開發電腦上建置專案資料夾所需的必要條件。

| 模組範本 | 必要條件 |
| --------------- | ------------ |
| Azure Functions | [.NET Core 2.1 SDK](https://www.microsoft.com/net/download) |
| C | [Git](https://git-scm.com/) |
| C# | [.NET Core 2.1 SDK](https://www.microsoft.com/net/download) |
| Java | <ul><li>[Java SE 開發套件 10](https://aka.ms/azure-jdks) <li> [設定 JAVA_HOME 環境變數](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) <li> [Maven](https://maven.apache.org/)</ul> |
| Node.js | <ul><li>[Node.js](https://nodejs.org/) <li> [Yeoman](https://www.npmjs.com/package/yo) <li> [Azure IoT Edge Node.js 模組產生器](https://www.npmjs.com/package/generator-azure-iot-edge-module)</ul> |
| Python |<ul><li> [Python](https://www.python.org/downloads/) <li> [Pip](https://pip.pypa.io/en/stable/installing/#installation) <li> [Git](https://git-scm.com/) </ul> |

### <a name="visual-studio-20172019-extension"></a>Visual Studio 2017/2019 擴充功能

適用于 Visual Studio 的 Azure IoT Edge 工具提供以 c # 和 C 為基礎的 IoT Edge 模組範本。

如需詳細資訊及下載，請參閱[Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools)的[Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)或 Azure IoT Edge Tools 的 Azure IoT Edge Tools。

### <a name="iot-edge-dev-tool"></a>IoT Edge 開發人員工具

Azure IoT Edge 開發人員工具會使用命令列功能簡化 IoT Edge 開發。 此工具會提供 CLI 命令來開發、偵錯及測試模組。 無論您要在電腦上手動安裝相依性，還是使用 IoT Edge 裝置容器，IoT Edge 開發人員工具都可以搭配您的開發系統使用。

如需詳細資訊並開始使用，請參閱 [IoT Edge 開發人員工具 Wiki](https://github.com/Azure/iotedgedev/wiki)。

## <a name="testing-tools"></a>測試工具

數個測試工具的存在是為了協助您模擬 IoT Edge 裝置，或更有效率地為模組進行偵錯。 下表顯示工具之間的高階比較，而各小節則更具體地描述每個工具。

生產環境部署僅支援 IoT Edge 執行階段，但下列工具可讓您針對開發和測試用途，模擬或輕鬆地建立 IoT Edge 裝置。 這些工具不會互斥，但是可以一起運作以獲得完整的開發體驗。

| 工具 | 也稱為 | 支援的平台 | 適用對象 |
| ---- | ------------- | ------------------- | --------- |
| IoT EdgeHub 開發人員工具  | iotedgehubdev | Windows、Linux、macOS | 模擬裝置為模組偵錯。 |
| IoT Edge 裝置容器 | microsoft/iotedgedev | Windows、Linux、macOS | 開發而不需要安裝相依性。 |
| 容器中的 IoT Edge 執行階段 | iotedgec | Windows、Linux、macOS、ARM | 在可能不支援執行階段的裝置上測試。 |
| IoT Edge 裝置容器 | toolboc/azure-iot-edge-device-container | Windows、Linux、macOS、ARM | 大規模地使用許多 IoT Edge 裝置測試一個案例。 |

### <a name="iot-edgehub-dev-tool"></a>IoT EdgeHub 開發人員工具

Azure IoT EdgeHub 開發人員工具提供本機開發和偵錯體驗。 此工具有助於在沒有 IoT Edge 執行階段的情況下啟動 IoT Edge 模組，讓您可以在本機建立、開發、測試、執行 IoT Edge 模組與解決方案，並進行偵錯。 您不需要將映像推送至容器登錄，就可以將其部署至裝置上進行測試。

IoT EdgeHub 開發人員工具的設計是為了搭配 Visual Studio 和 Visual Studio Code 擴充功能，以及 IoT Edge 開發人員工具使用。 它支援內部迴圈開發以及外部迴圈測試，因此也可以與 DevOps 工具整合。

如需詳細資訊以及安裝方式，請參閱 [Azure IoT EdgeHub 開發人員工具](https://pypi.org/project/iotedgehubdev/)。

### <a name="iot-edge-dev-container"></a>IoT Edge 裝置容器

Azure IoT Edge 裝置容器是一種 Docker 容器，具備開發 IoT Edge 所需的所有相依性。 此容器可讓您輕鬆地開始使用想要用於開發的多種語言，包括C#、Python、Node.js 和 Java。 您只需要安裝容器引擎 (例如 Docker 或 Moby)，就可以將容器提取到您的開發電腦。

如需詳細資訊，請參閱 [Azure IoT Edge 裝置容器](https://hub.docker.com/r/microsoft/iotedgedev/)。

### <a name="iot-edge-runtime-in-a-container"></a>容器中的 IoT Edge 執行階段

容器中的 IoT Edge 執行階段會提供採用裝置連接字串作為環境變數的完整執行階段。 此容器可讓您測試系統上可能不會以原生方式支援執行時間的 IoT Edge 模組和案例，例如 macOS。 您所部署的所有模組都將在執行階段容器之外啟動。 如果您希望執行階段以及所有已部署的模組存在於相同的容器內，請改為考慮 IoT Edge 裝置容器。

如需詳細資訊，請參閱[在容器中執行 Azure IoT Edge](https://github.com/Azure/iotedgedev/tree/master/docker/runtime)。

### <a name="iot-edge-device-container"></a>IoT Edge 裝置容器

IoT Edge 裝置容器是一個完整的 IoT Edge 裝置，可以在具有容器引擎的任何電腦上啟動。 裝置容器包含 IoT Edge 執行階段和容器引擎本身。 每個容器執行個體都是一個功能完整的自我佈建 IoT Edge 裝置。 裝置容器支援從遠端進行模組偵錯，前提是，該模組具備網路路由。 裝置容器適合用來快速建立大量的 IoT Edge 裝置，以大規模測試案例或 Azure Pipelines。 它也支援透過 helm 部署到 kubernetes。

如需詳細資訊，請參閱 [Azure IoT Edge 裝置容器](https://github.com/toolboc/azure-iot-edge-device-container)。

## <a name="devops-tools"></a>DevOps 工具

當您準備好針對廣泛的實際執行案例開發大規模的解決方案時，請利用新式的 DevOps 原則，包括自動化、監視，以及簡化的軟體工程程序。 IoT Edge 的擴充功能可支援 DevOps 工具，包括 Azure DevOps、Azure DevOps Projects 和 Jenkins。 如果您想要自訂現有的管線，或使用不同的 DevOps 工具 (例如 CircleCI 或 TravisCI)，則可以使用 IoT Edge 開發人員工具中隨附的 CLI 功能達到這個目的。

如需詳細資訊、指引和範例，請參閱下列頁面：

* [Azure IoT Edge 的持續整合與持續部署](how-to-continuous-integration-continuous-deployment.md)
* [使用 Azure DevOps Starter 建立 IoT Edge 的 CI/CD 管線](how-to-devops-starter.md)
* [Azure IoT Edge Jenkins 外掛程式](https://plugins.jenkins.io/azure-iot-edge)
* [IoT Edge DevOps GitHub 存放庫](https://github.com/toolboc/IoTEdge-DevOps)

---
title: 使用 CLI 部署遠端監視解決方案 - Azure | Microsoft Docs
description: 本操作指南會示範如何使用 CLI 來佈建遠端監視解決方案加速器。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.openlocfilehash: 501ca51a9542229a14e98a56679837950a82891e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258289"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-using-the-cli"></a>使用 CLI 部署遠端監視解決方案加速器

本操作指南會示範如何部署遠端監視解決方案加速器。 您將使用 CLI 來部署解決方案。 您還可以在azureiotsolutions.com使用基於 Web 的 UI 部署解決方案，瞭解此選項，請參閱[部署遠端監視解決方案加速器](quickstart-remote-monitoring-deploy.md)快速入門。

## <a name="prerequisites"></a>Prerequisites

若要部署遠端監視解決方案加速器，您需要一個有效的 Azure 訂用帳戶。

如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 有關詳細資訊，請參閱[Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。

若要執行 CLI，您必須在本機電腦上安裝 [Node.js](https://nodejs.org/)。

## <a name="install-the-cli"></a>安裝 CLI

若要安裝 CLI，請在您的命令列環境中執行下列命令：

```cmd/sh
npm install iot-solutions -g
```

## <a name="sign-in-to-the-cli"></a>登入 CLI

您必須使用 CLI 來登入 Azure 訂用帳戶，才能部署解決方案加速器：

```cmd/sh
pcs login
```

請依照畫面上的指示來完成登入程序。

## <a name="deployment-options"></a>部署選項

部署解決方案加速器時，有數個可設定部署程序的選項：

| 選項 | 值 | 描述 |
| ------ | ------ | ----------- |
| SKU    | `basic`, `standard`, `local` | _basic_ 部署適用於測試和示範環境，它會將所有微服務部署至單一虛擬機器。 _standard_ 部署適用於生產環境，它會將微服務部署至數部虛擬機器。 _local_ 部署會將 Docker 容器設定為在本機電腦上執行微服務，並且使用 Azure 雲端服務 (例如儲存體和 Cosmos DB)。 |
| 執行階段 | `dotnet`, `java` | 選取微服務的語言實作。 |

若要了解如何使用本機部署選項，請參閱[在本機執行遠端監視解決方案](iot-accelerators-remote-monitoring-deploy-local.md)。

## <a name="basic-and-standard-deployments"></a>基本和標準部署

本節摘要說明基本和標準部署之間的主要差異。

### <a name="basic"></a>基本

可以從[azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators)或使用 CLI 執行基本部署。

基本部署是專門設計來展示解決方案。 為了降低成本，所有微服務都會部署在單一虛擬機器中。 此部署不會使用已準備好用於生產環境的架構。

基本部署會在您的 Azure 訂用帳戶中建立下列服務︰

| Count | 資源                       | 類型         | 用於 |
|-------|--------------------------------|--------------|----------|
| 1     | [Linux 虛擬機器](https://azure.microsoft.com/services/virtual-machines/) | 標準 D1 V2  | 裝載微服務 |
| 1     | [Azure IoT 中心](https://azure.microsoft.com/services/iot-hub/)                  | S1 – 標準層 | 裝置管理與通訊 |
| 1     | [Azure 宇宙資料庫](https://azure.microsoft.com/services/cosmos-db/)              | 標準        | 儲存設定資料、規則、警示和其他冷儲存體 |  
| 1     | [Azure 儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)  | 標準        | 適用於 VM 和串流檢查點的儲存體 |
| 1     | [Web 應用程式](https://azure.microsoft.com/services/app-service/web/)        |                 | 裝載前端 Web 應用程式 |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | 管理使用者身分識別和安全性 |
| 1     | [Azure 地圖](https://azure.microsoft.com/services/azure-maps/)        | 標準                | 檢視資產位置 |
| 1     | [Azure 流分析](https://azure.microsoft.com/services/stream-analytics/)        |   3 個單位              | 提供即時分析 |
| 1     | [Azure 裝置佈建服務](https://docs.microsoft.com/azure/iot-dps/)        |       S1          | 大規模佈建裝置 |
| 1     | [Azure 時間序列見解](https://azure.microsoft.com/services/time-series-insights/)        |   S1 - 1 個單位              | 適用於訊息資料的儲存體，並能提供深入遙測分析 |

### <a name="standard"></a>標準

只能使用 CLI 執行標準部署。

標準部署是已準備好用於生產環境的部署，開發人員可以自訂和擴充。 當您準備自訂已準備好用於生產環境的架構 (基於規模調整與擴充性而建置) 時，請使用標準部署選項。 應用程式微服務是建置為 Docker 容器並使用 Azure Kubernetes Service 部署。 Kubernetes 協調器可部署、調整規模及管理微服務。

標準部署會在您的 Azure 訂用帳戶中建立下列服務︰

| Count | 資源                                     | SKU / 大小      | 用於 |
|-------|----------------------------------------------|-----------------|----------|
| 1     | [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service)| 使用完全受控 Kubernetes 容器協調流程服務，預設為 3 個代理程式|
| 1     | [Azure IoT 中心](https://azure.microsoft.com/services/iot-hub/)                     | S2 – 標準層 | 裝置管理、命令和控制 |
| 1     | [Azure 宇宙資料庫](https://azure.microsoft.com/services/cosmos-db/)                 | 標準        | 儲存設定資料，以及規則、警示和訊息等裝置遙測 |
| 5     | [Azure 存儲帳戶](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)    | 標準        | 4 個用於 VM 儲存體，1 個用於串流檢查點 |
| 1     | [應用服務](https://azure.microsoft.com/services/app-service/web/)             | S1 標準     | TLS 應用程式閘道 |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | 管理使用者身分識別和安全性 |
| 1     | [Azure 地圖](https://azure.microsoft.com/services/azure-maps/)        | 標準                | 檢視資產位置 |
| 1     | [Azure 流分析](https://azure.microsoft.com/services/stream-analytics/)        |   3 個單位              | 提供即時分析 |
| 1     | [Azure 裝置佈建服務](https://docs.microsoft.com/azure/iot-dps/)        |       S1          | 大規模佈建裝置 |
| 1     | [Azure 時間序列見解](https://azure.microsoft.com/services/time-series-insights/)        |   S1 - 1 個單位              | 適用於訊息資料的儲存體，並能提供深入遙測分析 |

> [!NOTE]
> 您可以在 中找到[https://azure.microsoft.com/pricing](https://azure.microsoft.com/pricing)這些服務的定價資訊。 您可以在 [Azure 入口網站](https://portal.azure.com/)中找到訂用帳戶的使用量與計費詳細資料。

## <a name="deploy-the-solution-accelerator"></a>部署解決方案加速器

部署範例：

### <a name="example-deploy-net-version"></a>範例：部署 .NET 版本

下列範例會示範如何部署基本 .NET 版的遠端監視解決方案加速器：

```cmd/sh
pcs -t remotemonitoring -s basic -r dotnet
```

### <a name="example-deploy-java-version"></a>範例：部署 Java 版本

下列範例會示範如何部署標準 Java 版的遠端監視解決方案加速器：

```cmd/sh
pcs -t remotemonitoring -s standard -r java
```

### <a name="pcs-command-options"></a>pcs 命令選項

當您執行 `pcs` 命令來部署解決方案時，系統會要求您提供：

- 您解決方案的名稱。 這個名稱必須是唯一的。
- 要使用的 Azure 訂用帳戶。
- 一個位置。
- 裝載微服務之虛擬機器帳戶的認證。 您可以使用這些認證來存取虛擬機器以進行疑難排解。

當 `pcs` 命令完成時，會顯示新解決方案加速器的 URL。 `pcs` 命令也會建立一個內含資訊的檔案 `{deployment-name}-output.json`，例如其所建立「IoT 中樞」名稱。

如需有關命令列參數的詳細資訊，請執行：

```cmd/sh
pcs -h
```

如需有關 CLI 的詳細資訊，請參閱 [如何使用 CLI](https://github.com/Azure/pcs-cli/blob/master/README.md) \(英文\)。

## <a name="next-steps"></a>後續步驟

在此操作指南中，您已了解如何：

> [!div class="checklist"]
> * 設定解決方案加速器
> * 部署解決方案加速器
> * 登入解決方案加速器

既然您已部署遠端監視解決方案，下一步便是[探索解決方案儀表板的功能](./quickstart-remote-monitoring-deploy.md)。

<!-- Next how-to guides in the sequence -->

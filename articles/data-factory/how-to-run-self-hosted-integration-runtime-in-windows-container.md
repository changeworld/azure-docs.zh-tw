---
title: 如何在 Windows 容器中執行 Self-Hosted Integration Runtime
description: 瞭解如何在 Windows 容器中執行 Self-Hosted Integration Runtime。
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/05/2020
ms.openlocfilehash: 7035da173102e59aec9c643381bad701d6facf38
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92634533"
---
# <a name="how-to-run-self-hosted-integration-runtime-in-windows-container"></a>如何在 Windows 容器中執行 Self-Hosted Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

本文將說明如何在 Windows 容器中執行 Self-Hosted Integration Runtime。
Azure Data Factory 正在提供 Self-Hosted Integration Runtime 的官方 windows 容器支援。 您可以下載 docker 組建原始程式碼，並將組建和執行程式結合在您自己的持續傳遞管線中。 

## <a name="prerequisites"></a>必要條件 
- [Windows 容器需求](/virtualization/windowscontainers/deploy-containers/system-requirements)
- Docker 2.3 版和更新版本 
- Self-Hosted Integration Runtime 4.11.7512.1 版和更新版本 
## <a name="get-started"></a>開始使用 
1.  安裝 Docker 並啟用 Windows 容器 
2.  從 https://github.com/Azure/Azure-Data-Factory-Integration-Runtime-in-Windows-Container 下載原始程式碼
3.  下載 ' SHIR ' 資料夾中的最新版本 SHIR 
4.  在 shell 中開啟您的資料夾： 
```console
cd "yourFolderPath"
```

5.  建立 windows docker 映射： 
```console
docker build . -t "yourDockerImageName" 
```
6.  執行 docker 容器： 
```console
docker run -d -e NODE_NAME="irNodeName" -e AUTH_KEY="IR_AUTHENTICATION_KEY" -e ENABLE_HA=true HA_PORT=8060 "yourDockerImageName"    
```
> [!NOTE]
> AUTH_KEY 是此命令的必要項。 NODE_NAME、ENABLE_HA 和 HA_PORT 都是選擇性的。 如果您未設定此值，則此命令會使用預設值。 ENABLE_HA 的預設值為 false，HA_PORT 為8060。

## <a name="container-health-check"></a>容器健康情況檢查 
在120秒啟動期間之後，健全狀況檢查程式每隔30秒會定期執行。 它會將 IR 健全狀況狀態提供給容器引擎。 

## <a name="limitations"></a>限制
目前，在 Windows 容器中執行 Self-Hosted Integration Runtime 時，不支援下列功能：
- HTTP Proxy 
- 使用 TLS/SSL 憑證的加密節點節點通訊 
- 產生和匯入備份 
- Daemon 服務 
- 自動更新 

### <a name="next-steps"></a>後續步驟
- 檢閱 [Azure Data Factory 中的整合執行階段概念](./concepts-integration-runtime.md)。
- 了解如何[在 Azure 入口網站中建立自我裝載的整合執行階段](./create-self-hosted-integration-runtime.md)。
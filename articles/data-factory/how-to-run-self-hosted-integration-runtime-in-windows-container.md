---
title: 如何在 Windows 容器中執行自我裝載 Integration Runtime
description: 瞭解如何在 Windows 容器中執行自我裝載的 Integration Runtime。
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
ms.openlocfilehash: d6f292ff89a70de90e6b86f19f73de26963d997f
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "87927550"
---
# <a name="how-to-run-self-hosted-integration-runtime-in-windows-container"></a>如何在 Windows 容器中執行自我裝載 Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

本文將說明如何在 Windows 容器中執行自我裝載的 Integration Runtime。
Azure Data Factory 提供自我裝載 Integration Runtime 的官方 windows 容器支援。 您可以下載 docker build 原始程式碼，並在您自己的持續傳遞管線中結合建築物和執行中的程式。 

## <a name="prerequisites"></a>必要條件 
- [Windows 容器需求](https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/system-requirements)
- Docker 版本2.3 和更新版本 
- 自我裝載 Integration Runtime 4.11.7512.1 和更新版本 
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
> AUTH_KEY 是此命令的必要參數。 NODE_NAME，ENABLE_HA 和 HA_PORT 是選擇性的。 如果您未設定此值，此命令會使用預設值。 ENABLE_HA 的預設值為 false，HA_PORT 為8060。

## <a name="container-health-check"></a>容器健全狀況檢查 
120秒的啟動期間之後，健全狀況檢查程式會每隔30秒定期執行一次。 它會將 IR 健全狀況狀態提供給容器引擎。 

## <a name="limitations"></a>限制
在 Windows 容器中執行自我裝載的 Integration Runtime 時，目前不支援下列功能：
- HTTP Proxy 
- 使用 TLS/SSL 憑證的加密節點節點通訊 
- 產生和匯入備份 
- Daemon 服務 
- 自動更新 

### <a name="next-steps"></a>後續步驟
- 檢閱 [Azure Data Factory 中的整合執行階段概念](https://docs.microsoft.com/azure/data-factory/concepts-integration-runtime)。
- 了解如何[在 Azure 入口網站中建立自我裝載的整合執行階段](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)。



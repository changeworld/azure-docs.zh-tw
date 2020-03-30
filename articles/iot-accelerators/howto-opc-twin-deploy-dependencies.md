---
title: 如何在 Azure 中部署 OPC 雙雲依賴項 |微軟文檔
description: 本文介紹如何部署執行本地開發和調試所需的 OPC 雙子 Azure 依賴項。
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 231d1efa02ec80e8ad56a8895d4262d774480111
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73824105"
---
# <a name="deploying-dependencies-for-local-development"></a>為本地開發部署依賴項

本文介紹如何僅部署執行本地開發和調試所需的 Azure 平臺服務。   最後，您將部署一個資源組，其中包含本地開發和調試所需的一切。

## <a name="deploy-azure-platform-services"></a>部署 Azure 平臺服務

1. 確保安裝了 PowerShell 和[AzureRM PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps)擴展。  打開命令提示符或終端並運行：

   ```bash
   git clone https://github.com/Azure/azure-iiot-components
   cd azure-iiot-components
   ```

   ```bash
   deploy -type local
   ```

2. 按照提示為部署的資源組分配名稱。  該腳本僅將依賴項部署到 Azure 訂閱中的此資源組，而不將微型服務部署到此資源組。  該腳本還在 Azure 活動目錄中註冊應用程式。  這是支援基於 AuTH 的身份驗證所必需的。  部署可能需要數分鐘的時間。

3. 腳本完成後，您可以選擇保存 .env 檔。  .env 環境檔是要在開發電腦上運行的所有服務和工具的設定檔。  

## <a name="troubleshooting-deployment-failures"></a>排除部署故障

### <a name="resource-group-name"></a>資源群組名稱

確保使用簡短和簡單的資源組名稱。  該名稱還用於命名資源，因為它必須符合資源命名要求。  

### <a name="azure-active-directory-aad-registration"></a>Azure 活動目錄 （AAD） 註冊

部署腳本嘗試在 Azure 活動目錄中註冊 AAD 應用程式。  根據您對所選 AAD 租戶的許可權，這可能會失敗。   選項有三個：

1. 如果從租戶清單中選擇了 AAD 租戶，請重新開機腳本並從清單中選擇其他腳本。
2. 或者，部署專用 AAD 租戶，重新開機腳本並選擇使用它。
3. 繼續而不進行身份驗證。  由於您在本地運行微服務，這是可以接受的，但不會模仿生產環境。  

## <a name="next-steps"></a>後續步驟

現在，您已成功將 OPC 孿生服務部署到現有專案，下面是建議的下一步：

> [!div class="nextstepaction"]
> [瞭解如何部署 OPC 雙子模組](howto-opc-twin-deploy-modules.md)

---
title: 如何將 OPC 孿生模組部署到現有 Azure 專案 |微軟文檔
description: 本文介紹如何將 OPC 孿生部署到現有專案。 您還可以瞭解如何排除部署故障。
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: b971ec13c71ccfd7d28ae6987593d09201b9b764
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73824126"
---
# <a name="deploy-opc-twin-to-an-existing-project"></a>將 OPC 孿生部署到現有專案

OPC 雙子模組在 IoT 邊緣上運行，並為 OPC 孿生和註冊表服務提供多項邊緣服務。

OPC Twin 微服務通過 OPC 雙 IoT 邊緣模組，方便工廠操作員與工廠車間 OPC UA 伺服器設備之間的通信。 微服務通過其 REST API 公開 OPC UA 服務（流覽、讀取、寫入和執行）。 

OPC UA 設備註冊表微服務提供對已註冊的 OPC UA 應用程式及其終結點的訪問。 操作員和管理員可以註冊和取消註冊新的 OPC UA 應用程式，並流覽現有應用程式，包括其終結點。 除了應用程式和端點管理外，註冊表服務還編目註冊的 OPC 雙 IoT 邊緣模組。 服務 API 允許您控制邊緣模組功能，例如啟動或停止伺服器發現（掃描服務），或啟動可以使用 OPC Twin 微服務訪問的新終結點孿生。

模組的核心是主管標識。 主管管理端點孿生，對應于使用相應的 OPC UA 註冊表 API 啟動的 OPC UA 伺服器終結點。 此終結點孿生將從雲中運行的 OPC Twin 微服務接收的 OPC UA JSON 轉換為 OPC UA 二進位消息，這些消息通過有狀態的安全通道發送到託管終結點。 主管還提供發現服務，將設備發現事件發送到 OPC UA 設備載入服務進行處理，其中這些事件會導致對 OPC UA 註冊表的更新。  本文介紹如何將 OPC 孿生模組部署到現有專案。

> [!NOTE]
> 有關部署詳細資訊和說明的詳細資訊，請參閱 GitHub[存儲庫](https://github.com/Azure/azure-iiot-opc-twin-module)。

## <a name="prerequisites"></a>Prerequisites

確保安裝了 PowerShell 和[AzureRM PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps)擴展。 如果尚未這樣做，則克隆此 GitHub 存儲庫。 在 PowerShell 中執行下列命令：

```powershell
git clone --recursive https://github.com/Azure/azure-iiot-components.git
cd azure-iiot-components
```

## <a name="deploy-industrial-iot-services-to-azure"></a>將工業 IoT 服務部署到 Azure

1. 在 PowerShell 會話中，運行：

    ```powershell
    set-executionpolicy -ExecutionPolicy Unrestricted -Scope Process
    .\deploy.cmd
    ```

2. 按照提示為部署的資源組和網站的名稱分配名稱。   該腳本將微服務及其 Azure 平臺依賴項部署到 Azure 訂閱中的資源組中。  該腳本還在 Azure 活動目錄 （AAD） 租戶中註冊應用程式，以支援基於 AuTH 的身份驗證。  部署需要幾分鐘時間。  成功部署解決方案後將看到的內容的示例：

   ![工業物聯網 OPC 孿生部署到現有專案](media/howto-opc-twin-deploy-existing/opc-twin-deploy-existing1.png)

   輸出包括公共終結點的 URL。 

3. 腳本成功完成後，選擇是否要保存`.env`該檔。  如果要使用主控台`.env`等工具連接到雲終結點，或部署用於開發和調試的模組，則需要環境檔。

## <a name="troubleshooting-deployment-failures"></a>排除部署故障

### <a name="resource-group-name"></a>資源群組名稱

確保使用簡短和簡單的資源組名稱。  該名稱還用於命名資源，因為它必須符合資源命名要求。  

### <a name="website-name-already-in-use"></a>網站名稱已在使用中

網站的名稱可能已在使用中。  如果遇到此錯誤，則需要使用不同的應用程式名稱。

### <a name="azure-active-directory-aad-registration"></a>Azure 活動目錄 （AAD） 註冊

部署腳本嘗試在 Azure 活動目錄中註冊兩個 AAD 應用程式。  根據您對所選 AAD 租戶的許可權，部署可能會失敗。 有兩個選項：

1. 如果從租戶清單中選擇了 AAD 租戶，請重新開機腳本並從清單中選擇其他腳本。
2. 或者，在另一個訂閱中部署專用 AAD 租戶，重新開機腳本，然後選擇使用它。

> [!WARNING]
> 在沒有身份驗證的情況下，切勿繼續。  如果您選擇這樣做，任何人都可以訪問您的 OPC 雙子終結點從互聯網未經身份驗證。   您可以隨時選擇["本地"部署選項](howto-opc-twin-deploy-dependencies.md)來踢輪胎。

## <a name="deploy-an-all-in-one-industrial-iot-services-demo"></a>部署一體式工業物聯網服務演示

您可以部署一體式演示，而不僅僅是服務和依賴項。  全部在一個演示包含三個 OPC UA 伺服器、OPC 雙子模組、所有微服務以及一個示例 Web 應用程式。  它旨在用於演示目的。

1. 請確保您具有存儲庫的克隆（請參閱上文）。 在存儲庫的根目錄中打開 PowerShell 提示符並運行：

    ```powershell
    set-executionpolicy -ExecutionPolicy Unrestricted -Scope Process
    .\deploy -type demo
    ```

2. 按照提示為資源組分配新名稱，為網站指定名稱。  成功部署後，腳本將顯示 Web 應用程式終結點的 URL。

## <a name="deployment-script-options"></a>部署腳本選項

該腳本採用以下參數：

```powershell
-type
```

部署類型（vm、本地、演示）

```powershell
-resourceGroupName
```

可以是現有資源組或新資源組的名稱。

```powershell
-subscriptionId
```

可選，將部署資源的訂閱 ID。

```powershell
-subscriptionName
```

或訂閱名稱。

```powershell
-resourceGroupLocation
```

可選，資源組位置。 如果指定，將嘗試在此位置創建新的資源組。

```powershell
-aadApplicationName
```

AAD 應用程式要在下註冊的名稱。

```powershell
-tenantId
```

要使用的 AAD 租戶。

```powershell
-credentials
```

## <a name="next-steps"></a>後續步驟

現在，您已經瞭解如何將 OPC Twin 部署到現有專案，下面是建議的下一步：

> [!div class="nextstepaction"]
> [OPC UA 用戶端和 OPC UA PLC 的安全通信](howto-opc-vault-secure.md)

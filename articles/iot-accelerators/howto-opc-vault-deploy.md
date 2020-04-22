---
title: 如何部署 OPC 保管庫證書管理服務 - Azure |微軟文件
description: 如何從頭開始部署 OPC 保管庫證書管理服務。
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 7ee186684b702a42335c6e1a7832cc5c761a69d0
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686945"
---
# <a name="build-and-deploy-the-opc-vault-certificate-management-service"></a>建構及部署 OPC 保存憑證管理服務

本文介紹如何在 Azure 中部署 OPC 保管庫證書管理服務。

> [!NOTE]
> 有關詳細資訊,請參閱 GitHub [OPC 保管庫儲存庫](https://github.com/Azure/azure-iiot-opc-vault-service)。

## <a name="prerequisites"></a>Prerequisites

### <a name="install-required-software"></a>安裝必要軟體

目前,生成和部署操作僅限於Windows。
這些範例都為 C# .NET 標準編寫,您需要建構服務和範例以進行部署。
.NET 標準所需的所有工具都附帶 .NET 核心工具。 請參考[到 .NET 核心開始](https://docs.microsoft.com/dotnet/articles/core/getting-started)。

1. [安裝 .NET 核心 2.1+][dotnet-install]。
2. [安裝 Docker(][docker-url]選擇可選,僅當需要本地 Docker 產生時)。
4. 安裝[PowerShell 的 Azure 命令列工具][powershell-install]。
5. 註冊 Azure[訂閱][azure-free]。

### <a name="clone-the-repository"></a>複製儲存機制

如果尚未這樣做,則克隆此 GitHub 儲存庫。 開啟命令提示符或終端,並執行以下內容:

```bash
git clone https://github.com/Azure/azure-iiot-opc-vault-service
cd azure-iiot-opc-vault-service 
```

或者,您也可以直接在 Visual Studio 2017 中克隆該回購。

### <a name="build-and-deploy-the-azure-service-on-windows"></a>在 Windows 上產生及部署 Azure 服務

PowerShell 文稿提供了一種部署 OPC Vault 微服務和應用程式的簡便方法。

1. 在回購根處打開 PowerShell 視窗。 
3. 跳到部署資料夾`cd deploy`。
3. 選擇不太可能與其他已`myResourceGroup`部署的網頁發生衝突的名稱。 請參閱本文後面的「已在使用的網站名稱」 部分。
5. 開始`.\deploy.ps1`部署以互動式安裝,或輸入完整的命令列:  
`.\deploy.ps1  -subscriptionName "MySubscriptionName" -resourceGroupLocation "East US" -tenantId "myTenantId" -resourceGroupName "myResourceGroup"`
7. 如果計劃使用此部署進行開發,則添加`-development 1`以啟用斯瓦格 UI,並部署調試生成。
6. 按照腳本中的說明登錄訂閱並提供其他資訊。
9. 成功產生與部署作業後,您應該會看到以下訊息:
   ```
   To access the web client go to:
   https://myResourceGroup.azurewebsites.net

   To access the web service go to:
   https://myResourceGroup-service.azurewebsites.net

   To start the local docker GDS server:
   .\myResourceGroup-dockergds.cmd

   To start the local dotnet GDS server:
   .\myResourceGroup-gds.cmd
   ```

   > [!NOTE]
   > 如果出現問題,請參閱本文後面的"故障排除部署故障"部分。

8. 開啟您最喜愛的瀏覽器,然後開啟應用程式頁面:`https://myResourceGroup.azurewebsites.net`
8. 為 Web 應用和 OPC Vault 微服務提供幾分鐘時間,在部署後預熱。 網頁主頁可能會首次使用,最多一分鐘,直到您得到第一個回應。
11. 要檢視斯瓦格 API,請開啟:`https://myResourceGroup-service.azurewebsites.net`
13. 要使用 dotnet 啟動本地`.\myResourceGroup-gds.cmd`GDS 伺服器,應啟動 。 使用 Docker,`.\myResourceGroup-dockergds.cmd`開始。

可以重新部署具有相同設置的生成。 請注意,此類操作會更新所有應用程式機密,並可能重置 Azure 活動目錄 (Azure AD) 應用程式註冊中的某些設置。

也可以只重新部署 Web 應用二進位檔案。 使用參數`-onlyBuild 1`,服務和應用程式的新 zip 包部署到 Web 應用程式。

成功部署後,可以開始使用服務。 請參考[管理 OPC 保存函式庫憑證管理服務](howto-opc-vault-manage.md)。

## <a name="delete-the-services-from-the-subscription"></a>從訂閱中移除服務

方法：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 轉到部署服務的資源組。
3. 選取 [刪除資源群組]**** 並且確認。
4. 過一段時間后,將刪除所有已部署的服務元件。
5. 跳到**Azure 的目錄** > **套用註冊**。
6. 每個已部署的資源組應列出三個註冊。 註冊具有以下名稱: `resourcegroup-client` `resourcegroup-module`、 `resourcegroup-service`、 、 分別刪除每個註冊。

現在,所有已部署的元件都將被刪除。

## <a name="troubleshooting-deployment-failures"></a>排除部署故障

### <a name="resource-group-name"></a>資源群組名稱

使用簡短和簡單的資源組名稱。 該名稱還用於命名資源和服務 URL 首碼。 因此,它必須符合資源命名要求。  

### <a name="website-name-already-in-use"></a>網站名稱已在使用中

網站的名稱可能已在使用中。 您需要使用不同的資源組名稱。 部署腳本正在使用的主機名是:HTTPs:/resourcegroupname.azurewebsites.net\/和 HTT:/resourgroupname-service.azurewebsites.net。\/
其他服務名稱由短名稱哈希的組合構建,不太可能與其他服務衝突。

### <a name="azure-ad-registration"></a>Azure AD 註冊 

部署文稿嘗試在 Azure AD 中註冊三個 Azure AD 應用程式。 根據所選 Azure AD 租戶中的許可權,此操作可能會失敗。 有兩個選項：

- 如果從租戶清單中選擇了 Azure AD 租戶,請重新啟動文本並從清單中選擇其他腳本。
- 或者,在另一個訂閱中部署專用 Azure AD 租戶。 重新啟動腳本,然後選擇使用它。

## <a name="deployment-script-options"></a>部署文稿選項

該文稿採用以下參數:


```
-resourceGroupName
```

這可以是現有資源組或新資源組的名稱。

```
-subscriptionId
```


這是將部署資源的訂閱 ID。 這是選擇性的。

```
-subscriptionName
```


或者,您可以使用訂閱名稱。

```
-resourceGroupLocation
```


這是資源組位置。 如果指定,此參數將嘗試在此位置創建新的資源組。 此參數也是可選的。


```
-tenantId
```


這是要使用的 Azure AD 租戶。 

```
-development 0|1
```

這是為開發而部署的。 使用除錯生成,並將ASP.NET環境設置為"開發"。 在`.publishsettings`Visual Studio 2017 中創建用於導入,以允許它直接部署應用和服務。 此參數也是可選的。

```
-onlyBuild 0|1
```

這是重建和僅重新部署 Web 應用,並重建 Docker 容器。 此參數也是可選的。

[azure-free]:https://azure.microsoft.com/free/
[powershell-install]:https://azure.microsoft.com/downloads/#powershell
[docker-url]: https://www.docker.com/
[dotnet-install]: https://www.microsoft.com/net/learn/get-started

## <a name="next-steps"></a>後續步驟

現在,您已經瞭解如何從頭開始部署 OPC Vault,您可以:

> [!div class="nextstepaction"]
> [管理 OPC 保存庫](howto-opc-vault-manage.md)

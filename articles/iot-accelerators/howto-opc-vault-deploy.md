---
title: 如何部署 OPC 保存庫憑證管理服務-Azure |Microsoft Docs
description: 如何從頭開始部署 OPC 保存庫憑證管理服務。
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 98dc9345d2c8b392fd094458b612857d6d454739
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92071484"
---
# <a name="build-and-deploy-the-opc-vault-certificate-management-service"></a>建立並部署 OPC 保存庫憑證管理服務

> [!IMPORTANT]
> 當我們更新本文時，請參閱 [Azure 產業 IoT](https://azure.github.io/Industrial-IoT/) 以取得最新的內容。

本文說明如何在 Azure 中部署 OPC 保存庫憑證管理服務。

> [!NOTE]
> 如需詳細資訊，請參閱 GitHub [OPC 保存庫存放庫](https://github.com/Azure/azure-iiot-opc-vault-service)。

## <a name="prerequisites"></a>必要條件

### <a name="install-required-software"></a>安裝必要軟體

目前的組建和部署作業僅限 Windows。
這些範例全都是針對 c # .NET Standard 所撰寫，您必須建立服務和範例以進行部署。
您 .NET Standard 所需的所有工具都隨附于 .NET Core 工具。 請參閱 [開始使用 .Net Core](/dotnet/articles/core/getting-started)。

1. [安裝 .Net Core 2.1 +][dotnet-install]。
2. 只有在需要本機 Docker 組建) 時，才[安裝 Docker][docker-url] (選用。
4. 安裝 [適用于 PowerShell 的 Azure 命令列工具][powershell-install]。
5. 註冊 [Azure 訂][azure-free]用帳戶。

### <a name="clone-the-repository"></a>複製儲存機制

如果您還沒有這麼做，請複製此 GitHub 存放庫。 開啟命令提示字元或終端機，然後執行下列命令：

```bash
git clone https://github.com/Azure/azure-iiot-opc-vault-service
cd azure-iiot-opc-vault-service 
```

或者，您可以直接在 Visual Studio 2017 中複製存放庫。

### <a name="build-and-deploy-the-azure-service-on-windows"></a>在 Windows 上建立和部署 Azure 服務

PowerShell 腳本提供簡單的方式來部署 OPC 保存庫微服務和應用程式。

1. 在存放庫根目錄中開啟 PowerShell 視窗。 
3. 移至 [部署] 資料夾 `cd deploy` 。
3. 選擇不 `myResourceGroup` 太可能造成與其他已部署網頁衝突的名稱。 請參閱本文稍後的「已在使用中的網站名稱」一節。
5. 開始部署以 `.\deploy.ps1` 進行互動式安裝，或輸入完整命令列：  
`.\deploy.ps1  -subscriptionName "MySubscriptionName" -resourceGroupLocation "East US" -tenantId "myTenantId" -resourceGroupName "myResourceGroup"`
7. 如果您打算使用此部署進行開發，請新增 `-development 1` 以啟用 SWAGGER UI，以及部署 debug build。
6. 依照腳本中的指示登入您的訂用帳戶，並提供其他資訊。
9. 成功完成組建和部署作業之後，您應該會看到下列訊息：
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
   > 如果發生問題，請參閱本文稍後的「疑難排解部署失敗」一節。

8. 開啟您慣用的瀏覽器，然後開啟應用程式頁面： `https://myResourceGroup.azurewebsites.net`
8. 提供 web 應用程式和 OPC 保存庫微服務幾分鐘的時間，以便在部署之後立即進行。 Web 首頁可能會在第一次使用時停止回應，最多可達一分鐘，直到您取得第一個回應為止。
11. 若要查看 Swagger API，請開啟： `https://myResourceGroup-service.azurewebsites.net`
13. 若要使用 dotnet 啟動本機 GDS 伺服器，請啟動 `.\myResourceGroup-gds.cmd` 。 使用 Docker，開始 `.\myResourceGroup-dockergds.cmd` 。

您可以使用完全相同的設定來重新部署組建。 請注意，這類作業會更新所有的應用程式秘密，而且可能會重設 Azure Active Directory (Azure AD) 應用程式註冊中的部分設定。

您也可以只重新部署 web 應用程式二進位檔。 使用參數 `-onlyBuild 1` 時，會將服務和應用程式的新 zip 套件部署至 web 應用程式。

成功部署之後，您就可以開始使用服務。 請參閱 [管理 OPC 保存庫憑證管理服務](howto-opc-vault-manage.md)。

## <a name="delete-the-services-from-the-subscription"></a>從訂用帳戶刪除服務

方式如下：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 移至部署服務的資源群組。
3. 選取 [刪除資源群組] 並且確認。
4. 不久之後，就會刪除所有已部署的服務元件。
5. 前往 [Azure Active Directory] > [應用程式註冊]。
6. 針對每個已部署的資源群組，應會列出三個註冊。 註冊的名稱如下： `resourcegroup-client` 、 `resourcegroup-module` 、 `resourcegroup-service` 。 分別刪除每個註冊。

現在會移除所有已部署的元件。

## <a name="troubleshooting-deployment-failures"></a>針對部署失敗進行疑難排解

### <a name="resource-group-name"></a>資源群組名稱

使用簡短且簡單的資源組名。 此名稱也會用來命名資源和服務 URL 前置詞。 因此，它必須符合資源命名需求。  

### <a name="website-name-already-in-use"></a>網站名稱已在使用中

網站的名稱可能已在使用中。 您必須使用不同的資源組名。 部署腳本所使用的主機名稱為： HTTPs： \/ /resourcegroupname.azurewebsites.net 和 HTTPs： \/ /resourgroupname-service.azurewebsites.net。
服務的其他名稱是由簡短名稱雜湊的組合所建立，而且不太可能與其他服務發生衝突。

### <a name="azure-ad-registration"></a>Azure AD 註冊 

部署腳本會嘗試在 Azure AD 中註冊三個 Azure AD 應用程式。 根據所選 Azure AD 租使用者中的許可權，這項作業可能會失敗。 有兩個選項：

- 如果您從租使用者清單中選擇 Azure AD 租使用者，請重新開機腳本，並從清單中選擇不同的租使用者。
- 或者，將私人 Azure AD 租使用者部署在另一個訂用帳戶中。 重新開機腳本，然後選擇使用它。

## <a name="deployment-script-options"></a>部署腳本選項

腳本會採用下列參數：


```
-resourceGroupName
```

這可以是現有或新資源群組的名稱。

```
-subscriptionId
```


這是將部署資源的訂用帳戶識別碼。 這是選擇性的。

```
-subscriptionName
```


或者，您可以使用訂用帳戶名稱。

```
-resourceGroupLocation
```


這是資源群組的位置。 如果有指定，這個參數會嘗試在此位置建立新的資源群組。 這個參數也是選擇性的。


```
-tenantId
```


這是要使用的 Azure AD 租使用者。 

```
-development 0|1
```

這是為了開發而部署的。 使用 debug build，並將 ASP.NET 環境設定為開發。 `.publishsettings`針對 Visual Studio 2017 中的匯入建立，讓它能夠直接部署應用程式和服務。 這個參數也是選擇性的。

```
-onlyBuild 0|1
```

這是為了重建和重新部署 web 應用程式，以及重建 Docker 容器。 這個參數也是選擇性的。

[azure-free]:https://azure.microsoft.com/free/
[powershell-install]:https://azure.microsoft.com/downloads/#powershell
[docker-url]: https://www.docker.com/
[dotnet-install]: https://www.microsoft.com/net/learn/get-started

## <a name="next-steps"></a>後續步驟

現在您已瞭解如何從頭開始部署 OPC 保存庫，您可以：

> [!div class="nextstepaction"]
> [管理 OPC 保存庫](howto-opc-vault-manage.md)
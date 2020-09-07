---
title: 教學課程：將現有自訂網域對應至 Azure Spring Cloud
description: 如何將現有自訂分散式名稱服務 (DNS) 名稱對應至 Azure Spring Cloud
author: MikeDodaro
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 05107d0246be2273c09e91573bd30a4108ac7795
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89290322"
---
# <a name="map-an-existing-custom-domain-to-azure-spring-cloud"></a>將現有的自訂網域對應至 Azure Spring Cloud
網域名稱服務 (DNS) 是一種在整個網路中儲存網路節點名稱的技術。 本教學課程會使用 CNAME 記錄來對應網域，例如 www.contoso.com。 其會使用憑證來保護自訂網域，並示範如何強制執行傳輸層安全性 (TLS)，也稱為安全通訊端層 (SSL)。 

憑證會將網路流量加密。 這些 TLS/SSL 憑證可以儲存在 Azure Key Vault 中。 

## <a name="prerequisites"></a>Prerequisites
* 部署至 Azure Spring Cloud 的應用程式 (請參閱[快速入門：使用 Azure 入口網站來啟動現有的 Azure Spring Cloud 應用程式](spring-cloud-quickstart.md)，或使用現有應用程式)。
* 網域名稱，其可存取網域提供者 (例如 GoDaddy) 的 DNS 登錄。
* 來自第三方提供者的私人憑證 (也就是您的自我簽署憑證)。 此憑證必須符合網域。
* [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)的已部署執行個體

## <a name="import-certificate"></a>匯入憑證 
匯入憑證的程序要求將 PEM 或 PFX 編碼的檔案放在磁碟上，而且您必須擁有私密金鑰。 

若要將憑證上傳至金鑰保存庫：
1. 移至您的金鑰保存庫執行個體。
1. 在左側瀏覽窗格中，按一下 [憑證]。
1. 在上層功能表上，按一下 [產生/匯入]。
1. 在 [憑證建立方法] 底下的 [建立憑證] 對話方塊中，選取 `Import`。
1. 在 [上傳憑證檔案] 之下，瀏覽至憑證位置，然後加以選取。
1. 在 [密碼] 之下，輸入憑證的私密金鑰。
1. 按一下頁面底部的 [新增] 。

    ![匯入憑證 1](./media/custom-dns-tutorial/import-certificate-a.png)

若要在匯入憑證之前，向 Azure Spring Cloud 授與您金鑰保存庫的存取權：
1. 移至您的金鑰保存庫執行個體。
1. 在左側瀏覽窗格中，按一下 [存取原則]。
1. 在上層功能表上，按一下 [新增存取原則]。
1. 填入資訊、按一下 [新增] 按鈕，然後 [儲存] 存取原則。

| 秘密權限 | 憑證權限 | 選取主體 |
|--|--|--|
| 取得、列出 | 取得、列出 | Azure Spring Cloud 網域 - 管理 |

![匯入憑證 2](./media/custom-dns-tutorial/import-certificate-b.png)

或者，您也可以使用 Azure CLI 向 Azure Spring Cloud 授與金鑰保存庫的存取權。

透過下列命令取得物件識別碼。
```
az ad sp show --id 03b39d0f-4213-4864-a245-b1476ec03169 --query objectId
```

向 Azure Spring Cloud 授與金鑰保存庫的讀取權限，並取代下列命令中的物件識別碼。
```
az keyvault set-policy -g <key vault resource group> -n <key vault name>  --object-id <object id> --certificate-permissions get list --secret-permissions get list
``` 

若要將憑證匯入至 Azure Spring Cloud：
1. 移至您的服務執行個體。 
1. 從應用程式的左側瀏覽窗格中，選取 [TLS/SSL 設定]。
1. 然後按一下 [匯入 Key Vault 憑證]。

    ![匯入憑證](./media/custom-dns-tutorial/import-certificate.png)

或者，您也可以使用 Azure CLI 來匯入憑證：

```
az spring-cloud certificate add --name <cert name> --vault-uri <key vault uri> --vault-certificate-name <key vault cert name>
```

> [!IMPORTANT] 
> 在執行先前的匯入憑證命令之前，請先確定您已向 Azure Spring Cloud 授與您金鑰保存庫的存取權。 如果您還沒有這麼做，則可以執行下列命令來授與存取權限。

```
az keyvault set-policy -g <key vault resource group> -n <key vault name>  --object-id 938df8e2-2b9d-40b1-940c-c75c33494239 --certificate-permissions get list
``` 

當您成功匯入憑證時，您會在 [私密金鑰憑證] 清單中看到該憑證。

![私密金鑰憑證](./media/custom-dns-tutorial/key-certificates.png)

或者，您也可以使用 Azure CLI 來顯示憑證清單：

```
az spring-cloud certificate list
```

> [!IMPORTANT] 
> 若要使用此憑證保護自訂網域，您仍需要將憑證繫結至特定網域。 請依照本文件中「新增 SSL 繫結」標題之下的步驟操作。

## <a name="add-custom-domain"></a>新增自訂網域
您可以使用 CNAME 記錄，將自訂 DNS 名稱對應至 Azure Spring Cloud。 

> [!NOTE] 
> 不支援 A 記錄。 

### <a name="create-the-cname-record"></a>建立 CNAME 記錄
移至您的 DNS 提供者並新增 CNAME 記錄，以將您的網域對應至 <service_name>.azuremicroservices.io。 此處的 <service_name> 是 Azure Spring Cloud 執行個體的名稱。 我們支援萬用字元網域和子網域。 新增 CNAME 之後，DNS 記錄分頁會如下列範例所示： 

![[DNS 記錄] 頁面](./media/custom-dns-tutorial/dns-records.png)

## <a name="map-your-custom-domain-to-azure-spring-cloud-app"></a>將自訂網域對應至 Azure Spring Cloud 應用程式
如果您在 Azure Spring Cloud 中沒有任何應用程式，請依照指示操作：[快速入門：使用 Azure 入口網站來啟動現有的 Azure Spring Cloud 應用程式](https://review.docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-portal?branch=master)。

移至應用程式頁面。

1. 選取 [自訂網域]。
2. 然後 [新增自訂網域]。 

    ![自訂網域](./media/custom-dns-tutorial/custom-domain.png)

3. 輸入您已新增 CNAME 記錄的完整網域名稱，例如 www.contoso.com。 請確定 [主機名稱記錄類型] 設為 CNAME (<service_name>.azuremicroservices.io)
4. 按一下 [驗證] 以啟用 [新增] 按鈕。
5. 按一下 [新增] 。

    ![新增自訂網域](./media/custom-dns-tutorial/add-custom-domain.png)

或者，您也可以使用 Azure CLI 來新增自訂網域：
```
az spring-cloud app custom-domain bind --domain-name <domain name> --app <app name> 
```

一個應用程式可以有多個網域，但一個網域只能對應到一個應用程式。 將自訂網域成功對應至應用程式時，您會在自訂網域資料表上看到該網域。

![自訂網域資料表](./media/custom-dns-tutorial/custom-domain-table.png)

或者，您也可以使用 Azure CLI 來顯示自訂網域清單：
```
az spring-cloud app custom-domain list --app <app name> 
```

> [!NOTE]
> 自訂網域的 [不安全] 標籤表示其尚未繫結至 SSL 憑證。 任何從瀏覽器到自訂網域的 HTTPS 要求都會收到錯誤或警告。

## <a name="add-ssl-binding"></a>新增 SSL 繫結
在自訂網域資料表中，選取 [新增 ssl 繫結]，如上圖所示。  
1. 選取您的 [憑證] 或將其匯入。
1. 按一下 [檔案] 。

    ![新增 SSL 繫結 1](./media/custom-dns-tutorial/add-ssl-binding.png)

或者，您也可以使用 Azure CLI 來**新增 SSL 繫結**：
```
az spring-cloud app custom-domain update --domain-name <domain name> --certificate <cert name> --app <app name> 
```

成功新增 SSL 繫結之後，網域狀態會是安全的：[良好]。 

![新增 SSL 繫結 2](./media/custom-dns-tutorial/secured-domain-state.png)

## <a name="enforce-https"></a>強制使用 HTTPS
根據預設，任何人仍然可使用 HTTP 存取您的應用程式，但您可以將所有 HTTP 要求重新導向至 HTTPS 連接埠。

在應用程式頁面的左側導覽中，選取 [自訂網域]。 然後，將 [僅限 HTTPS] 設為 [True]。

![新增 SSL 繫結 3](./media/custom-dns-tutorial/enforce-http.png)

或者，您也可以使用 Azure CLI 來強制執行 HTTPS：
```
az spring-cloud app update -name <app-name> --https-only <true|false> -g <resource group> --service <service-name>
```

當作業完成時，瀏覽至指向您的應用程式的任何 HTTPS URL。 請注意，HTTP URL 沒有作用。

## <a name="see-also"></a>另請參閱
* [什麼是 Azure 金鑰保存庫？](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
* [匯入憑證](https://docs.microsoft.com/azure/key-vault/certificate-scenarios#import-a-certificate)
* [使用 Azure CLI 啟動您的 Spring Cloud 應用程式](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli)


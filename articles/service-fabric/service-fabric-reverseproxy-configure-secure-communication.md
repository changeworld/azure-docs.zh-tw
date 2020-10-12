---
title: Azure Service Fabric 反向 proxy 安全通訊
description: 設定反向 proxy，以在 Azure Service Fabric 應用程式中啟用安全的端對端通訊。
ms.topic: conceptual
ms.date: 08/10/2017
ms.openlocfilehash: b01ce559b3c790164992d6618149afa9df069466
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86256130"
---
# <a name="connect-to-a-secure-service-with-the-reverse-proxy"></a>安全服務與反向 Proxy 的連線

本文說明如何建立反向 Proxy 和服務之間的安全連線，以確保端對端的安全通道。 若要深入了解反向 Proxy，請參閱 [Azure Service Fabric 中的反向 Proxy](service-fabric-reverseproxy.md)

> [!IMPORTANT]
> 僅有將反向 Proxy 設為接聽 HTTPS 時，才支援安全的服務連線。 此文章是以此情況作為前提。 若要在 Service Fabric 中設定反向 Proxy，請參閱[在 Azure Service Fabric 中設定反向 Proxy](service-fabric-reverseproxy-setup.md)。

## <a name="secure-connection-establishment-between-the-reverse-proxy-and-services"></a>建立反向 Proxy 與服務之間的安全連線 

### <a name="reverse-proxy-authenticating-to-services"></a>服務的反向 Proxy 驗證：
反向 Proxy 會使用其憑證對服務進行自我識別。 針對 Azure 叢集，該憑證會以 Resource Manager 範本之 [**Microsoft.ServiceFabric/clusters** \(英文\) ](/azure/templates/microsoft.servicefabric/clusters) [資源類型區段](../azure-resource-manager/templates/template-syntax.md)中的 ***reverseProxyCertificate*** 屬性來指定。 針對獨立叢集，該憑證會以 ClusterConfig.json 之 **Security** 區段中的 ***ReverseProxyCertificate*** 或 ***ReverseProxyCertificateCommonNames*** 屬性來指定。 若要深入了解，請參閱[在獨立叢集上啟用反向 Proxy](service-fabric-reverseproxy-setup.md#enable-reverse-proxy-on-standalone-clusters)。 

服務可以實作邏輯來驗證反向 Proxy 出示的憑證。 服務可以將接受的用戶端憑證詳細資料指定為設定套件中的組態設定。 系統會在執行階段時加以讀取，並用來驗證反向 Proxy 出示的憑證。 若要新增組態設定，請參閱[管理應用程式參數](service-fabric-manage-multiple-environment-app-configuration.md)。 

### <a name="reverse-proxy-verifying-the-services-identity-via-the-certificate-presented-by-the-service"></a>反向 Proxy 可透過服務出示的憑證，來驗證服務的身分識別：
反向 Proxy 需支援下列原則，才能針對服務出示的憑證執行伺服器憑證驗證：None、ServiceCommonNameAndIssuer 和 ServiceCertificateThumbprints。
若要選擇反向 Proxy 要使用的原則，請在 [fabricSettings](service-fabric-cluster-fabric-settings.md) 下方的 **ApplicationGateway/Http** 區段中指定 **ApplicationCertificateValidationPolicy**。

下節會逐一說明這些選項的組態詳細資料。

### <a name="service-certificate-validation-options"></a>服務憑證驗證選項 

- **None**：反向 Proxy 會略過驗證透過 Proxy 的服務憑證，並建立安全連線。 此為預設行為。
在 [ApplicationGateway/Http****](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) 區段中，為 **ApplicationCertificateValidationPolicy** 指定 **None** 值。

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "ApplicationCertificateValidationPolicy",
                   "value": "None"
                 }
               ]
             }
           ],
           ...
   }
   ```

- **ServiceCommonNameAndIssuer**：反向 Proxy 會依據憑證的通用名稱和直接簽發者的指紋，來驗證服務出示的憑證：在 [**ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) 區段中，為 **ApplicationCertificateValidationPolicy** 指定 **ServiceCommonNameAndIssuer** 值。

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "ApplicationCertificateValidationPolicy",
                   "value": "ServiceCommonNameAndIssuer"
                 }
               ]
             }
           ],
           ...
   }
   ```

   若要指定服務的通用名稱和簽發者指紋清單，請在 **fabricSettings** 下方新增 [**ApplicationGateway/Http/ServiceCommonNameAndIssuer**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttpservicecommonnameandissuer) 區段，如下所示。 您可在**參數**陣列中新增多組憑證通用名稱和簽發者指紋。 

   如果端點反向 proxy 所連接的憑證會顯示一般名稱的憑證，而簽發者指紋符合此處指定的任何值，則會建立 TLS 通道。
   如果憑證詳細資料比對發生錯誤，反向 Proxy 就無法完成用戶端的要求，並會顯示狀態碼 502 (閘道錯誤)。 HTTP 狀態行也會出現「SSL 憑證無效」的句子。 

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http/ServiceCommonNameAndIssuer",
               "parameters": [
                 {
                   "name": "WinFabric-Test-Certificate-CN1",
                   "value": "b3 44 9b 01 8d 0f 68 39 a2 c5 d6 2b 5b 6c 6a c8 22 b4 22 11"
                 },
                 {
                   "name": "WinFabric-Test-Certificate-CN2",
                   "value": "b3 44 9b 01 8d 0f 68 39 a2 c5 d6 2b 5b 6c 6a c8 22 11 33 44"
                 }
               ]
             }
           ],
           ...
   }
   ```

- **ServiceCertificateThumbprints**：反向 Proxy 會依據透過 Proxy 之服務的指紋來驗證其憑證。 當服務設為使用自我簽署憑證時，您可以選擇採用此路由：在 [**ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) 區段中，為 **ApplicationCertificateValidationPolicy** 指定 **ServiceCertificateThumbprints** 值。

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "ApplicationCertificateValidationPolicy",
                   "value": "ServiceCertificateThumbprints"
                 }
               ]
             }
           ],
           ...
   }
   ```

   另外，請在 **ApplicationGateway/Http** 區段中，為指紋指定 **ServiceCertificateThumbprints** 項目。 您可以在值欄位中，以逗號分隔的清單來指定多個指紋，如下所示：

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                   ...
                 {
                   "name": "ServiceCertificateThumbprints",
                   "value": "78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 bf,78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 b9"
                 }
               ]
             }
           ],
           ...
   }
   ```

   如果伺服器憑證的指紋列在此設定專案中，反向 proxy 就會成功進行 TLS 連接。 否則，它會終止連線而無法完成用戶端的要求，並顯示 502 (閘道錯誤)。 HTTP 狀態行也會出現「SSL 憑證無效」的句子。

## <a name="endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints"></a>當服務公開安全與不安全端點時的端點選取邏輯
Service Fabric 支援設定多個服務端點。 如需詳細資訊，請參閱[在服務資訊清單中指定資源](service-fabric-service-manifest-resources.md)。

反向 Proxy 會依據[服務 URI](./service-fabric-reverseproxy.md#uri-format-for-addressing-services-by-using-the-reverse-proxy) 中的 **ListenerName** 查詢參數，來選取其中一個用來轉送要求的端點。 如果未指定 **ListenerName** 參數，反向 Proxy 可以從端點清單中挑選任何端點。 根據為服務設定的端點，選取的端點可以是 HTTP 或 HTTPS 端點。 您可能會有要在「僅限安全模式」中執行反向 Proxy 的情況或需求；也就是說，您不希望安全的反向 Proxy 將要求轉送到不安全的端點。 若要將反向 Proxy 設定為僅限安全模式，可在 [**ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) 區段中，將 **SecureOnlyMode** 組態項目的值指定為 **true**。   

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
                ...
              {
                "name": "SecureOnlyMode",
                "value": true
              }
            ]
          }
        ],
        ...
}
```

> [!NOTE]
> 在 **SecureOnlyMode** 中運作時，如果用戶端指定的 **ListenerName** 是對應至 HTTP (不安全) 端點，則反向 Proxy 無法完成要求，並會顯示 HTTP 狀態碼 404 (找不到)。

## <a name="setting-up-client-certificate-authentication-through-the-reverse-proxy"></a>設定透過反向 Proxy 的用戶端憑證驗證
TLS 終止會發生在反向 proxy，且所有用戶端憑證資料都會遺失。 若要讓服務執行用戶端憑證驗證，請在 [**ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) 區段中，指定 **ForwardClientCertificate** 設定。

1. 當 **>forwardclientcertificate** 設定為 **false**時，反向 proxy 將不會在與用戶端的 TLS 交握期間要求用戶端憑證。
此為預設行為。

2. 當 **>forwardclientcertificate** 設定為 **true**時，反向 proxy 會在其與用戶端的 TLS 交握期間要求用戶端的憑證。
接著，它會使用名為 **X-Client-Certificate** 的自訂 HTTP 標頭來轉送用戶端憑證資料。 標頭值是用戶端憑證的 PEM 格式字串 (base64 編碼)。 檢查憑證資料之後，服務或許能成功完成要求，也可能無法完成要求，並顯示適當的狀態碼。
如果用戶端未出示憑證，反向 proxy 會轉送空白標頭，並讓服務處理此情況。

> [!NOTE]
> 反向 proxy 的作用只是轉送服務。 它不會執行任何用戶端憑證的驗證。


## <a name="next-steps"></a>接下來的步驟
* [在叢集上安裝及設定反向 Proxy](service-fabric-reverseproxy-setup.md)。
* 請參閱 [設定反向 proxy 以連接到安全的服務](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample#configure-reverse-proxy-to-connect-to-secure-services)
* 請參閱 [GitHub 上的範例專案](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)中服務之間的 HTTP 通訊範例。
* [使用 Reliable Services 遠端服務進行遠端程序呼叫](service-fabric-reliable-services-communication-remoting.md)
* [在 Reliable Services 中使用 OWIN 的 Web API](./service-fabric-reliable-services-communication-aspnetcore.md)
* [管理叢集憑證](service-fabric-cluster-security-update-certs-azure.md)

---
title: 設定自訂設定
description: 設定適用於整個 Azure App Service 環境的設定。 了解如何使用 Azure Resource Manager 範本來執行此動作。
author: stefsch
ms.assetid: 1d1d85f3-6cc6-4d57-ae1a-5b37c642d812
ms.topic: tutorial
ms.date: 10/03/2020
ms.author: stefsch
ms.custom: mvc, seodec18
ms.openlocfilehash: 88163c07d570df5e0ff343776c17c463010ce368
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91713280"
---
# <a name="custom-configuration-settings-for-app-service-environments"></a>App Service 環境的自訂組態設定
## <a name="overview"></a>概觀
因為 App Service 環境 (ASEs) 對單一客戶是隔離的，所以有某些專門套用到 App Service 環境的組態設定。 本文記錄各種可供 App Service 環境使用的特定自訂。

如果您沒有 App Service 環境，請參閱 [如何建立 App Service 環境](app-service-web-how-to-create-an-app-service-environment.md)。

您可以使用新 **clusterSettings** 屬性的陣列儲存 App Service 環境自訂。 這個屬性是在 *hostingEnvironments* Azure Resource Manager 實體的「屬性」字典中找到的。

下列縮寫的 Resource Manager 範本程式碼片段顯示 **clusterSettings** 屬性︰

```json
"resources": [
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/hostingEnvironments",
    "name": ...,
    "location": ...,
    "properties": {
        "clusterSettings": [
            {
                "name": "nameOfCustomSetting",
                "value": "valueOfCustomSetting"
            }
        ],
        "workerPools": [ ...],
        etc...
    }
}
```

**clusterSettings** 屬性可以包含在 Resource Manager 範本中，以更新 App Service 環境。

## <a name="use-azure-resource-explorer-to-update-an-app-service-environment"></a>使用 Azure 資源總管更新 App Service 環境
或者，您可以使用 [Azure 資源總管](https://resources.azure.com)更新 App Service 環境。  

1. 在 [資源總管] 中，移至 App Service 環境的節點 ([訂用帳戶]   > [resourceGroups]   > [提供者]   > [Microsoft.Web]   > [hostingEnvironments]  )。 接著，按一下您想要更新的特定 App Service 環境。
2. 按一下右窗格上方工具列中的 [讀取/寫入]  ，允許在資源總管中進行互動式編輯。  
3. 按一下藍色的 [編輯]  按鈕，開放編輯 Resource Manager 範本。
4. 向下捲動到右窗格底部。 **clusterSettings** 屬性位在最底部，您可以在此輸入或更新其值。
5. 輸入 (或複製並貼上) 您希望 **clusterSettings** 屬性出現的組態值陣列。  
6. 按一下右窗格上方的綠色 [PUT]  按鈕，確認 App Service 環境的變更。

不過，送出變更後，約需 30 分鐘乘以 App Service 環境中前端數量的時間，變更才會生效。
例如，如果 App Service 環境有四個前端，大約需要兩個小時才能完成組態更新。 實行組態變更時，App Service 環境上就不會進行其他調整作業或組態變更作業。

## <a name="enable-internal-encryption"></a>啟用內部加密

App Service 環境會以黑箱系統的方式運作，您看不到內部元件或系統內的通訊。 若要達到更高的輸送量，內部元件之間預設不會啟用加密。 系統是安全的，因為流量完全無法存取，而無法進行監視或存取。 如果您有需要從端對端完整加密資料路徑的合規性需求，可以透過 clusterSetting 達成此需求。  

```json
"clusterSettings": [
    {
        "name": "InternalEncryption",
        "value": "true"
    }
],
```
這會加密前端和背景工作角色之間 ASE 中的內部網路流量，加密分頁檔，同時加密背景工作角色磁碟。 啟用 InternalEncryption clusterSetting 之後，可能會對系統效能造成影響。 當您進行變更以啟用 InternalEncryption 時，您的 ASE 將會處於不穩定的狀態，直到變更完全傳播為止。 視您的 ASE 中有多少執行個體而定，變更的完整傳播可能需要幾小時才能完成。 強烈建議您不要在使用中的 ASE 上啟用此功能。 如果您需要在主動使用的 ASE 上啟用此功能，強烈建議您將流量轉向備份環境，直到作業完成為止。 


## <a name="disable-tls-10-and-tls-11"></a>停用 TLS 1.0 和 TLS 1.1

如果您想要依照應用程式基礎來管理應用程式的 TLS 設定，您可以使用[強制使用 TLS 設定](../configure-ssl-bindings.md#enforce-tls-versions)文件所提供的指導方針。 

如果您想要停用 ASE 中所有應用程式的所有輸入 TLS 1.0 和 TLS 1.1 流量，您可以設定下列 **clusterSettings** 項目：

```json
"clusterSettings": [
    {
        "name": "DisableTls1.0",
        "value": "1"
    }
],
```

設定的名稱指出 1.0，但若已設定，它會同時停用 TLS 1.0 與 TLS 1.1。

## <a name="change-tls-cipher-suite-order"></a>變更 TLS 加密套件順序
來自客戶的另一個問題是，他們是否可以修改由其伺服器交涉的加密的清單，而這可透過修改 **clusterSettings** 來達成，如下所示。 您可以從[此 MSDN 文章](https://msdn.microsoft.com/library/windows/desktop/aa374757\(v=vs.85\).aspx)擷取可用加密套件的清單。

```json
"clusterSettings": [
    {
        "name": "FrontEndSSLCipherSuiteOrder",
        "value": "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384_P256,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256"
    }
],
```

> [!WARNING]
> 如果對安全通道無法了解的加密套件設定了不正確的值，對您的伺服器的所有 TLS 通訊可能會停止運作。 在這種情況下，您必須從 **clusterSettings** 移除 FrontEndSSLCipherSuiteOrder** 項目，並提交更新的 Resource Manager 範本以還原回預設的加密套件設定。  請謹慎使用這項功能。

## <a name="get-started"></a>開始使用
Azure 快速入門 Resource Manager 範本網站包含具有 [建立 App Service 環境](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/)基本定義的範本。

<!-- LINKS -->

<!-- IMAGES -->

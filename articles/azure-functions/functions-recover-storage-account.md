---
title: 如何針對無法連線至 Azure Functions 執行階段的情形進行疑難排解。
description: 了解如何針對無效的儲存體帳戶進行疑難排解。
author: alexkarcher-msft
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: 910b582cb40b9f8aff6a553621b4677d6b019826
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963881"
---
# <a name="how-to-troubleshoot-functions-runtime-is-unreachable"></a>如何針對「無法連線至函式執行階段」的情形進行疑難排解

本文的目的是要在 Azure 入口網站中顯示「函式執行時間無法連線」錯誤訊息時進行疑難排解。 發生此錯誤時，您會看到下列錯誤字串顯示在入口網站中。

`Error: Azure Functions Runtime is unreachable. Click here for details on storage configuration`

當 Azure Functions 執行階段無法啟動時，就會發生這種情況。 之所以會發生這個錯誤，最常見的原因是函式應用程式無法存取其儲存體帳戶。 若要深入瞭解，請參閱[儲存體帳戶需求](storage-considerations.md#storage-account-requirements)。

本文的其餘部分可協助您針對此錯誤的下列原因進行疑難排解，包括如何識別和解決每個案例。

+ [儲存體帳戶已刪除](#storage-account-deleted)
+ [儲存體帳戶的應用程式設定已刪除](#storage-account-application-settings-deleted)
+ [儲存體帳戶的認證無效](#storage-account-credentials-invalid)
+ [無法存取儲存體帳戶](#storage-account-inaccessible)
+ [超過每日執行配額](#daily-execution-quota-full)
+ [您的應用程式位於防火牆後方](#app-is-behind-a-firewall)


## <a name="storage-account-deleted"></a>儲存體帳戶已刪除

每個函式應用程式都需要有儲存體帳戶才能運作。 如果該帳戶已刪除，函式就不會運作。

### <a name="how-to-find-your-storage-account"></a>如何尋找儲存體帳戶

藉由在應用程式設定中查閱儲存體帳戶名稱來開始。 `AzureWebJobsStorage` 或 `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` 會包含包裝在連接字串中的儲存體帳戶名稱。 如需詳細資訊，請參閱[這裡的應用程式設定參考](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)。

在 Azure 入口網站中搜尋儲存體帳戶，以了解其是否仍存在。 如果已遭刪除，您就必須重新建立儲存體帳戶，並取代儲存體連接字串。 函式程式碼將會遺失，所以您必須重新部署一次。

## <a name="storage-account-application-settings-deleted"></a>儲存體帳戶的應用程式設定已刪除

在上一個步驟中，如果您沒有儲存體帳戶連接字串，可能會遭到刪除或覆寫。 使用部署位置或 Azure Resource Manager 指令碼來設定應用程式設定時，最常會刪除應用程式設定。

### <a name="required-application-settings"></a>必要的應用程式設定

* 必要項
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* 取用方案函式所必備
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

請[在這裡閱讀這些應用程式設定的相關資訊](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)。

### <a name="guidance"></a>指導方針

* 請不要核取任何這些設定的 [位置設定]。 當您交換部署位置時，函數應用程式會中斷。
* 請勿在自動部署過程中修改這些設定。
* 在建立時，必須提供這些設定，且設定必須有效。 不包含這些設定的自動化部署會導致不會執行的函式應用程式，即使稍後再新增設定也是如此。

## <a name="storage-account-credentials-invalid"></a>儲存體帳戶的認證無效

如果您重新產生儲存體金鑰，則必須更新上述儲存體帳戶連接字串。 [在這裡閱讀更多關於儲存體金鑰管理的資訊](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)。

## <a name="storage-account-inaccessible"></a>無法存取儲存體帳戶

您的函數應用程式必須能夠存取儲存體帳戶。 導致函式無法存取儲存體帳戶的常見問題包括：

+ 部署到 App Service 環境（ASE）的函式應用程式，沒有正確的網路規則，以允許進出儲存體帳戶的流量。

+ 儲存體帳戶防火牆已啟用，且未設定為允許進出功能的流量。 若要深入了解，請參閱[設定 Azure 儲存體防火牆和虛擬網路](../storage/common/storage-network-security.md)。

## <a name="daily-execution-quota-full"></a>每日執行配額已滿

如果您已設定每日執行配額，則會暫時停用您的函式應用程式，這會導致許多入口網站控制項變得無法使用。 

+ 若要在[Azure 入口網站](https://portal.azure.com)中確認，請在函式應用程式中開啟 [**平臺功能**] > **函數應用程式設定**。 當您超過設定的**每日使用量配額**時，您會看到下列訊息：

    `The function app has reached daily usage quota and has been stopped until the next 24 hours time frame.`

+ 若要解決此問題，請移除或增加每日配額，然後重新開機您的應用程式。 否則，應用程式的執行會在隔天后遭到封鎖。

## <a name="app-is-behind-a-firewall"></a>應用程式位於防火牆後方

如果您的函式應用程式[裝載于內部負載平衡的 App Service 環境](../app-service/environment/create-ilb-ase.md)中，而且設定為封鎖輸入網際網路流量，或設定了[輸入 IP 限制](functions-networking-options.md#inbound-ip-restrictions)以封鎖網際網路存取，則無法連線到您的函式執行時間。 Azure 入口網站會直接呼叫執行中的應用程式來提取函式清單，也會對 KUDU 端點進行 HTTP 呼叫。 [`Platform Features`] 索引標籤下的平台層級設定仍然可以使用。

若要驗證您的 ASE 設定，請流覽至 ASE 所在子網的 NSG，並驗證輸入規則，以允許來自您正在存取應用程式之電腦的公用 IP 流量。 您也可以從連線至執行應用程式之虛擬網路的電腦，或在虛擬網路中執行的虛擬機器，使用入口網站。 [如需輸入規則設定的詳細資訊，請參閱這裡](../app-service/environment/network-info.md#network-security-groups)

## <a name="next-steps"></a>後續步驟

瞭解如何監視您的函數應用程式：

> [!div class="nextstepaction"]
> [監視 Azure Functions](functions-monitoring.md)

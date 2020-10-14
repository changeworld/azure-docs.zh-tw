---
title: 疑難排解錯誤：無法存取 Azure Functions 執行階段
description: 了解如何針對無效的儲存體帳戶進行疑難排解。
ms.topic: article
ms.date: 09/05/2018
ms.openlocfilehash: 094f4f45be0ae4173af434be178f2eae2b21b6be
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92054724"
---
# <a name="troubleshoot-error-azure-functions-runtime-is-unreachable"></a>疑難排解錯誤：「無法連線到 Azure Functions 執行階段」

本文可協助您針對出現在 Azure 入口網站中的下列錯誤字串進行疑難排解：

> 「錯誤：無法連線到 Azure Functions 執行階段。 如需儲存體設定的詳細資訊，請按一下這裡。」

當 Azure Functions 執行階段無法啟動時，就會發生此問題。 此問題最常見的原因是函式應用程式已失去其儲存體帳戶的存取權。 如需詳細資訊，請參閱 [儲存體帳戶需求](./functions-create-function-app-portal.md#storage-account-requirements)。

本文的其餘部分可協助您疑難排解下列錯誤的原因，包括如何識別和解決每個案例。

## <a name="storage-account-was-deleted"></a>儲存體帳戶已刪除

每個函式應用程式都需要有儲存體帳戶才能運作。 如果刪除該帳戶，您的函式將無法運作。

首先，在您的應用程式設定中查閱您的儲存體帳戶名稱。 `AzureWebJobsStorage`或 `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` 包含在連接字串中包裝的儲存體帳戶名稱。 如需詳細資訊，請參閱 [Azure Functions 的應用程式設定參考](./functions-app-settings.md#azurewebjobsstorage)。

在 Azure 入口網站中搜尋您的儲存體帳戶，以查看它是否仍然存在。 如果已刪除，請重新建立儲存體帳戶，並取代儲存體連接字串。 您的函式程式碼會遺失，而您需要重新部署它。

## <a name="storage-account-application-settings-were-deleted"></a>儲存體帳戶應用程式設定已刪除

在先前的步驟中，如果您找不到儲存體帳戶連接字串，則可能會遭到刪除或覆寫。 當您使用部署位置或 Azure Resource Manager 腳本來設定應用程式設定時，最常見的情況是刪除應用程式設定。

### <a name="required-application-settings"></a>必要的應用程式設定

* 必要：
    * [`AzureWebJobsStorage`](./functions-app-settings.md#azurewebjobsstorage)
* 耗用量和高階方案函式的必要功能：
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](./functions-app-settings.md)
    * [`WEBSITE_CONTENTSHARE`](./functions-app-settings.md)

如需詳細資訊，請參閱 [Azure Functions 的應用程式設定參考](./functions-app-settings.md)。

### <a name="guidance"></a>指引

* 請勿勾選任何這些設定的「位置設定」。 如果您交換部署位置，函數應用程式會中斷。
* 請勿將這些設定修改為自動化部署的一部分。
* 在建立時，必須提供這些設定，且設定必須有效。 未包含這些設定的自動化部署會導致函式應用程式不會執行，即使稍後新增設定也是如此。

## <a name="storage-account-credentials-are-invalid"></a>儲存體帳號憑證無效

如果您重新產生儲存體金鑰，則必須更新先前所討論的儲存體帳戶連接字串。 如需有關儲存體金鑰管理的詳細資訊，請參閱 [建立 Azure 儲存體帳戶](../storage/common/storage-account-create.md)。

## <a name="storage-account-is-inaccessible"></a>無法存取儲存體帳戶

您的函數應用程式必須能夠存取儲存體帳戶。 封鎖函數應用程式存取儲存體帳戶的常見問題包括：

* 函數應用程式會部署到您的 App Service 環境，而不需要正確的網路規則，以允許進出儲存體帳戶的流量。

* 儲存體帳戶防火牆已啟用，且未設定為允許進出函式的流量。 如需詳細資訊，請參閱[設定 Azure 儲存體防火牆和虛擬網路](../storage/common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

## <a name="daily-execution-quota-is-full"></a>每日執行配額已滿

如果您已設定每日執行配額，則會暫時停用您的函式應用程式，這會導致許多入口網站控制項變得無法使用。 

若要確認[Azure 入口網站](https://portal.azure.com)中的配額，請在您的函式應用程式中選取**平臺功能**  >  **函數應用程式設定**。 如果您超過所設定的 **每日使用量配額** ，則會顯示下列訊息：

  > 「函式應用程式已達到每日使用量配額，並已停止到下一個24小時的時間範圍內。」

若要解決此問題，請移除或增加每日配額，然後重新開機您的應用程式。 否則，會封鎖應用程式的執行，直到下一天為止。

## <a name="app-is-behind-a-firewall"></a>應用程式位於防火牆後方

您的函式執行時間可能會因為下列其中一個原因而無法連接：

* 您的函式應用程式 [裝載于內部負載平衡的 App Service 環境](../app-service/environment/create-ilb-ase.md) ，並設定為封鎖輸入的網際網路流量。

* 您的函數應用程式具有設定為封鎖網際網路存取的 [輸入 IP 限制](functions-networking-options.md#inbound-ip-restrictions) 。 

Azure 入口網站直接呼叫執行中的應用程式來提取函式清單，然後對 Kudu 端點進行 HTTP 呼叫。 [ **平臺功能** ] 索引標籤下的平台層級設定仍可使用。

若要驗證您的 App Service 環境設定：
1. 移至 App Service 環境所在子網的 [網路安全性群組] (NSG) 。
1. 驗證輸入規則，以允許來自您正在存取應用程式之電腦的公用 IP 的流量。 
   
您也可以從連接到執行應用程式之虛擬網路的電腦，或在虛擬網路中執行的虛擬機器，使用入口網站。 

如需輸入規則設定的詳細資訊，請參閱 [App Service 環境之網路功能考慮](../app-service/environment/network-info.md#network-security-groups)的「網路安全性群組」一節。

## <a name="next-steps"></a>後續步驟

深入瞭解如何監視您的函數應用程式：

> [!div class="nextstepaction"]
> [監視 Azure Functions](functions-monitoring.md)

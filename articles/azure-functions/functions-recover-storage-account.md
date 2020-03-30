---
title: 故障排除錯誤：無法到達 Azure 函數運行時
description: 了解如何針對無效的儲存體帳戶進行疑難排解。
author: alexkarcher-msft
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: 8fcd0661e2c7cab505121cf0d4d7b4c1d29017f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063776"
---
# <a name="troubleshoot-error-azure-functions-runtime-is-unreachable"></a>故障排除錯誤："無法到達 Azure 函數運行時"

本文可説明您排除 Azure 門戶中顯示的以下錯誤字串：

> 錯誤：無法到達 Azure 函數運行時。 按一下此處瞭解有關存儲配置的詳細資訊。

當 Azure 函數運行時無法啟動時，將出現此問題。 此問題的最常見原因是功能應用無法訪問其存儲帳戶。 有關詳細資訊，請參閱[存儲帳戶要求](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal#storage-account-requirements)。

本文的其餘部分可説明您排除此錯誤的以下原因，包括如何識別和解決每個情況。

## <a name="storage-account-was-deleted"></a>存儲帳戶已刪除

每個函式應用程式都需要有儲存體帳戶才能運作。 如果該帳戶被刪除，您的功能將不起作用。

首先，在應用程式設定中查找存儲帳戶名稱。 或`AzureWebJobsStorage``WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`包含在連接字串中包裝的存儲帳戶的名稱。 有關詳細資訊，請參閱[Azure 函數的應用設置參考](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)。

在 Azure 門戶中搜索存儲帳戶，以查看它是否仍然存在。 如果已刪除，請重新創建存儲帳戶並替換存儲連接字串。 函數代碼丟失，您需要重新部署它。

## <a name="storage-account-application-settings-were-deleted"></a>已刪除存儲帳戶應用程式設定

在上一步中，如果找不到存儲帳戶連接字串，則可能將其刪除或覆蓋。 刪除應用程式設定最常發生在使用部署槽或 Azure 資源管理器腳本設置應用程式設定時。

### <a name="required-application-settings"></a>必要的應用程式設定

* 必要：
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* 消費計畫功能所需的：
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

有關詳細資訊，請參閱[Azure 函數的應用設置參考](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)。

### <a name="guidance"></a>指引

* 不要檢查這些設置中的"插槽設置"。 如果交換部署槽，函數應用將中斷。
* 不要作為自動部署的一部分修改這些設置。
* 在建立時，必須提供這些設定，且設定必須有效。 不包含這些設置的自動部署會導致函數應用無法運行，即使以後添加了這些設置也是如此。

## <a name="storage-account-credentials-are-invalid"></a>存儲帳戶憑據無效

如果重新生成存儲金鑰，則必須更新前面討論的存儲帳戶連接字串。 有關存儲金鑰管理的詳細資訊，請參閱創建[Azure 存儲帳戶](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)。

## <a name="storage-account-is-inaccessible"></a>無法訪問存儲帳戶

您的功能應用必須能夠訪問存儲帳戶。 阻止函數應用訪問存儲帳戶的常見問題包括：

* 功能應用部署到應用服務環境，無需正確的網路規則以允許流量往來和從存儲帳戶。

* 存儲帳戶防火牆已啟用，未配置為允許流量與函數通信。 如需詳細資訊，請參閱[設定 Azure 儲存體防火牆和虛擬網路](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

## <a name="daily-execution-quota-is-full"></a>每日執行配額已滿

如果配置了每日執行配額，則函數應用將暫時禁用，從而導致許多門戶控制項不可用。 

要驗證[Azure 門戶](https://portal.azure.com)中的配額，請在函數應用中選擇**平臺** > **功能應用設置**。 如果您已超出已設置**的每日使用配額**，將顯示以下消息：

  > "功能應用已達到每日使用配額，並一直停止，直到下 24 小時的時間框架。

要解決此問題，請刪除或增加每日配額，然後重新開機應用。 否則，應用的執行將一直阻止到第二天。

## <a name="app-is-behind-a-firewall"></a>應用位於防火牆後面

由於以下任一原因，可能無法到達函數運行時：

* 您的功能應用託管在[內部負載平衡的應用服務環境中](../app-service/environment/create-ilb-ase.md)，它配置為阻止入站互聯網流量。

* 您的功能應用具有已配置為阻止 Internet 訪問的[入站 IP 限制](functions-networking-options.md#inbound-ip-restrictions)。 

Azure 門戶直接調用正在運行的應用以獲取函數清單，並且對 Kudu 終結點進行 HTTP 調用。 **"平臺功能**"選項卡下的平臺級設置仍然可用。

要驗證應用服務環境配置，請進行以下操作：
1. 轉到應用服務環境所在的子網的網路安全性群組 （NSG）。
1. 驗證入站規則，以允許來自您訪問應用程式的電腦的公共 IP 的流量。 
   
還可以使用連接到運行應用的虛擬網路的電腦或虛擬網路中運行的虛擬機器的門戶。 

有關入站規則配置的詳細資訊，請參閱[應用服務環境網路注意事項](https://docs.microsoft.com/azure/app-service/environment/network-info#network-security-groups)的"網路安全性群組"部分。

## <a name="next-steps"></a>後續步驟

瞭解如何監視功能應用：

> [!div class="nextstepaction"]
> [監視 Azure Functions](functions-monitoring.md)

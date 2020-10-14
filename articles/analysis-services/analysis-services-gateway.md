---
title: 適用于 Azure Analysis Services 的內部部署資料閘道 |Microsoft Docs
description: 如果 Azure 中的 Analysis Services 伺服器會連接到內部部署資料來源，則需要一個內部部署閘道。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: fed184c349789dc38f12f62567acc0d0500ca94c
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92016088"
---
# <a name="connecting-to-on-premises-data-sources-with-on-premises-data-gateway"></a>使用內部部署資料閘道連接至內部部署資料來源

內部部署資料閘道在內部部署資料來源和雲端中的 Azure Analysis Services 伺服器之間提供安全的資料傳輸。 除了在相同區域中使用多個 Azure Analysis Services 伺服器之外，最新版的閘道也適用于 Azure Logic Apps、Power BI、Power Apps 和 Power Automate。 雖然您安裝的閘道在這些服務中都是相同的，但 Azure Analysis Services 和 Logic Apps 有一些額外的步驟。

此處提供的資訊僅適用于 Azure Analysis Services 如何搭配內部部署資料閘道運作。 若要深入瞭解閘道的一般功能，以及它如何與其他服務搭配運作，請參閱 [什麼是內部部署資料閘道？](/data-integration/gateway/service-gateway-onprem)。

針對 Azure Analysis Services，第一次透過閘道進行設定是四個部分的進程：

- **下載並執行安裝程式** - 這個步驟會在您組織中的電腦上安裝閘道服務。 您也會使用[租用戶](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) Azure AD 中的帳戶來登入 Azure。 不支援 Azure B2B (來賓) 帳戶。

- **註冊您的閘道** - 在此步驟中，您會為您的閘道指定名稱和復原金鑰，然後選取區域，並且向閘道雲端服務註冊您的閘道。 您的閘道資源可以在任何區域中註冊，但建議與您的 Analysis Services 伺服器位於相同的區域。 

- 在**azure 中建立閘道資源**-在此步驟中，您會在 azure 中建立閘道資源。

- 將**閘道資源連接到伺服器**-一旦擁有閘道資源之後，您就可以開始將伺服器連接到該資源。 您可以將多部伺服器和其他資源連接在相同的區域中。



## <a name="how-it-works"></a>運作方式
您在組織的電腦上安裝的閘道會以 Windows 服務 (**內部部署資料閘道**) 的形式執行。 此本機服務已透過 Azure 服務匯流排向閘道雲端服務註冊。 然後，您會建立 Azure 訂用帳戶的內部部署資料閘道資源。 然後，您的 Azure Analysis Services 伺服器會連線到您的 Azure 閘道資源。 當您伺服器上的模型需要連線到內部部署資料來源進行查詢或處理時，查詢和資料流程會周遊閘道資源、Azure 服務匯流排、本機內部部署資料閘道服務以及您的資料來源。 

![運作方式](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

查詢和資料流程：

1. 雲端服務使用內部部署資料來源的加密認證建立查詢。 查詢接著傳送至佇列供閘道處理。
2. 閘道雲端服務會分析該查詢，並將要求推送至 [Azure 服務匯流排](https://azure.microsoft.com/documentation/services/service-bus/)。
3. 內部部署資料閘道會輪詢 Azure 服務匯流排是否有待處理的要求。
4. 閘道收到查詢、解密認證，並使用這些認證連接至資料來源。
5. 閘道將查詢傳送至資料來源執行。
6. 結果會從資料來源傳送回閘道，然後再到雲端服務和您的伺服器。

## <a name="installing"></a>安裝

安裝 Azure Analysis Services 環境時，請務必遵循 [安裝和設定適用于 azure 的內部部署資料閘道 Analysis Services](analysis-services-gateway-install.md)中所述的步驟。 本文是 Azure Analysis Services 特有的。 它包含在 Azure 中設定內部部署資料閘道資源所需的額外步驟，並將您的 Azure Analysis Services server 連線至資源。

## <a name="connecting-to-a-gateway-resource-in-a-different-subscription"></a>連接至不同訂用帳戶中的閘道資源

建議您在與伺服器相同的訂用帳戶中建立 Azure 閘道資源。 不過，您可以設定您的伺服器，以連接到另一個訂用帳戶中的閘道資源。 設定現有的伺服器設定或在入口網站中建立新的伺服器時，不支援連接到另一個訂用帳戶中的閘道資源，但可使用 PowerShell 進行設定。 若要深入瞭解，請參閱 [將閘道資源連線到伺服器](analysis-services-gateway-install.md#connect-gateway-resource-to-server)。

## <a name="ports-and-communication-settings"></a>埠和通訊設定

閘道會建立 Azure 服務匯流排的輸出連接。 閘道會與下列輸出連接埠進行通訊︰TCP 443 (預設)、5671、5672、9350 到 9354。  閘道不需要輸入連接埠。

您可能需要在防火牆中包含資料區域的 IP 位址。 您可以下載 [Microsoft Azure Datacenter IP 清單](https://www.microsoft.com/download/details.aspx?id=56519)。 此清單每週更新。 Azure Datacenter IP 清單中列出的 IP 位址採用 CIDR 標記法。 若要深入了解，請參閱[無類別網域間路由選擇](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)。

以下是閘道所使用的完整功能變數名稱。

| 網域名稱 | 輸出連接埠 | 描述 |
| --- | --- | --- |
| *.powerbi.com |80 |用於下載安裝程式的 HTTP。 |
| *.powerbi.com |443 |HTTPS |
| *.analysis.windows.net |443 |HTTPS |
| *. login.windows.net、login.live.com、aadcdn.msauth.net |443 |HTTPS |
| *.servicebus.windows.net |5671-5672 |進階訊息佇列通訊協定 (AMQP) |
| *.servicebus.windows.net |443、9350-9354 |透過 TCP 之服務匯流排轉送上的接聽程式 (需要 443 才能取得「存取控制」權杖) |
| *.frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *.msftncsi.com |443 |如果 Power BI 服務無法連接至閘道，則用來測試網際網路連線能力。 |
| *.microsoftonline-p.com |443 |用於驗證 (視設定而定)。 |
| dc.services.visualstudio.com    |443 |供 AppInsights 用來收集遙測。 |

### <a name="forcing-https-communication-with-azure-service-bus"></a>強制使用 Azure 服務匯流排進行 HTTPS 通訊

您可以強制閘道使用 HTTPS 取代直接 TCP 來與 Azure 服務匯流排通訊；但這樣會大幅降低效能。 您可以修改 *Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config* 檔案，方法是將值從 `AutoDetect` 變更為 `Https`。 這個檔案通常位於 *C:\Program Files\On-premises data gateway*。

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```

## <a name="next-steps"></a>後續步驟 

下列文章包含在內部部署資料閘道的一般內容中，適用于閘道支援的所有服務：

* [內部部署資料閘道常見問題集](/data-integration/gateway/service-gateway-onprem-faq)   
* [使用內部部署資料閘道應用程式](/data-integration/gateway/service-gateway-app)   
* [租用戶層級管理](/data-integration/gateway/service-gateway-tenant-level-admin)
* [設定 proxy 設定](/data-integration/gateway/service-gateway-proxy)   
* [調整通訊設定](/data-integration/gateway/service-gateway-communication)   
* [設定記錄檔](/data-integration/gateway/service-gateway-log-files)   
* [疑難排解](/data-integration/gateway/service-gateway-tshoot)
* [監視及最佳化閘道效能](/data-integration/gateway/service-gateway-performance) \(部分機器翻譯\)
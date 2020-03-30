---
title: 用於 Azure 分析服務的本地資料閘道 |微軟文檔
description: 如果 Azure 中的 Analysis Services 伺服器會連接到內部部署資料來源，則需要一個內部部署閘道。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 648646b6f973762245c344cd2629a874a219b170
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310147"
---
# <a name="connecting-to-on-premises-data-sources-with-on-premises-data-gateway"></a>使用本地資料閘道連接到本地資料來源

內部部署資料閘道在內部部署資料來源和雲端中的 Azure Analysis Services 伺服器之間提供安全的資料傳輸。 除了在同一區域中處理多個 Azure 分析服務伺服器外，最新版本的閘道還適用于 Azure 邏輯應用、Power BI、電源應用和電源自動功能。 雖然您安裝的閘道在所有這些服務中都是相同的，但 Azure 分析服務和邏輯應用還有一些其他步驟。

此處提供的資訊特定于 Azure 分析服務如何與本地資料閘道一起工作。 要瞭解有關閘道一般及其與其他服務的工作方式的更多內容，請參閱[什麼是本地資料閘道？](/data-integration/gateway/service-gateway-onprem)

對於 Azure 分析服務，首次使用閘道進行設置是一個四部分的過程：

- **下載並執行安裝程式** - 這個步驟會在您組織中的電腦上安裝閘道服務。 您也會使用[租用戶](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) Azure AD 中的帳戶來登入 Azure。 不支援 Azure B2B (來賓) 帳戶。

- **註冊您的閘道** - 在此步驟中，您會為您的閘道指定名稱和復原金鑰，然後選取區域，並且向閘道雲端服務註冊您的閘道。 您的閘道資源可以在任何區域註冊，但建議它位於與分析服務伺服器相同的區域。 

- **在 Azure 中創建閘道資源**- 在此步驟中，在 Azure 中創建閘道資源。

- **將伺服器連接到閘道資源**- 擁有閘道資源後，即可開始將伺服器連接到它。 您可以連接多個伺服器和其他資源，前提是它們位於同一區域。



## <a name="how-it-works"></a><a name="how-it-works"> </a>工作原理
您在組織的電腦上安裝的閘道會以 Windows 服務 (**內部部署資料閘道**) 的形式執行。 此本機服務已透過 Azure 服務匯流排向閘道雲端服務註冊。 然後，為 Azure 訂閱創建本地資料閘道資源。 然後，Azure 分析服務伺服器將連接到 Azure 閘道資源。 當您伺服器上的模型需要連線到內部部署資料來源進行查詢或處理時，查詢和資料流程會周遊閘道資源、Azure 服務匯流排、本機內部部署資料閘道服務以及您的資料來源。 

![運作方式](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

查詢和資料流程：

1. 雲端服務使用內部部署資料來源的加密認證建立查詢。 查詢接著傳送至佇列供閘道處理。
2. 閘道雲端服務會分析該查詢，並將要求推送至 [Azure 服務匯流排](https://azure.microsoft.com/documentation/services/service-bus/)。
3. 內部部署資料閘道會輪詢 Azure 服務匯流排是否有待處理的要求。
4. 閘道收到查詢、解密認證，並使用這些認證連接至資料來源。
5. 閘道將查詢傳送至資料來源執行。
6. 結果會從資料來源傳送回閘道，然後再到雲端服務和您的伺服器。

## <a name="installing"></a>安裝

為 Azure 分析服務環境安裝時，請務必按照[Azure 分析服務的安裝和配置本地資料閘道](analysis-services-gateway-install.md)中描述的步驟操作。 本文特定于 Azure 分析服務。 它包括在 Azure 中設置本地資料閘道資源並將 Azure 分析服務伺服器連接到該資源所需的其他步驟。

## <a name="ports-and-communication-settings"></a>埠和通訊設定

閘道會建立 Azure 服務匯流排的輸出連接。 閘道會與下列輸出連接埠進行通訊︰TCP 443 (預設)、5671、5672、9350 到 9354。  閘道不需要輸入連接埠。

您可能需要在防火牆中包含資料區域的 IP 位址。 您可以下載 [Microsoft Azure Datacenter IP 清單](https://www.microsoft.com/download/details.aspx?id=56519)。 此清單每週更新。 Azure Datacenter IP 清單中列出的 IP 位址採用 CIDR 標記法。 若要深入了解，請參閱[無類別網域間路由選擇](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)。

以下是閘道使用的完全限定的功能變數名稱。

| 網域名稱 | 輸出連接埠 | 描述 |
| --- | --- | --- |
| *.powerbi.com |80 |用於下載安裝程式的 HTTP。 |
| *.powerbi.com |443 |HTTPS |
| *.analysis.windows.net |443 |HTTPS |
| *.login.windows.net，login.live.com，aadcdn.msauth.net |443 |HTTPS |
| *.servicebus.windows.net |5671-5672 |進階訊息佇列通訊協定 (AMQP) |
| *.servicebus.windows.net |443、9350-9354 |透過 TCP 之服務匯流排轉送上的接聽程式 (需要 443 才能取得「存取控制」權杖) |
| *.frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *.msftncsi.com |443 |如果 Power BI 服務無法連接至閘道，則用來測試網際網路連線能力。 |
| *.microsoftonline-p.com |443 |用於驗證 (視設定而定)。 |
| dc.services.visualstudio.com  |443 |AppInsights 用於收集遙測資料。 |

### <a name="forcing-https-communication-with-azure-service-bus"></a><a name="force-https"></a>強制使用 Azure 服務匯流排進行 HTTPS 通訊

您可以強制閘道使用 HTTPS 取代直接 TCP 來與 Azure 服務匯流排通訊；但這樣會大幅降低效能。 您可以修改 *Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config* 檔案，方法是將值從 `AutoDetect` 變更為 `Https`。 這個檔案通常位於 *C:\Program Files\On-premises data gateway*。

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```

## <a name="next-steps"></a>後續步驟 

以下文章包含在適用于閘道支援的所有服務的本地資料閘道一般內容中：

* [本地資料閘道常見問題解答](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem-faq)   
* [使用內部部署資料閘道應用程式](https://docs.microsoft.com/data-integration/gateway/service-gateway-app)   
* [租用戶層級管理](https://docs.microsoft.com/data-integration/gateway/service-gateway-tenant-level-admin)
* [配置代理設置](https://docs.microsoft.com/data-integration/gateway/service-gateway-proxy)   
* [調整通訊設定](https://docs.microsoft.com/data-integration/gateway/service-gateway-communication)   
* [配置日誌檔](https://docs.microsoft.com/data-integration/gateway/service-gateway-log-files)   
* [疑難排解](https://docs.microsoft.com/data-integration/gateway/service-gateway-tshoot)
* [監視及調整閘道效能](https://docs.microsoft.com/data-integration/gateway/service-gateway-performance)

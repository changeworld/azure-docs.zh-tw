---
title: 與應用程式閘道整合
description: 瞭解如何在此端到端演練中將ILB應用服務環境中的應用與應用程式閘道整合。
author: ccompy
ms.assetid: a6a74f17-bb57-40dd-8113-a20b50ba3050
ms.topic: article
ms.date: 03/03/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: e4838597c50898748eb4b33e81ff22eaeea37b30
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476902"
---
# <a name="integrate-your-ilb-app-service-environment-with-the-azure-application-gateway"></a>整合 ILB App Service Environment 與 Azure 應用程式閘道 #

[App Service Environment](./intro.md) 是在客戶的 Azure 虛擬網路子網路中進行的 Azure App Service 部署。 可使用應用程式存取的公用或私用端點進行部署。 使用私用端點 (也就是內部負載平衡器) 進行的 App Service Environment 部署稱為 ILB App Service Environment。  

Web 應用程式防火牆會檢查輸入的 Web 流量以封鎖 SQL 插入、跨網站指令碼、惡意程式碼上傳和應用程式 DDoS 以及其他攻擊，藉此保護您的 Web 應用程式。 它也會針對資料外洩防護 (DLP) 檢查來自後端 Web 伺服器的回應。 您可以從 Azure Marketplace 取得 WAF 裝置，或者您可以使用 [Azure 應用程式閘道][appgw]。

Azure 應用程式閘道是一個虛擬裝置,提供第 7 層負載平衡、TLS/SSL 卸載和 Web 應用程式防火牆 (WAF) 保護。 它可以接聽公用 IP 位址，並將流量路由到您的應用程式端點。 下列資訊說明如何將已設定 WAF 的應用程式閘道與 ILB App Service Environment 中的應用程式進行整合。  

應用程式閘道與 ILB App Service Environment 的整合是在應用程式層級進行的。 當您使用 ILB App Service Environment 設定應用程式閘道時，請針對 ILB App Service Environment 中的特定應用程式進行此操作。 此技巧可讓您在單一 ILB App Service Environment 中裝載安全的多租用戶應用程式。  

![指向 ILB App Service Environment 上之應用程式的應用程式閘道][1]

在本逐步解說中，您將：

* 建立 Azure 應用程式閘道。
* 將應用程式閘道設定為指向 ILB App Service Environment 中的應用程式。
* 將應用程式設定為採用自訂網域名稱。
* 編輯指向應用程式閘道的公用 DNS 主機名稱。

## <a name="prerequisites"></a>Prerequisites

為了整合應用程式閘道與 ILB App Service Environment，您需要：

* ILB App Service Environment。
* 在 ILB App Service Environment 中執行的應用程式。
* 可在網際網路路由傳送的網域名稱，以便搭配 ILB App Service Environment 中的應用程式使用。
* ILB App Service Environment 使用的 ILB 位址。 此資訊位於 **「設定** > **IP 位址**」 下的「應用服務環境」門戶中:

    ![ILB App Service Environment 所使用之 IP 位址清單的範例][9]
    
* 稍後用來指向應用程式閘道的公用 DNS 名稱。 

如需如何建立 ILB App Service Environment 的詳細資訊，請參閱[建立和使用 ILB App Service Environment][ilbase]。

本文假設您要讓應用程式閘道位於 App Service Environment 部署所在的同一個 Azure 虛擬網路中。 開始建立應用程式閘道之前，請挑選或建立將用來裝載閘道的子網路。 

請勿使用名稱為 GatewaySubnet 的子網路。 如果您將應用程式閘道放在 GatewaySubnet，稍後就無法建立虛擬網路閘道。 

另外，也不能將閘道放在 ILB App Service Environment 所使用的子網路中。 App Service Environment 是唯一可位於這個子網路的物件。

## <a name="configuration-steps"></a>組態步驟 ##

1. 在 Azure 門戶中,轉到**新** > **網路** > **應用程式閘道**。

2. 在 [基本資料]**** 區域中：

   a. 於 [名稱]**** 中輸入應用程式閘道的名稱。

   b. 於 [階層]**** 中選取 [WAF]****。

   c. 於 [訂用帳戶]**** 中選取 App Service Environment 虛擬網路所使用的同一個訂用帳戶。

   d. 於 [資源群組]**** 中建立或選取資源群組。

   e. 於 [位置]**** 中選取 App Service Environment 虛擬網路的位置。

   ![新應用程式閘道的建立基本概念][2]

3. 在 [設定]**** 區設中：

   a. 於 [虛擬網路]**** 中選取 App Service Environment 虛擬網路。

   b. 於 [子網路]**** 中選取應用程式閘道必須部署於其中的子網路。 請勿使用 GatewaySubnet，因為它會讓您無法建立 VPN 閘道。

   c. 於 [IP 位址類型]**** 中選取 [公用]****。

   d. 於 [公用 IP 位址]**** 中選取公用 IP 位址。 如果您沒有公用 IP 位址，請立即建立一個。

   e. 於 [通訊協定]**** 中選取 [HTTP]**** 或 [HTTPS]****。 如果您要設定為 HTTPS，則需要提供 PFX 憑證。

   f. 對於 [Web 應用程式防火牆]****，您可以啟用防火牆，並視需要將它設定為 [偵測]**** 或 [預防]****。

   ![新應用程式閘道的建立設定][3]
    
4. 在 [摘要]**** 區段中檢閱設定，然後選取 [確定]****。 應用程式閘道可能需要 30 多分鐘才能完成設定。  

5. 在應用程式閘道完成設定後，請移至應用程式閘道入口網站。 選取 [後端集區]****。 新增 ILB App Service Environment 的 ILB 位址。

   ![設定後端集區][4]

6. 後端集區的設定程序完成後，選取 [健康情況探查]****。 針對要用於應用程式的網域名稱建立健康情況探查。 

   ![設定健全狀態探查][5]
    
7. 健康情況探查的設定程序完成後，選取 [HTTP 設定]****。 編輯現有設定、選取 [使用自訂探查]****，然後挑選您設定的探查。

   ![進行 HTTP 設定][6]
    
8. 移至應用程式閘道的 [概觀]**** 區段，然後複製應用程式閘道所使用的公用 IP 位址。 將該 IP 位址設定為應用程式網域名稱的 A 記錄，或在 CNAME 記錄中使用該位址的 DNS 名稱。 相較於從應用程式閘道 [概觀]**** 區段中的連結複製位址，從公用 IP 位址的 UI 選取公用 IP 位址並加以複製會比較容易。 

   ![應用程式閘道入口網站][7]

9. 在 ILB App Service Environment 中設定應用程式的自訂網域名稱。 轉到門戶中的應用,並在 **「設定」** 下選擇 **「自訂域**」。

   ![在應用程式上設定自訂網域名稱][8]

[設定 Web 應用程式的自訂網域名稱][custom-domain]一文中，有為 Web 應用程式設定自訂網域名稱的相關資訊。 但對於 ILB App Service Environment 中的應用程式，則不會驗證網域名稱。 因為您擁有用來管理應用程式端點的 DNS，因此可以在那裡放置任何內容。 在此情況下新增的自訂網域名稱不需要位於您的 DNS，但仍必須設定於應用程式中。 

設定完成並等候一小段時間讓 DNS 變更傳播之後，您就可以使用您建立的自訂網域名稱存取應用程式。 


<!--IMAGES-->
[1]: ./media/integrate-with-application-gateway/appgw-highlevel.png
[2]: ./media/integrate-with-application-gateway/appgw-createbasics.png
[3]: ./media/integrate-with-application-gateway/appgw-createsettings.png
[4]: ./media/integrate-with-application-gateway/appgw-backendpool.png
[5]: ./media/integrate-with-application-gateway/appgw-healthprobe.png
[6]: ./media/integrate-with-application-gateway/appgw-httpsettings.png
[7]: ./media/integrate-with-application-gateway/appgw-publicip.png
[8]: ./media/integrate-with-application-gateway/appgw-customdomainname.png
[9]: ./media/integrate-with-application-gateway/appgw-iplist.png

<!--LINKS-->
[appgw]: https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction
[custom-domain]: ../app-service-web-tutorial-custom-domain.md
[ilbase]: ./create-ilb-ase.md

---
title: App Service 環境網路
description: App Service 環境網路詳細資料
author: ccompy
ms.assetid: 6f262f63-aef5-4598-88d2-2f2c2f2bfc24
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 680b1f3b6af186eba27a4dd926016a04cd863760
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98013478"
---
# <a name="app-service-environment-networking"></a>App Service 環境網路

> [!NOTE]
> 本文說明 App Service 環境 v3 (preview) 
> 

App Service 環境 (ASE) 是裝載 web 應用程式、api 應用程式和函式應用程式的 Azure App Service 單一租使用者部署。 當您安裝 ASE 時，您會挑選您要在其中部署 (VNet) 的 Azure 虛擬網路。 所有輸入和輸出流量應用程式都會在您指定的 VNet 內。  

ASEv3 使用兩個子網。  一個子網用於處理輸入流量的私人端點。 這可以是既有的子網，也可以是新的子網。  輸入子網可用於與私人端點不同的目的。 輸出子網只能用於來自 ASE 的輸出流量。 ASE 輸出子網中沒有任何其他專案可進入。

## <a name="addresses"></a>位址 
ASE 在建立時有下列位址：

| 網址類別型 | description |
|--------------|-------------|
| 輸入位址 | 輸入位址是 ASE 所使用的私人端點位址。 |
| 輸出子網 | 輸出子網也是 ASE 子網。 在預覽期間，此子網只會用於輸出流量。 |
| Windows 輸出位址 | 在對網際網路進行輸出呼叫時，此 ASE 中的 Windows 應用程式預設會使用此位址。 |
| Linux 輸出位址 | 此 ASE 中的 Linux 應用程式預設會在對網際網路進行輸出呼叫時，使用此位址。 |

ASEv3 會詳細說明 ase 入口網站的 **IP 位址** 部分中 ase 所使用的位址。

![ASE 位址 UI](./media/networking/networking-ip-addresses.png)

如果您刪除 ASE 所使用的私人端點，您將無法連線到 ASE 中的應用程式。  

ASE 會使用輸出子網中的位址來支援 ASE 所使用的基礎結構。 當您在 ASE 中調整 App Service 方案時，您將會使用更多位址。 ASE 中的應用程式在輸出子網中沒有專用位址。 應用程式在輸出子網中的應用程式所使用的位址，會隨著時間而變更。

## <a name="ports"></a>連接埠

ASE 會接收埠80和443上的應用程式流量。  ASE 沒有任何輸入或輸出埠需求。 

## <a name="extra-configurations"></a>額外設定

與 ASEv2 不同的是，透過 ASEv3，您可以將網路安全性群組設定 (Nsg) 和路由表 (Udr) ，如您所見而不受限制。 如果您想要將來自 ASE 的所有輸出流量強制通道傳送至 NVA 裝置。 您可以將 WAF 裝置放在 ASE 的所有輸入流量前面。 

## <a name="dns"></a>DNS

您 ASE 中的應用程式將會使用您的 VNet 設定所在的 DNS。 遵循 [使用 App Service 環境](https://docs.microsoft.com/azure/app-service/environment/using#dns-configuration) 中的指示，將您的 DNS 伺服器設定為指向您的 ASE。 如果您希望某些應用程式使用不同的 DNS 伺服器，而不是使用您的 VNet 設定，您可以使用應用程式設定 WEBSITE_DNS_SERVER 和 WEBSITE_DNS_ALT_SERVER，以手動方式在每個應用程式上設定它。 應用程式設定 WEBSITE_DNS_ALT_SERVER 設定次要 DNS 伺服器。 只有當主要 DNS 伺服器沒有回應時，才會使用次要 DNS 伺服器。 

## <a name="preview-limitation"></a>預覽限制

ASEv3 中有幾個無法使用的網路功能。  ASEv3 中無法使用的專案包括：

• FTP •遠端 debug •外部負載平衡器部署•能夠存取容器部署的私人容器登錄•能夠呼叫全域對等互連的 Vnet •能夠以服務端點或私人端點保護的儲存體進行備份/還原帳戶•能夠讓應用程式設定 keyvault 服務端點或私人端點安全 keyvault 帳戶上的參考•能夠將 BYOS 用於服務端點或私人端點安全的儲存體帳戶•使用網路監看員或連出流量的 NSG 流程
    
    


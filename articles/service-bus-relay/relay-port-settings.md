---
title: Azure 轉送連接埠設定 | Microsoft Docs
description: 本文包含一個表格，說明 Azure 轉送的埠值所需的設定。
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 532e3c297c42ea16b1f462a01699fc2fd71c6cce
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80529118"
---
# <a name="azure-relay-port-settings"></a>Azure 轉送連接埠設定

下表說明 Azure 轉送的連接埠值所需的設定。

## <a name="hybrid-connections"></a>混合式連線

混合式連線會在埠443上使用 Websocket，並以 TLS 作為基礎傳輸機制，這只會使用**HTTPS** 。 

## <a name="wcf-relays"></a>WCF 轉送
  
|繫結|傳輸安全性|連接埠|  
|-------------|------------------------|----------|  
|[BasicHttpRelayBinding 類別](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (用戶端)|是|HTTPS| 
|" |否|HTTP|  
|[BasicHttpRelayBinding 類別](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (服務)|之前或之後|9351/HTTP|  
|[NetEventRelayBinding 類別](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (用戶端)|是|9351/HTTPS|  
|" |否|9350/HTTP|  
|[NetEventRelayBinding 類別](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (服務)|之前或之後|9351/HTTP|  
|[NetTcpRelayBinding 類別](/dotnet/api/microsoft.servicebus.nettcprelaybinding) (用戶端/服務)|之前或之後|5671/9352/HTTP (如果使用混合式則為 9352/9353)|  
|[NetOnewayRelayBinding 類別](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (用戶端)|是|9351/HTTPS|  
|" |否|9350/HTTP|  
|[NetOnewayRelayBinding 類別](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (服務)|之前或之後|9351/HTTP|  
|[WebHttpRelayBinding 類別](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (用戶端)|是|HTTPS|  
|" |否|HTTP|  
|[WebHttpRelayBinding 類別](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (服務)|之前或之後|9351/HTTP|  
|[WS2007HttpRelayBinding 類別](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (用戶端)|是|HTTPS|  
|" |否|HTTP|  
|[WS2007HttpRelayBinding 類別](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (服務)|之前或之後|9351/HTTP|

## <a name="next-steps"></a>後續步驟
若要深入了解 Azure 轉送，請造訪下列連結：
* [什麼是 Azure 轉送？](relay-what-is-it.md)
* [轉送常見問題集](relay-faq.md)
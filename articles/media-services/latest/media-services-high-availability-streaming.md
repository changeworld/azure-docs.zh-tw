---
title: Azure 媒體服務高可用性串流
description: 瞭解發生區域資料中心中斷或失敗時，如何容錯移轉至次要媒體服務帳戶。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: 1492dd392eabc4331f8e3d4604fb245a89dedff5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "78899226"
---
# <a name="media-services-high-availability-streaming"></a>媒體服務高可用性串流

如果基礎元件或相依服務的區域資料中心中斷或失敗，Azure 媒體服務目前不會提供服務的立即容錯移轉。 本文提供如何建立隨選影片跨區域串流的指引。

## <a name="prerequisites"></a>必要條件

回顧[如何建立跨區域編碼系統](media-services-high-availability-encoding.md)

## <a name="how-to-build-video-on-demand-cross-region-streaming"></a>如何建立隨選影片跨區域串流 

* 隨選影片跨區域串流牽涉到複製[資產](assets-concept.md)、[內容金鑰原則](content-key-policy-concept.md)（如果使用）、[串流原則](streaming-policy-concept.md)和[串流定位器](streaming-locators-concept.md)。 
* 您必須在這兩個區域中建立原則，並將其保持在最新狀態。 
* 當您建立串流定位器時，您會想要使用相同的定位器識別碼值、ContentKey 識別碼值和 ContentKey 值。  
* 如果您要將內容編碼，建議您將區域 A 中的內容編碼並加以發佈，然後將編碼的內容複寫到區域 B，並使用與區域 A 相同的值加以發行。
* 您可以在來源和金鑰傳遞服務的主機名稱上使用流量管理員（在媒體服務設定中，這看起來就像自訂金鑰伺服器 URL）。

## <a name="next-steps"></a>後續步驟

退房：

* [教學課程：根據 URL 編碼遠端檔案並串流影片](stream-files-dotnet-quickstart.md)
* [程式碼範例](https://docs.microsoft.com/samples/browse/?products=azure-media-services)

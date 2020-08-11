---
title: 適用于 Azure 網路監看員的資料存放區 |Microsoft Docs
description: 瞭解網路監看員服務的資料常駐
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/20/2020
ms.author: damendo
ms.openlocfilehash: 14b4d3568c129c77260b00d554db520809dfd670
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88068176"
---
# <a name="data-residency-for-the-azure-network-watcher"></a>適用于 Azure 網路監看員的資料存放區
除了連線監視器 (Preview) 服務以外，Azure 網路監看員不會儲存客戶資料。


## <a name="connection-monitor-preview-data-residency"></a>連接監視器 (預覽) 資料存放區
*連接監視器 (預覽) *服務會儲存客戶資料。 這項資料會由網路監看員自動儲存在單一區域中。 因此，連線*監視器 (預覽) *會自動滿足區域內資料的常駐需求，包括如[信任中心](https://azuredatacentermap.azurewebsites.net/)所指定。

## <a name="singapore-data-residency"></a>新加坡資料常駐

在 Azure 中，啟用將客戶資料儲存在單一區域的功能目前僅適用于東南亞地區， (新加坡) 的亞太地區地區。 針對所有其他區域，客戶資料會儲存在異地。 如需詳細資訊，請參閱[信任中心](https://azuredatacentermap.azurewebsites.net/)。

> [!Note]
> **先前**的複寫客戶可以選擇使用其網路監看員實例來儲存使用者的 IP 位址，讓網路監看員能夠監視與使用者 IP 位址相關的連線能力、延遲和網路拓撲變更。 這些使用者 IP 位址可能會分類為客戶資料。 從2020年7月15日起，網路監看員將此資料儲存在單一區域中。  (的客戶資料已不再複寫到 HK。 ) 此資料不再複寫到 HK。 此客戶資料為，並已在待用時加密。
> 
> 如果此客戶資料是由協力廠商存取，則會允許協力廠商知道 IP 位址，但不會授與協力廠商存取與 IP 位址相關聯的電腦或裝置。 網路監看員認為沒有協力廠商以 HK 的方式存取此客戶資料。

## <a name="next-steps"></a>後續步驟

* 閱讀[網路監看員](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)的總覽。

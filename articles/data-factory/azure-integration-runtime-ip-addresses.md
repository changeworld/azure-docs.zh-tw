---
title: Azure Integration Runtime IP 位址
description: 瞭解必須允許入站流量的 IP 位址，以便正確配置防火牆以保護對資料存儲的網路訪問。
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/06/2020
ms.openlocfilehash: 598876e12fe04129692d0c9a842f4edb2ec00768
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086801"
---
# <a name="azure-integration-runtime-ip-addresses"></a>Azure Integration Runtime IP 位址

Azure 集成運行時使用的 IP 位址取決於 Azure 集成運行時所在的區域。 *全部*位於同一區域中的 Azure 集成運行時使用相同的 IP 位址範圍。

> [!IMPORTANT]  
> 資料流程當前不使用這些 IP。 
>
> 您可以使用這些 IP 範圍執行資料移動、管道和外部活動。 這些 IP 範圍可用於在資料存儲/網路安全性群組 （NSG）/ 防火牆中白名單，以便從 Azure 集成運行時進行入站訪問。 

## <a name="azure-integration-runtime-ip-addresses-specific-regions"></a>Azure 集成運行時 IP 位址：特定區域

允許來自資源所在的特定 Azure 區域中為 Azure 集成運行時列出的 IP 位址的流量：

|                | 區域              | IP 位址                                                 |
| -------------- | ------------------- | ------------------------------------------------------------ |
| Asia           | 東亞           | 20.189.104.128/25, </br>20.189.106.0/26, </br>13.75.39.112/28 |
| &nbsp;         | 東南亞      | 20.43.128.128/25, </br>20.43.130.0/26, </br>40.78.236.176/28 |
| 澳大利亞      | 澳大利亞東部      | 20.37.193.0/25,</br>20.37.193.128/26,</br>13.70.74.144/28    |
| &nbsp;         | 澳大利亞東南部 | 20.42.225.0/25,</br>20.42.225.128/26,</br>13.77.53.160/28    |
| 巴西         | 巴西南部        | 191.235.224.128/25,</br>191.235.225.0/26,</br>191.233.205.160/28 |
| Canada         | 加拿大中部      | 52.228.80.128/25,</br>52.228.81.0/26,</br>13.71.175.80/28    |
| 中國          | 中國東部 2        | 40.73.172.48/28,</br>52.130.0.128/25,</br>52.130.1.0/26      |
| 歐洲         | 北歐        | 20.38.82.0/23,</br>20.38.80.192/26,</br>13.69.230.96/28      |
| &nbsp;         | 西歐         | 40.74.26.0/23,</br>40.74.24.192/26,</br>13.69.67.192/28      |
| 法國         | 法國中部      | 20.43.40.128/25,</br>20.43.41.0/26,</br>40.79.132.112/28     |
| 印度          | 印度中部       | 52.140.104.128/25,</br>52.140.105.0/26,</br>20.43.121.48/28  |
| 日本          | 日本東部          | 20.43.64.128/25,</br>20.43.65.0/26,</br>13.78.109.192/28     |
| 南韓          | 南韓中部       | 20.41.64.128/25,</br>20.41.65.0/26,</br>52.231.20.64/28      |
| 南非   | 南非北部  | 102.133.124.104/29,</br>102.133.216.128/25,</br>102.133.217.0/26 |
| United Kingdom | 英國南部            | 51.104.24.128/25,</br>51.104.25.0/26,</br>51.104.9.32/28     |
| 美國  | 美國中部          | 20.37.154.0/23,</br>20.37.156.0/26,</br>20.44.10.64/28       |
|                | 美國東部             | 20.42.2.0/23,</br>20.42.4.0/26,</br>40.71.14.32/28           |
|                | 美國東部 2            | 20.41.2.0/23,</br>20.41.4.0/26,</br>20.44.17.80/28           |
|                | 美國東部 2 EUAP      | 20.39.8.128/26,</br>20.39.8.96/27,</br>40.75.35.144/28       |
|                | 美國中北部    | 40.80.185.0/24,</br>40.80.186.0/25,</br>52.162.111.48/28      |
|                | 美國中南部    | 40.119.9.0/25,</br>40.119.9.128/26,</br>13.73.244.32/28      |
|                | 美國中西部     | 52.150.137.128/25,</br>52.150.136.192/26,</br>13.71.199.0/28 |
|                | 美國西部             | 40.82.250.0/23,</br>40.82.249.64/26,</br>13.86.219.208/28    |
|                | 美國西部 2            | 20.42.132.0/23,</br>20.42.129.64/26,</br>13.66.143.128/28    |
|                | US Gov 維吉尼亞州     | 52.127.45.96/28,</br>52.127.48.128/25,</br>52.127.49.0/26    |

## <a name="known-issue-with-azure-storage"></a>Azure 存儲的已知問題

* 連接到 Azure 存儲帳戶時，IP 網路規則對來自與存儲帳戶位於同一區域的 Azure 集成運行時的請求沒有任何影響。 有關詳細資訊，[請參閱本文](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range)。 

  相反，我們建議[在連接到 Azure 存儲 時使用受信任的服務](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993)。 

## <a name="next-steps"></a>後續步驟

* [在 Azure Data Factory 中資料移動的安全性考量](data-movement-security-considerations.md)

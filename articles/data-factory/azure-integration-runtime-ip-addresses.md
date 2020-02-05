---
title: Azure Integration Runtime IP 位址
description: 瞭解您必須允許輸入流量的 IP 位址，以便適當地設定防火牆來保護資料存放區的網路存取。
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
ms.openlocfilehash: d64475ce524a3e859ed68a46552fedf30068f71d
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514777"
---
# <a name="azure-integration-runtime-ip-addresses"></a>Azure Integration Runtime IP 位址

Azure Integration Runtime 使用的 IP 位址取決於您的 Azure Integration Runtime 所在的區域。 *全部*位於相同區域中的 Azure 整合執行時間會使用相同的 IP 位址範圍。

> [!IMPORTANT]  
> 資料流程目前不會使用這些 Ip。 
>
> 您可以使用這些 IP 範圍來進行資料移動、管線和外部活動執行。 這些 IP 範圍可以用於資料存放區/網路安全性群組（NSG）/防火牆中的允許清單，以從 Azure Integration runtime 進行輸入存取。 

## <a name="azure-integration-runtime-ip-addresses-specific-regions"></a>Azure Integration Runtime IP 位址：特定區域

允許在資源所在的特定 Azure 區域中，針對 Azure 整合執行時間列出的 IP 位址進行流量：

|                | 地區              | IP 位址                                                 |
| -------------- | ------------------- | ------------------------------------------------------------ |
| 亞洲           | 東亞           | 20.189.104.128/25、 </br>20.189.106.0/26、 </br>13.75.39.112/28 |
| &nbsp;         | 東南亞      | 20.43.128.128/25、 </br>20.43.130.0/26、 </br>40.78.236.176/28 |
| 澳洲      | 澳大利亞東部      | 20.37.193.0/25、</br>20.37.193.128/26、</br>13.70.74.144/28    |
| &nbsp;         | 澳大利亞東南部 | 20.42.225.0/25、</br>20.42.225.128/26、</br>13.77.53.160/28    |
| 巴西         | 巴西南部        | 191.235.224.128/25、</br>191.235.225.0/26、</br>191.233.205.160/28 |
| 加拿大         | 加拿大中部      | 52.228.80.128/25、</br>52.228.81.0/26、</br>13.71.175.80/28    |
| 歐洲         | 歐洲北部        | 20.38.82.0/23、</br>20.38.80.192/26、</br>13.69.230.96/28      |
| &nbsp;         | 歐洲西部         | 40.74.26.0/23、</br>40.74.24.192/26、</br>13.69.67.192/28      |
| 法國         | 法國中部      | 20.43.40.128/25、</br>20.43.41.0/26、</br>40.79.132.112/28     |
| 印度          | 印度中部       | 52.140.104.128/25、</br>52.140.105.0/26、</br>20.43.121.48/28  |
| 日本          | 日本東部          | 20.43.64.128/25、</br>20.43.65.0/26、</br>13.78.109.192/28     |
| 南韓          | 南韓中部       | 20.41.64.128/25、</br>20.41.65.0/26、</br>52.231.20.64/28      |
| 南非   | 南非北部  | 102.133.124.104/29、</br>102.133.216.128/25、</br>102.133.217.0/26 |
| 英國 | 英國南部            | 51.104.24.128/25、</br>51.104.25.0/26、</br>51.104.9.32/28     |
| 美國  | 美國中部          | 20.37.154.0/23、</br>20.37.156.0/26、</br>20.44.10.64/28       |
|                | 美國東部             | 20.42.2.0/23、</br>20.42.4.0/26、</br>40.71.14.32/28           |
|                | 美國東部 2            | 20.41.2.0/23、</br>20.41.4.0/26、</br>20.44.17.80/28           |
|                | 美國東部 2 EUAP      | 20.39.8.128/26、</br>20.39.8.96/27、</br>40.75.35.144/28       |
|                | 美國中北部    | 40.80.185.0/25、</br>40.80.185.128/26、</br>52.162.111.48/28   |
|                | 美國中南部    | 40.119.9.0/25、</br>40.119.9.128/26、</br>13.73.244.32/28      |
|                | 美國中西部     | 52.150.137.128/25、</br>52.150.136.192/26、</br>13.71.199.0/28 |
|                | 美國西部             | 40.82.250.0/23、</br>40.82.249.64/26、</br>13.86.219.208/28    |
|                | 美國西部 2            | 20.42.132.0/23、</br>20.42.129.64/26、</br>13.66.143.128/28    |

## <a name="known-issue-with-azure-storage"></a>Azure 儲存體的已知問題

* 連線到 Azure 儲存體帳戶時，IP 網路規則不會影響源自儲存體帳戶之相同區域中來自 Azure 整合執行時間的要求。 如需詳細資訊，請[參閱這篇文章](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range)。 

  相反地，我們建議您[在連接到 Azure 儲存體時](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993)，使用信任的服務。 

## <a name="next-steps"></a>後續步驟

* [Azure Data Factory 中資料移動的安全性考慮](data-movement-security-considerations.md)

---
title: 設定 Azure HDInsight 中的網路虛擬裝置
description: 瞭解如何在 Azure HDInsight 中為您的網路虛擬裝置設定一些額外功能。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: abedaf6f66bdd7aea36512c90bb5ee799f8e7a99
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85800939"
---
# <a name="configure-network-virtual-appliance-in-azure-hdinsight"></a>設定 Azure HDInsight 中的網路虛擬裝置

> [!Important]
> 只有當您想要設定 Azure 防火牆以外的網路虛擬裝置（NVA）時，**才**需要下列資訊。

Azure 防火牆會自動設定為允許許多常見重要案例的流量。 使用另一個網路虛擬裝置時，您必須設定一些額外的功能。 當您設定網路虛擬裝置時，請記住下列因素：

* 具有服務端點功能的服務可以設定服務端點，這會導致略過 NVA，通常是基於成本或效能考慮。
* IP 位址相依性適用于非 HTTP/S 流量（TCP 和 UDP 流量）。
* FQDN HTTP/HTTPS 端點可以在您的 NVA 裝置中列入允許清單。
* 將您建立的路由表指派給您的 HDInsight 子網。

## <a name="service-endpoint-capable-dependencies"></a>支援服務端點的相依性

您可以選擇性地啟用下列一個或多個服務端點，這會導致略過 NVA。 此選項適用于大量資料傳輸，以節省成本，同時也可用於效能優化。 

| **端點** |
|---|
| Azure SQL |
| Azure 儲存體 |
| Azure Active Directory |

### <a name="ip-address-dependencies"></a>IP 位址相依性

| **端點** | **詳細資料** |
|---|---|
| [這裡](hdinsight-management-ip-addresses.md)發佈的 ip | 這些 Ip 適用于 HDInsight 控制位置，而且應該包含在 UDR 中，以避免非對稱式路由 |
| AAD-DS 私人 Ip | 只有 ESP 叢集才需要|


### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS 相依性

> [!Important]
> 下列清單只提供幾個最重要的 Fqdn。 您可以取得 Fqdn 的完整清單（大部分是 Azure 儲存體和 Azure 服務匯流排），以便[在此檔案中](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json)設定 NVA。 HDInsight 控制平面作業會使用這些相依性來成功建立叢集。

| **端點**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |

## <a name="next-steps"></a>後續步驟

* [使用防火牆限制輸出流量](./hdinsight-restrict-outbound-traffic.md)
* [Azure HDInsight 虛擬網路架構](hdinsight-virtual-network-architecture.md)

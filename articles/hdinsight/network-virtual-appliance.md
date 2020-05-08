---
title: 設定 Azure HDInsight 中的網路虛擬裝置
description: 瞭解如何在 Azure HDInsight 中為您的網路虛擬裝置設定一些額外功能。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: cbc2104ae3c55ae3670867b7a253d812f3a4be0e
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864704"
---
# <a name="configure-network-virtual-appliance-in-azure-hdinsight"></a>設定 Azure HDInsight 中的網路虛擬裝置

> [!Important]
> 只有當您想要設定 Azure 防火牆以外的網路虛擬裝置（NVA）時，**才**需要下列資訊。

Azure 防火牆會自動設定為允許許多常見重要案例的流量。 使用另一個網路虛擬裝置時，您必須設定一些額外的功能。 當您設定網路虛擬裝置時，請記住下列因素：

* 應使用服務端點來設定支援的服務端點服務。
* IP 位址相依性適用于非 HTTP/S 流量（TCP 和 UDP 流量）。
* FQDN HTTP/HTTPS 端點可以放在您的 NVA 裝置中。
* 萬用字元 HTTP/HTTPS 端點是相依性，可能會根據數個限定詞而有所不同。
* 將您建立的路由表指派給您的 HDInsight 子網。

## <a name="service-endpoint-capable-dependencies"></a>支援服務端點的相依性

| **端點** |
|---|
| Azure SQL |
| Azure 儲存體 |
| Azure Active Directory |

### <a name="ip-address-dependencies"></a>IP 位址相依性

| **端點** | **詳細資料** |
|---|---|
| \*:123 | NTP 時鐘檢查。 在連接埠 123 上的多個端點檢查流量 |
| [這裡](hdinsight-management-ip-addresses.md)發佈的 ip | 這些 Ip 是 HDInsight 服務 |
| ESP 叢集的 AAD-DS 私人 Ip |
| \*：16800（適用于 KMS Windows 啟用） |
| \*適用于 Log Analytics 的12000 |

### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS 相依性

> [!Important]
> 下列清單只提供幾個最重要的 Fqdn。 在[此檔案中](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json)設定 NVA 時，您可以取得額外的 fqdn （大多是 Azure 儲存體和 Azure 服務匯流排）。

| **端點**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| wawsinfraprodbay063.blob.core.windows.net:443                         |
| registry-1.docker.io:443                                              |
| auth.docker.io:443                                                    |
| production.cloudflare.docker.com:443                                  |
| download.docker.com:443                                               |
| us.archive.ubuntu.com:80                                              |
| download.mono-project.com:80                                          |
| packages.treasuredata.com:80                                          |
| security.ubuntu.com:80                                                |
| azure.archive.ubuntu.com:80                                           |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |

## <a name="next-steps"></a>後續步驟

* [使用防火牆限制輸出流量](./hdinsight-restrict-outbound-traffic.md)
* [Azure HDInsight 虛擬網路架構](hdinsight-virtual-network-architecture.md)

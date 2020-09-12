---
title: 在 Azure HDInsight 中設定網路虛擬裝置
description: 瞭解如何在 Azure HDInsight 中為您的網路虛擬裝置設定許多額外的功能。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: 1dcc345260c6a0844344bb33dec94c40d1dd9a54
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89650862"
---
# <a name="configure-network-virtual-appliance-in-azure-hdinsight"></a>在 Azure HDInsight 中設定網路虛擬裝置

> [!Important]
> 只有當您想要將網路虛擬裝置設定為 Azure 防火牆以外的 (NVA) 時， **才** 需要下列資訊。

Azure 防火牆會自動設定為允許許多常見重要案例的流量。 使用其他網路虛擬裝置將需要您設定一些額外的功能。 當您設定網路虛擬裝置時，請記住下列因素：

* 具有服務端點功能的服務可以使用服務端點進行設定，這會導致略過 NVA，通常是針對成本或效能考慮。
* IP 位址相依性適用于 (TCP 和 UDP 流量) 的非 HTTP/S 流量。
* FQDN HTTP/HTTPS 端點可以在您的 NVA 裝置中列入允許清單。
* 將您建立的路由表指派給您的 HDInsight 子網。

## <a name="service-endpoint-capable-dependencies"></a>支援服務端點的相依性

您可以選擇性地啟用下列其中一個或多個服務端點，這會導致略過 NVA。 此選項適用于大量的資料傳輸，以節省成本，也可用於效能優化。 

| **端點** |
|---|
| Azure SQL |
| Azure 儲存體 |
| Azure Active Directory |

### <a name="ip-address-dependencies"></a>IP 位址相依性

| **端點** | **詳細資料** |
|---|---|
| [在此](hdinsight-management-ip-addresses.md)發佈的 ip | 這些 Ip 適用于 HDInsight 控制位置，且應包含在 UDR 中，以避免非對稱式路由 |
| AAD-DS 私人 Ip | 只有 ESP 叢集需要|


### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS 相依性

> [!Important]
> 下列清單只提供幾個 Fqdn，在建立叢集之後以及在叢集操作的存留期間，作業系統和安全性修補或憑證驗證都需要這些 Fqdn。 您可以取得 Fqdn 相依性的清單， (大多 Azure 儲存體和 Azure 服務匯流排 [在此檔案中](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json)設定 NVA 的) 。 HDInsight 資源提供者 (RP) 使用這些相依性，以成功建立和監視/管理叢集。 這些包括遙測/診斷記錄、布建中繼資料、叢集相關設定、腳本、ARM 範本等等。FQDN 相依性清單可能會隨著發行未來的 HDIngisht 更新而變更。

| **端點**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| microsoft.com:80                                                      |

## <a name="next-steps"></a>接下來的步驟

* [使用防火牆限制輸出流量](./hdinsight-restrict-outbound-traffic.md)
* [Azure HDInsight 虛擬網路架構](hdinsight-virtual-network-architecture.md)

---
title: 在 Azure HDInsight 中設定網路虛擬裝置
description: 瞭解如何在 Azure HDInsight 中為您的網路虛擬裝置設定許多額外的功能。
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: 7fe266c3c7b75762133fca4645e0675845c28972
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943976"
---
# <a name="configure-network-virtual-appliance-in-azure-hdinsight"></a>在 Azure HDInsight 中設定網路虛擬裝置

> [!Important]
> 只有當您想要將網路虛擬裝置設定為 [Azure 防火牆](./hdinsight-restrict-outbound-traffic.md)以外的 (NVA) 時，**才** 需要下列資訊。

Azure 防火牆 FQDN 標記會自動設定為允許許多常見重要 Fqdn 的流量。 使用其他網路虛擬裝置將需要您設定一些額外的功能。 當您設定網路虛擬裝置時，請記住下列因素：

* 具有服務端點功能的服務可以使用服務端點進行設定，這會導致略過 NVA，通常是針對成本或效能考慮。
* 如果 ResourceProviderConnection 設定為 [ *輸出*]，您可以針對儲存體和 SQL server 使用私人端點進行中繼存放區，而不需要將它們新增至 NVA。
* IP 位址相依性適用于 (TCP 和 UDP 流量) 的非 HTTP/S 流量。
* FQDN HTTP/HTTPS 端點可以在您的 NVA 裝置中核准。
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
| [在此](hdinsight-management-ip-addresses.md)發佈的 ip | 這些 Ip 適用于 HDInsight 資源提供者，且應包含在 UDR 中，以避免非對稱式路由。 只有在 ResourceProviderConnection 設定為 [ *輸入*] 時，才需要此規則。 如果 ResourceProviderConnection 設定為 *輸出* ，則 UDR 中不需要這些 ip。  |
| AAD-DS 私人 Ip | 只有 ESP 叢集需要 Vnet 時才需要對等互連。|


### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS 相依性

您可以取得 Fqdn 相依性的清單， (大多 Azure 儲存體和 Azure 服務匯流排 [在此](https://github.com/Azure-Samples/hdinsight-fqdn-lists/)存放庫中設定 NVA 的) 。 如需區域清單，請參閱 [這裡](https://github.com/Azure-Samples/hdinsight-fqdn-lists/tree/master/Regional)。 HDInsight 資源提供者 (RP) 使用這些相依性，以成功建立和監視/管理叢集。 這些包括遙測/診斷記錄、布建中繼資料、叢集相關設定、腳本等。這項 FQDN 相依性清單可能會因發行未來的 HDInsight 更新而變更。

下列清單只提供幾個 Fqdn，在建立叢集 *之後* 以及叢集操作的存留期間，作業系統和安全性修補或憑證驗證都需要這些 fqdn：

| **執行時間相依性 Fqdn**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| microsoft.com/pki/mscorp/cps/default.htm：443                                      |
| microsoft.com:80                                                      |
|login.windows.net:443                                                  |
|login.microsoftonline.com:443                                          |

## <a name="next-steps"></a>後續步驟

* [使用防火牆限制輸出流量](./hdinsight-restrict-outbound-traffic.md)
* [Azure HDInsight 虛擬網路架構](hdinsight-virtual-network-architecture.md)

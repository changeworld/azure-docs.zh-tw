---
title: InvalidNetworkConfigurationErrorCode 錯誤-Azure HDInsight
description: 在 Azure HDInsight 中使用 InvalidNetworkConfigurationErrorCode 建立叢集失敗的各種原因
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: 6dd4db999cb130c9816ad023888a4333e968c224
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76720379"
---
# <a name="cluster-creation-fails-with-invalidnetworkconfigurationerrorcode-in-azure-hdinsight"></a>叢集建立因 Azure HDInsight 中的 InvalidNetworkConfigurationErrorCode 而失敗

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方法。

如果您看到錯誤碼`InvalidNetworkConfigurationErrorCode`說明「虛擬網路設定與 HDInsight 需求不相容」，通常表示叢集的[虛擬網路](../hdinsight-plan-virtual-network-deployment.md)設定有問題。 根據其餘的錯誤描述，遵循下列各節來解決您的問題。

## <a name="hostname-resolution-failed"></a>「主機名稱解析失敗」

### <a name="issue"></a>問題

錯誤描述包含「主機名稱解析失敗」。

### <a name="cause"></a>原因

此錯誤會指向自訂 DNS 設定的問題。 虛擬網路內的 DNS 伺服器可以將 DNS 查詢轉送到 Azure 的遞迴解析程式，以解析該虛擬網路內的主機名稱（如需詳細資訊，請參閱[虛擬網路中的名稱解析](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)）。 存取 Azure 的遞迴解析程式是透過所提供的虛擬 IP 168.63.129.16。 此 IP 只能從 Azure Vm 存取。 因此，如果您使用內部部署 DNS 伺服器，或您的 DNS 伺服器是 Azure VM，而不是叢集虛擬網路的一部分，則無法使用。

### <a name="resolution"></a>解決方案

1. 透過 Ssh 連線到屬於叢集一部分的 VM，然後執行命令`hostname -f`。 這會傳回主機的完整功能變數名稱（ `<host_fqdn>`在下列指示中稱為）。

1. 然後，執行命令`nslookup <host_fqdn>` （例如`nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net`）。 如果此命令會將名稱解析成 IP 位址，則表示您的 DNS 伺服器正常運作。 在此情況下，請向 HDInsight 提出支援案例，我們將會調查您的問題。 在您的支援案例中，請包含您所執行的疑難排解步驟。 這可協助我們更快解決問題。

1. 如果上述命令未傳回 IP 位址，則執行`nslookup <host_fqdn> 168.63.129.16` （例如`nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net 168.63.129.16`）。 如果此命令能夠解析 IP，則表示您的 DNS 伺服器不會將查詢轉送到 Azure 的 DNS，或者它不是與叢集相同的虛擬網路中的 VM。

1. 如果您沒有可作為叢集虛擬網路中自訂 DNS 伺服器的 Azure VM，則需要先新增此功能。 在虛擬網路中建立 VM，其會設定為 DNS 轉寄站。

1. 在虛擬網路中部署 VM 之後，請在此 VM 上設定 DNS 轉送規則。 將所有 Idn 名稱解析要求轉寄到168.63.129.16，並將其餘內容轉送至您的 DNS 伺服器。 [以下](../hdinsight-plan-virtual-network-deployment.md)是自訂 DNS 伺服器的這項設定的範例。

1. 將此 VM 的 IP 位址新增為虛擬網路 DNS 設定的第一個 DNS 專案。

---

## <a name="failed-to-connect-to-azure-storage-account"></a>「無法連接到 Azure 儲存體帳戶」

### <a name="issue"></a>問題

錯誤描述包含「無法連接到 Azure 儲存體帳戶」或「無法連線到 Azure SQL」。

### <a name="cause"></a>原因

Azure 儲存體和 SQL 沒有固定的 IP 位址，所以我們需要允許所有 Ip 的輸出連線，以允許存取這些服務。 確切的解決步驟取決於您是否已設定網路安全性群組（NSG）或使用者定義的規則（UDR）。 請參閱[使用網路安全性群組和使用者定義的路由來控制 HDInsight 的網路流量](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ip)一節，以取得這些設定的詳細資料。

### <a name="resolution"></a>解決方案

* 如果您的叢集使用[網路安全性群組（NSG）](../../virtual-network/virtual-network-vnet-plan-design-arm.md)。

    移至 [Azure 入口網站]，並找出與部署叢集的子網相關聯的 NSG。 在 [**輸出安全性規則**] 區段中，允許不受限制地對網際網路進行輸出存取（請注意，此處較小的**優先順序**號碼表示優先順序較高）。 此外，在 [**子網**] 區段中，確認此 NSG 是否已套用至叢集子網。

* 如果您的叢集使用[使用者定義的路由（UDR）](../../virtual-network/virtual-networks-udr-overview.md)。

    移至 [Azure 入口網站]，並找出與部署叢集的子網相關聯的路由表。 一旦您找到子網的路由表，請檢查其中的 [**路由**] 區段。

    如果已定義路由，請確定已部署叢集的區域有 IP 位址的路由，而且每個路由的**NextHopType**都是**網際網路**。 針對前述文章中記載的每個必要 IP 位址，應該會有定義的路由。

---

## <a name="virtual-network-configuration-is-not-compatible-with-hdinsight-requirement"></a>「虛擬網路設定與 HDInsight 需求不相容」

### <a name="issue"></a>問題

錯誤描述包含類似如下的訊息：

```
ErrorCode: InvalidNetworkConfigurationErrorCode
ErrorDescription: Virtual Network configuration is not compatible with HDInsight Requirement. Error: 'Failed to connect to Azure Storage Account; Failed to connect to Azure SQL; HostName Resolution failed', Please follow https://go.microsoft.com/fwlink/?linkid=853974 to fix it.
```

### <a name="cause"></a>原因

可能是自訂 DNS 設定的問題。

### <a name="resolution"></a>解決方案

驗證168.63.129.16 是否在自訂 DNS 鏈中。 虛擬網路內的 DNS 可以將要求轉送到 Azure 內的遞迴解析程式，以解析該虛擬網路內的主機名稱。 如需詳細資訊，請參閱[虛擬網路中的名稱解析](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)。 存取 Azure 的遞迴解析程式是透過所提供的虛擬 IP 168.63.129.16。

1. 使用[ssh 命令](../hdinsight-hadoop-linux-use-ssh-unix.md)連接到您的叢集。 以您叢集的名稱取代 CLUSTERNAME，然後輸入命令，以編輯下面的命令：

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 執行下列命令：

    ```bash
    cat /etc/resolv.conf | grep nameserver*
    ```

    您應該會看到如下的結果：

    ```output
    nameserver 168.63.129.16
    nameserver 10.21.34.43
    nameserver 10.21.34.44
    ```

    根據結果，選擇下列其中一個步驟來執行下列動作：

#### <a name="1686312916-is-not-in-this-list"></a>168.63.129.16 不在此清單中

**選項1**  
使用[規劃用於 Azure HDInsight 的虛擬網路](../hdinsight-plan-virtual-network-deployment.md)中所述的步驟，將168.63.129.16 新增為虛擬網路的第一個自訂 DNS。 只有當您的自訂 DNS 伺服器在 Linux 上執行時，這些步驟才適用。

**選項2**  
部署虛擬網路的 DNS 伺服器 VM。 請執行下列步驟：

* 在虛擬網路中建立 VM，其會設定為 DNS 轉寄站（可以是 Linux 或 windows VM）。
* 設定此 VM 上的 DNS 轉送規則（將所有 Idn 名稱解析要求轉寄至168.63.129.16，並將其餘內容轉送至您的 DNS 伺服器）。
* 將此 VM 的 IP 位址新增為虛擬網路 DNS 設定的第一個 DNS 專案。

#### <a name="1686312916-is-in-the-list"></a>168.63.129.16 在清單中

在此情況下，請使用 HDInsight 建立支援案例，我們會調查您的問題。 在您的支援案例中包含下列命令的結果。 這可協助我們更快地調查並解決問題。

從前端節點上的 ssh 會話，編輯，然後執行下列程式：

```bash
hostname -f
nslookup <headnode_fqdn> (e.g.nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
dig @168.63.129.16 <headnode_fqdn> (e.g. dig @168.63.129.16 hn0-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
```

---

### <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過[Azure 社區支援](https://azure.microsoft.com/support/community/)取得 azure 專家的解答。

* 連接[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帳戶，藉由將 Azure 社區連接至適當的資源來改善客戶體驗：解答、支援及專家。

* 如果您需要更多協助，您可以從[Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列選取 [**支援**]，或開啟 [說明 **+ 支援**] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 您的 Microsoft Azure 訂用帳戶包含訂用帳戶管理和帳單支援的存取權，而技術支援則透過其中一項[Azure 支援方案](https://azure.microsoft.com/support/plans/)提供。

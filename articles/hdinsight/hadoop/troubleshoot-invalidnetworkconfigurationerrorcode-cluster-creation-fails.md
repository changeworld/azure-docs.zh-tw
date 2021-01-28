---
title: InvalidNetworkConfigurationErrorCode 錯誤-Azure HDInsight
description: 在 Azure HDInsight 中使用 InvalidNetworkConfigurationErrorCode 建立叢集失敗的各種原因
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/12/2021
ms.openlocfilehash: 83d4819ecb1da91bda5fb4f1cb445bbc34fd007f
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98927010"
---
# <a name="cluster-creation-fails-with-invalidnetworkconfigurationerrorcode-in-azure-hdinsight"></a>叢集建立失敗，並在 Azure HDInsight 中 InvalidNetworkConfigurationErrorCode

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方式。

如果您看到的錯誤碼顯示「 `InvalidNetworkConfigurationErrorCode` 虛擬網路設定與 HDInsight 需求不相容」，通常表示您的叢集的 [虛擬網路](../hdinsight-plan-virtual-network-deployment.md) 設定有問題。 根據錯誤描述的其餘部分，遵循下列各節來解決您的問題。

## <a name="hostname-resolution-failed"></a>「主機名稱解析失敗」

### <a name="issue"></a>問題

錯誤描述包含「主機名稱解析失敗」。

### <a name="cause"></a>原因

此錯誤指向自訂 DNS 設定的問題。 虛擬網路內的 DNS 伺服器可以將 DNS 查詢轉送至 Azure 的遞迴解析程式，以解析該虛擬網路內的主機名稱 (如需詳細資料，請參閱 [虛擬網路中的名稱解析](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)) 。 存取 Azure 的遞迴解析程式是透過所提供的虛擬 IP 168.63.129.16。 此 IP 只能從 Azure Vm 進行存取。 因此，如果您使用內部部署 DNS 伺服器，或您的 DNS 伺服器是不屬於叢集虛擬網路一部分的 Azure VM，它將無法運作。

### <a name="resolution"></a>解決方案

1. 透過 Ssh 連線到屬於叢集一部分的 VM，然後執行命令 `hostname -f` 。 這會傳回主機的完整功能變數名稱 (如 `<host_fqdn>` 下列指示所示) 。

1. 然後，執行命令 `nslookup <host_fqdn>` (例如 `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net`) 。 如果此命令將名稱解析為 IP 位址，則表示您的 DNS 伺服器正常運作。 在此情況下，請使用 HDInsight 提出支援案例，我們會調查您的問題。 在您的支援案例中，請包含您所執行的疑難排解步驟。 這可協助我們更快解決問題。

1. 如果上述命令未傳回 IP 位址，請執行 (例如 `nslookup <host_fqdn> 168.63.129.16` `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net 168.63.129.16`) 。 如果此命令能夠解析 IP，這表示您的 DNS 伺服器未將查詢轉送到 Azure 的 DNS，或它不是與叢集相同虛擬網路的 VM。

1. 如果您沒有可作為叢集虛擬網路中自訂 DNS 伺服器的 Azure VM，則必須先新增此 VM。 在虛擬網路中建立 VM，此 VM 將設定為 DNS 轉寄站。

1. 當您在虛擬網路中部署 VM 之後，請在此 VM 上設定 DNS 轉送規則。 將所有 iDNS 的名稱解析要求轉寄至168.63.129.16，並將其餘部分轉寄到您的 DNS 伺服器。 [以下](../hdinsight-plan-virtual-network-deployment.md) 是自訂 DNS 伺服器的這項設定範例。

1. 將此 VM 的 IP 位址新增為虛擬網路 DNS 設定的第一個 DNS 專案。

---

## <a name="failed-to-connect-to-azure-storage-account"></a>「無法連接到 Azure 儲存體帳戶」

### <a name="issue"></a>問題

錯誤描述包含「無法連接到 Azure 儲存體帳戶」或「無法連線至 Azure SQL」。

### <a name="cause"></a>原因

Azure 儲存體和 SQL 沒有固定的 IP 位址，因此我們需要允許所有 Ip 的輸出連線，以允許存取這些服務。 確切的解決步驟取決於您是否已設定網路安全性群組 (NSG) 或 (UDR) 的 User-Defined 規則。 如需這些設定的詳細資訊，請參閱使用 [網路安全性群組和使用者定義路由來控制 HDInsight 的網路流量](../control-network-traffic.md) 一節。

### <a name="resolution"></a>解決方案

* 如果您的叢集使用 [ (NSG) 的網路安全性群組 ](../../virtual-network/virtual-network-vnet-plan-design-arm.md)。

    移至 Azure 入口網站並識別與部署叢集的子網相關聯的 NSG。 在 [ **輸出安全性規則** ] 區段中，允許網際網路的輸出存取而不受限制 (請注意，此處較小的 **優先順序** 號碼表示優先順序較高的) 。 此外，在 [ **子網** ] 區段中，確認是否已將此 NSG 套用至叢集子網。

* 如果您的叢集使用 [ (UDR) 的使用者定義路由 ](../../virtual-network/virtual-networks-udr-overview.md)。

    移至 Azure 入口網站並識別與部署叢集的子網相關聯的路由表。 一旦您找到子網的路由表，請檢查其中的 **路由** 區段。

    如果有已定義的路由，請確定已部署叢集的區域有 IP 位址的路由，且每個路由的 **NextHopType** 都是 **網際網路**。 應該會有針對上述文章中記載的每個必要 IP 位址定義的路由。

## <a name="failed-to-establish-an-outbound-connection-from-the-cluster-for-the-communication-with-the-hdinsight-resource-provider-please-ensure-that-outbound-connectivity-is-allowed"></a>「無法從叢集建立輸出連線，以與 HDInsight 資源提供者進行通訊。 請確定允許輸出連線能力。」

### <a name="issue"></a>問題

錯誤描述包含「無法從叢集建立輸出連線，以便與 HDInsight 資源提供者進行通訊。 請確定允許輸出連線能力。」

### <a name="cause"></a>原因

使用私人連結的 HDInsight 叢集時，必須將來自叢集的輸出存取設定為允許對 HDInsight 資源提供者進行連線。

### <a name="resolution"></a>解決方案

* 若要解決此問題，請參閱 HDInsight Private Link 設定步驟，位於 [私人連結設定](../hdinsight-private-link.md)
---

## <a name="virtual-network-configuration-is-not-compatible-with-hdinsight-requirement"></a>「虛擬網路設定與 HDInsight 需求不相容」

### <a name="issue"></a>問題

錯誤描述包含類似如下的訊息：

```
ErrorCode: InvalidNetworkConfigurationErrorCode
ErrorDescription: Virtual Network configuration is not compatible with HDInsight Requirement. Error: 'Failed to connect to Azure Storage Account; Failed to connect to Azure SQL; HostName Resolution failed', Please follow https://go.microsoft.com/fwlink/?linkid=853974 to fix it.
```

### <a name="cause"></a>原因

可能是自訂 DNS 設定有問題。

### <a name="resolution"></a>解決方案

驗證168.63.129.16 是否位於自訂 DNS 鏈中。 虛擬網路內的 DNS 可以將要求轉送到 Azure 內的遞迴解析程式，以解析該虛擬網路內的主機名稱。 如需詳細資訊，請參閱 [虛擬網路中的名稱解析](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)。 存取 Azure 的遞迴解析程式是透過所提供的虛擬 IP 168.63.129.16。

1. 使用 [ssh 命令](../hdinsight-hadoop-linux-use-ssh-unix.md)來連線到您的叢集。 編輯以下命令並將 CLUSTERNAME 取代為您叢集的名稱，然後輸入命令：

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 執行以下命令：

    ```bash
    cat /etc/resolv.conf | grep nameserver*
    ```

    您應該會看到如下內容：

    ```output
    nameserver 168.63.129.16
    nameserver 10.21.34.43
    nameserver 10.21.34.44
    ```

    根據結果，選擇下列其中一個步驟來執行：

#### <a name="1686312916-is-not-in-this-list"></a>168.63.129.16 不在此清單中

**選項 1**  
使用 [規劃 Azure HDInsight 的虛擬網路](../hdinsight-plan-virtual-network-deployment.md)中所述的步驟，將168.63.129.16 新增為虛擬網路的第一個自訂 DNS。 只有當您的自訂 DNS 伺服器是在 Linux 上執行時，這些步驟才適用。

**選項 2**  
部署虛擬網路的 DNS 伺服器 VM。 請執行下列步驟：

* 在虛擬網路中建立 VM，此 VM 將設定為 DNS 轉寄站 (它可以是 Linux 或 windows VM) 。
* 在此 VM 上設定 DNS 轉送規則 (將所有 iDNS 名稱解析要求轉寄至168.63.129.16，並將其餘部分傳送至您的 DNS 伺服器) 。
* 將此 VM 的 IP 位址新增為虛擬網路 DNS 設定的第一個 DNS 專案。

#### <a name="1686312916-is-in-the-list"></a>168.63.129.16 在清單中

在此情況下，請使用 HDInsight 建立支援案例，我們會調查您的問題。 在您的支援案例中包含下列命令的結果。 這可協助我們更快速地調查和解決問題。

從前端節點上的 ssh 會話，編輯，然後執行下列動作：

```bash
hostname -f
nslookup <headnode_fqdn> (e.g.nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
dig @168.63.129.16 <headnode_fqdn> (e.g. dig @168.63.129.16 hn0-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
```
### <a name="cause"></a>原因

此錯誤碼的另一個原因 `InvalidNetworkConfigurationErrorCode` 可能是在 `EnableVmProtection` PowerShell 或 Azure Runbook 中使用已被取代的參數。

### <a name="resolution"></a>解決方案

使用適用于 `Get-AzVirtualNetwork` [AZ PowerShell SDK](/powershell/module/az.network/get-azvirtualnetwork)中記載的有效參數

---

### <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。

* 與 [@AzureSupport](https://twitter.com/azuresupport) 聯繫 - 專為改善客戶體驗而設的官方 Microsoft Azure 帳戶，協助 Azure 社群連接至適當的資源，例如解答、支援及專家等。

* 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) 提交支援要求。 從功能表列中選取 [支援] 或開啟 [說明 + 支援] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](../../azure-portal/supportability/how-to-create-azure-support-request.md)。 您可透過 Microsoft Azure 訂閱來存取訂閱管理和帳單支援，並透過其中一項 [Azure 支援方案](https://azure.microsoft.com/support/plans/)以取得技術支援。

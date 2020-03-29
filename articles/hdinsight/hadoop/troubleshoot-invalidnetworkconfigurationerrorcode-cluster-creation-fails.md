---
title: 無效網路設定錯誤代碼錯誤 - Azure HDInsight
description: Azure HDInsight 中具有無效網路設定錯誤代碼的群集創建失敗的各種原因
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: 6dd4db999cb130c9816ad023888a4333e968c224
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720379"
---
# <a name="cluster-creation-fails-with-invalidnetworkconfigurationerrorcode-in-azure-hdinsight"></a>在 Azure HDInsight 中使用無效網路設定錯誤代碼創建群集失敗

本文介紹了與 Azure HDInsight 群集交互時問題的故障排除步驟和可能的解決方案。

如果您看到描述"虛擬`InvalidNetworkConfigurationErrorCode`網路設定與 HDInsight 要求不相容"的錯誤代碼，則通常表示群集的[虛擬網路配置](../hdinsight-plan-virtual-network-deployment.md)有問題。 根據錯誤描述的其餘部分，請按照以下部分解決您的問題。

## <a name="hostname-resolution-failed"></a>"主機名稱解析失敗"

### <a name="issue"></a>問題

錯誤描述包含"主機名稱解析失敗"。

### <a name="cause"></a>原因

此錯誤指向自訂 DNS 配置的問題。 虛擬網路中的 DNS 伺服器可以將 DNS 查詢轉發到 Azure 的遞迴解析器，以解析該虛擬網路中的主機名稱（有關詳細資訊，請參閱[虛擬網路中的名稱解析](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)）。 存取 Azure 的遞迴解析程式是透過所提供的虛擬 IP 168.63.129.16。 此 IP 只能從 Azure VM 訪問。 因此，如果您使用的是 OnPrem DNS 伺服器，或者您的 DNS 伺服器是 Azure VM，則它不起作用，而 Azure VM 不屬於群集的虛擬網路。

### <a name="resolution"></a>解決方案

1. Ssh 進入群集的 VM，並運行命令`hostname -f`。 這將返回主機完全限定的功能變數名稱（請參閱`<host_fqdn>`以下說明）。

1. 然後，運行命令`nslookup <host_fqdn>`（例如， `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net`。 如果此命令將名稱解析為 IP 位址，則表示 DNS 伺服器工作正常。 在這種情況下，請向 HDInsight 提出支援案例，我們將調查您的問題。 在支援案例中，包括您執行的故障排除步驟。 這將有助於我們更快地解決問題。

1. 如果上述命令不返回 IP 位址，則運行`nslookup <host_fqdn> 168.63.129.16`（例如 ， `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net 168.63.129.16`。 如果此命令能夠解析 IP，則意味著 DNS 伺服器未將查詢轉發到 Azure 的 DNS，或者它不是與群集屬於同一虛擬網路的 VM。

1. 如果沒有可在群集虛擬網路中充當自訂 DNS 伺服器的 Azure VM，則需要首先添加此 VM。 在虛擬網路中創建 VM，該 VM 將配置為 DNS 轉寄站。

1. 在虛擬網路中部署 VM 後，在此 VM 上配置 DNS 轉發規則。 將所有 iDNS 名稱解析請求轉發到 168.63.129.16，其餘請求將轉發到 DNS 伺服器。 [下面是](../hdinsight-plan-virtual-network-deployment.md)自訂 DNS 伺服器此設置的示例。

1. 添加此 VM 的 IP 位址作為虛擬網路 DNS 配置的第一個 DNS 條目。

---

## <a name="failed-to-connect-to-azure-storage-account"></a>"無法連接到 Azure 存儲帳戶"

### <a name="issue"></a>問題

錯誤說明包含"無法連接到 Azure 存儲帳戶"或"無法連接到 Azure SQL"。

### <a name="cause"></a>原因

Azure 存儲和 SQL 沒有固定的 IP 位址，因此我們需要允許所有 IP 的出站連接以允許訪問這些服務。 確切的解決步驟取決於您是否已設置網路安全性群組 （NSG） 還是使用者定義規則 （UDR）。 有關這些配置的詳細資訊，請參閱有關[使用 HDInsight 使用網路安全性群組和使用者定義路由控制網路流量](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ip)的部分。

### <a name="resolution"></a>解決方案

* 如果您的群集使用[網路安全性群組 （NSG）。](../../virtual-network/virtual-network-vnet-plan-design-arm.md)

    轉到 Azure 門戶，並標識與部署群集的子閘道聯的 NSG。 在 **"出站安全規則"** 部分中，允許出站訪問互聯網不受限制（請注意，此處的**優先順序**較小意味著更高的優先順序）。 此外，在**子網**部分中，確認此 NSG 是否應用於群集子網。

* 如果您的群集使用[使用者定義的路由 （UDR）。](../../virtual-network/virtual-networks-udr-overview.md)

    轉到 Azure 門戶，並標識與部署群集的子閘道聯的路由表。 找到子網的路由表後，檢查其中的**路由**部分。

    如果定義了路由，請確保部署群集的區域有 IP 位址的路由，並且每個路由的下**一個HopType**是**Internet**。 應為上述文章中記錄的每個必需 IP 位址定義一個路由。

---

## <a name="virtual-network-configuration-is-not-compatible-with-hdinsight-requirement"></a>"虛擬網路配置與 HDInsight 要求不相容"

### <a name="issue"></a>問題

錯誤描述包含類似于如下的消息：

```
ErrorCode: InvalidNetworkConfigurationErrorCode
ErrorDescription: Virtual Network configuration is not compatible with HDInsight Requirement. Error: 'Failed to connect to Azure Storage Account; Failed to connect to Azure SQL; HostName Resolution failed', Please follow https://go.microsoft.com/fwlink/?linkid=853974 to fix it.
```

### <a name="cause"></a>原因

可能是自訂 DNS 設置的問題。

### <a name="resolution"></a>解決方案

驗證 168.63.129.16 是否位於自訂 DNS 鏈中。 虛擬網路內的 DNS 可以將要求轉送到 Azure 內的遞迴解析程式，以解析該虛擬網路內的主機名稱。 有關詳細資訊，請參閱[虛擬網路中的名稱解析](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)。 存取 Azure 的遞迴解析程式是透過所提供的虛擬 IP 168.63.129.16。

1. 使用[ssh 命令](../hdinsight-hadoop-linux-use-ssh-unix.md)連接到群集。 通過將 CLUSTERNAME 替換為群集的名稱來編輯下面的命令，然後輸入以下命令：

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

    根據結果 - 選擇以下步驟之一：

#### <a name="1686312916-is-not-in-this-list"></a>168.63.129.16 不在此清單中

**選項 1**  
使用[為 Azure HDInsight 規劃虛擬網路](../hdinsight-plan-virtual-network-deployment.md)中所述的步驟，添加 168.63.129.16 作為虛擬網路的第一個自訂 DNS。 僅當自訂 DNS 伺服器在 Linux 上運行時，這些步驟才適用。

**選項 2**  
為虛擬網路部署 DNS 伺服器 VM。 請執行下列步驟：

* 在虛擬網路中創建 VM，該 VM 將配置為 DNS 轉寄站（可以是 Linux 或 Windows VM）。
* 在此 VM 上配置 DNS 轉發規則（將所有 iDNS 名稱解析請求轉發到 168.63.129.16，其餘請求轉發到 DNS 伺服器）。
* 添加此 VM 的 IP 位址作為虛擬網路 DNS 配置的第一個 DNS 條目。

#### <a name="1686312916-is-in-the-list"></a>168.63.129.16 在清單中

在這種情況下，請使用 HDInsight 創建支援案例，我們將調查您的問題。 在支援案例中包括以下命令的結果。 這將有助於我們更快地調查和解決問題。

從頭節點上的 ssh 會話中，編輯然後運行以下內容：

```bash
hostname -f
nslookup <headnode_fqdn> (e.g.nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
dig @168.63.129.16 <headnode_fqdn> (e.g. dig @168.63.129.16 hn0-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
```

---

### <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 通過[Azure 社區支援](https://azure.microsoft.com/support/community/)從 Azure 專家那裡獲得答案。

* 與[@AzureSupport](https://twitter.com/azuresupport)- 正式的 Microsoft Azure 帳戶連接 Azure 社區，以將 Azure 社區連接到正確的資源：答案、支援和專家，從而改善客戶體驗。

* 如果需要更多説明，可以從[Azure 門戶](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援請求。 從功能表列中選擇 **"支援"** 或打開 **"説明 + 支援**中心"。 有關詳細資訊，請查看[如何創建 Azure 支援請求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 訂閱中包含對訂閱管理和計費支援的訪問，並且通過[Azure 支援計畫](https://azure.microsoft.com/support/plans/)之一提供技術支援。

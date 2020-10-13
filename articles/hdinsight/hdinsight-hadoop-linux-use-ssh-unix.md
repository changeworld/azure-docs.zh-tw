---
title: 使用 SSH 搭配 Hadoop - Azure HDInsight
description: 您可以使用安全殼層 (SSH) 存取 HDInsight。 本檔提供從 Windows、Linux、Unix 或 macOS 用戶端使用 ssh 命令連接到 HDInsight 的相關資訊。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 02/28/2020
ms.openlocfilehash: 074b1571cea6c102a00fcefe7934cad0ded8458d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86087651"
---
# <a name="connect-to-hdinsight-apache-hadoop-using-ssh"></a>使用 SSH 連線到 HDInsight (Apache Hadoop)

了解如何使用[安全殼層 (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) 安全地連線到 Apache Hadoop on Azure HDInsight。 如需透過虛擬網路連接的詳細資訊，請參閱 [Azure HDInsight 虛擬網路架構](./hdinsight-virtual-network-architecture.md)。 另請參閱 [規劃 Azure HDInsight 叢集的虛擬網路部署](./hdinsight-plan-virtual-network-deployment.md)。

下表包含使用 SSH 用戶端連接到 HDInsight 時所需的位址和埠資訊：

| 位址 | 連接埠 | 連線到... |
| ----- | ----- | ----- |
| `<clustername>-ssh.azurehdinsight.net` | 22 | 主要前端節點 |
| `<clustername>-ssh.azurehdinsight.net` | 23 | 次要前端節點 |
| `<clustername>-ed-ssh.azurehdinsight.net` | 22 | HDInsight 上 (ML 服務的邊緣節點)  |
| `<edgenodename>.<clustername>-ssh.azurehdinsight.net` | 22 | 邊緣節點 (任何其他叢集類型（如果邊緣節點存在）)  |

將 `<clustername>` 取代為您的叢集名稱。 將 `<edgenodename>` 替換為邊緣節點的名稱。

如果您的叢集包含邊緣節點，我們建議您__一律使用 SSH 連線到邊緣節點__。 前端節點會裝載對於 Hadoop 健康狀態至關重要的服務。 邊緣節點則只會執行您放在上面的服務。 如需使用邊緣節點的詳細資訊，請參閱[在 HDInsight 中使用邊緣節點](hdinsight-apps-use-edge-node.md#access-an-edge-node)。

> [!TIP]  
> 當您初次連線至 HDInsight，您的 SSH 用戶端可能會顯示警告，指出無法建立主機的真確性。 在系統提示時，選取 [是] 將主機新增至您的 SSH 用戶端信任的伺服器清單。
>
> 如果您之前曾連線至相同名稱的伺服器，您可能會收到警告，指出預存的主機金鑰與伺服器的主機金鑰不符。 如需了解如何移除現有的伺服器名稱項目，請參閱您的 SSH 用戶端文件。

## <a name="ssh-clients"></a>SSH 用戶端

Linux、Unix 和 macOS 系統提供 `ssh` 和 `scp` 命令。 `ssh` 用戶端通常用來建立以 Linux 或 Unix 為基礎之系統的遠端命令列工作階段。 `scp` 用戶端用來安全地複製用戶端與遠端系統之間的檔案。

Microsoft Windows 預設不會安裝任何 SSH 用戶端。 `ssh` 和 `scp` 用戶端均可透過下列套件使用於 Windows︰

* [OpenSSH 用戶端](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse)。 此用戶端是 Windows 10 Fall Creators Update 引進的選擇性功能。

* [Windows 10 上 Ubuntu 的 Bash](https://docs.microsoft.com/windows/wsl/about)。

* [Azure Cloud Shell](../cloud-shell/quickstart.md)。 Cloud Shell 在您的瀏覽器中提供 Bash 環境。

* [Git](https://git-scm.com/)。

另外還有數個圖形化 SSH 用戶端，例如 [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/) 和 [MobaXterm](https://mobaxterm.mobatek.net/)。 雖然這些用戶端可用來連線到 HDInsight，但連線的程序與使用 `ssh` 公用程式時不同。 如需詳細資訊，請參閱您所使用之圖形用戶端的檔。

## <a name="authentication-ssh-keys"></a><a id="sshkey"></a>驗證︰SSH 金鑰

SSH 金鑰使用 [公開金鑰加密](https://en.wikipedia.org/wiki/Public-key_cryptography) 來驗證 SSH 會話。 SSH 金鑰比密碼更安全，並提供簡單的方式來保護 Hadoop 叢集的存取。

如果您使用金鑰來保護 SSH 帳戶，當您連線時，用戶端必須提供對應的私密金鑰︰

* 大部分用戶端均可設定為使用__預設金鑰__。 例如，`ssh` 用戶端會在 Linux 和 Unix 環境的 `~/.ssh/id_rsa` 中尋找私密金鑰。

* 您可以指定__私密金鑰的路徑__。 使用 `ssh` 用戶端時，您可以使用 `-i` 參數來指定私密金鑰的路徑。 例如： `ssh -i ~/.ssh/id_rsa sshuser@myedge.mycluster-ssh.azurehdinsight.net` 。

* 如果您有__多個私密金鑰__可搭配不同的伺服器使用，請考慮使用 [ssh-agent (https://en.wikipedia.org/wiki/Ssh-agent)](https://en.wikipedia.org/wiki/Ssh-agent) 之類的公用程式。 `ssh-agent` 公用程式可用來自動選取在建立 SSH 工作階段時所要使用的金鑰。

> [!IMPORTANT]  
> 如果您使用複雜密碼來保護私密金鑰，您必須在使用金鑰時輸入複雜密碼。 `ssh-agent` 之類的公用程式可以快取密碼以方便您使用。

### <a name="create-an-ssh-key-pair"></a>建立 SSH 金鑰組

使用 `ssh-keygen` 命令來建立公開和私密金鑰檔案。 下列命令會產生 2048 位元的 RSA 金鑰組，以供搭配 HDInsight 使用︰

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048
```

系統會在金鑰建立過程中提示您輸入資訊。 例如，金鑰的儲存位置或是否要使用複雜密碼。 程序完成之後，系統會建立兩個檔案：公開金鑰和私密金鑰。

* __公開金鑰__可用來建立 HDInsight 叢集。 公開金鑰的副檔名為 `.pub`。

* __私密金鑰__可用來向 HDInsight 叢集驗證用戶端。

> [!IMPORTANT]  
> 您可以使用複雜密碼來保護金鑰。 複雜密碼實際上就是私密金鑰的密碼。 即使有人取得您的私密金鑰，他們必須有複雜密碼才能使用該金鑰。

### <a name="create-hdinsight-using-the-public-key"></a>使用公開金鑰建立 HDInsight

| 建立方法 | 如何使用公開金鑰 |
| ------- | ------- |
| Azure 入口網站 | 取消核取 [ __使用 ssh 的叢集登入密碼__]，然後選取 [ __公開金鑰__ ] 作為 [ssh 驗證類型]。 最後，選取公開金鑰檔案，或將檔案的文字內容貼到 [SSH 公開金鑰]____ 欄位。</br>![建立 HDInsight 叢集時的 [SSH 公開金鑰] 對話方塊](./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-public-key.png) |
| Azure PowerShell | 使用 `-SshPublicKey` [>new-azhdinsightcluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) Cmdlet 的參數，並以字串形式傳遞公開金鑰的內容。|
| Azure CLI | 使用 `--sshPublicKey` 命令的參數 [`az hdinsight create`](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) ，並以字串形式傳遞公開金鑰的內容。 |
| Resource Manager 範本 | 如需對範本使用 SSH 金鑰的範例，請參閱[使用 SSH 金鑰在 Linux 上部署 HDInsight](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-publickey/)。 [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-publickey/azuredeploy.json) 檔案中的 `publicKeys` 元素可用來在建立叢集時將金鑰傳遞至 Azure。 |

## <a name="authentication-password"></a>驗證：密碼

您可以使用密碼來保護 SSH 帳戶。 當您使用 SSH 連線到 HDInsight 時，系統會提示您輸入密碼。

> [!WARNING]  
> Microsoft 不建議對 SSH 使用密碼驗證。 密碼可以猜到，因此很容易遭受暴力密碼破解攻擊。 相反地，我們會建議您使用 [SSH 金鑰來進行驗證](#sshkey)。

> [!IMPORTANT]  
> SSH 帳戶密碼會在 HDInsight 叢集建立後 70 天到期。 如果密碼到期，您可以使用[管理 HDInsight](hdinsight-administer-use-portal-linux.md#change-passwords) 文件中的資訊更換。

### <a name="create-hdinsight-using-a-password"></a>使用密碼建立 HDInsight

| 建立方法 | 如何指定密碼 |
| --------------- | ---------------- |
| Azure 入口網站 | 根據預設，SSH 使用者帳戶會具有和叢集登入帳戶相同的密碼。 若要使用不同的密碼，請取消核取 [ __使用 ssh 的叢集登入密碼__]，然後在 [ __ssh 密碼__ ] 欄位中輸入密碼。</br>![建立 HDInsight 叢集時的 [SSH 密碼] 對話方塊](./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-password.png)|
| Azure PowerShell | 使用 `--SshCredential` [>new-azhdinsightcluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) Cmdlet 的參數，並傳遞 `PSCredential` 包含 SSH 使用者帳戶名稱和密碼的物件。 |
| Azure CLI | 使用 `--ssh-password` 命令的參數 [`az hdinsight create`](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) ，並提供密碼值。 |
| Resource Manager 範本 | 如需對範本使用密碼的範例，請參閱[使用 SSH 密碼在 Linux 上部署 HDInsight](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-password/)。 [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-password/azuredeploy.json) 檔案中的 `linuxOperatingSystemProfile` 元素可用來在建立叢集時將 SSH 帳戶名稱和密碼傳遞至 Azure。|

### <a name="change-the-ssh-password"></a>變更 SSH 密碼

如需有關變更 SSH 使用者帳戶密碼的資訊，請參閱[管理 HDInsight](hdinsight-administer-use-portal-linux.md#change-passwords) 文件的__變更密碼__一節。

## <a name="authentication-domain-joined-hdinsight"></a>已加入驗證網域的 HDInsight

如果您使用已 __加入網域的 HDInsight__叢集，您必須在 `kinit` 與 SSH 本機使用者連線之後使用命令。 此命令會提示您輸入網域使用者和密碼，並向與叢集相關聯的 Azure Active Directory 網域驗證您的工作階段。

您也可以在每個加入網域的節點上啟用 Kerberos 驗證 (例如，前端節點、邊緣節點) 至使用網域帳戶的 ssh。 若要進行此作業，請編輯 sshd 組態檔：

```bash
sudo vi /etc/ssh/sshd_config
```

取消註解，並將 `KerberosAuthentication` 變更為 `yes`

```bash
sudo service sshd restart
```

使用 `klist` 命令確認 Kerberos 驗證是否成功。

如需詳細資訊，請參閱[設定已加入網域的 HDInsight](./domain-joined/apache-domain-joined-configure.md)。

## <a name="connect-to-nodes"></a>連線到節點

前端節點和邊緣節點 (是否可透過網際網路上的埠22和23存取一個) 。

* 連線到__前端節點__時，請使用連接埠 __22__ 連線到主要前端節點，以及使用連接埠 __23__ 連線到次要前端節點。 要使用的完整網域名稱為 `clustername-ssh.azurehdinsight.net`，其中 `clustername` 是您的叢集名稱。

    ```bash
    # Connect to primary head node
    # port not specified since 22 is the default
    ssh sshuser@clustername-ssh.azurehdinsight.net

    # Connect to secondary head node
    ssh -p 23 sshuser@clustername-ssh.azurehdinsight.net
    ```

* 連線到__邊緣節點__時，請使用連接埠 22。 完整網域名稱為 `edgenodename.clustername-ssh.azurehdinsight.net`，其中 `edgenodename` 是您建立邊緣節點時提供的名稱。 `clustername` 是叢集的名稱。

    ```bash
    # Connect to edge node
    ssh sshuser@edgnodename.clustername-ssh.azurehdinsight.net
    ```

> [!IMPORTANT]  
> 先前的範例假設您使用密碼驗證，或該憑證驗證自動發生。 如果您使用 SSH 金鑰組進行驗證，但未自動使用憑證，請使用 `-i` 參數來指定私密金鑰。 例如： `ssh -i ~/.ssh/mykey sshuser@clustername-ssh.azurehdinsight.net` 。

連接之後，提示會變更，以指出 SSH 使用者名稱和您所連接的節點。 例如，以 `sshuser` 身分連線到主要前端節點時，提示為 `sshuser@<active-headnode-name>:~$`。

### <a name="connect-to-worker-and-apache-zookeeper-nodes"></a>連線至背景工作角色和 Apache Zookeeper 節點

背景工作角色節點和 Zookeeper 節點無法直接從網際網路存取。 從叢集前端節點或邊緣節點即可加以存取。 以下是連接至其他節點的一般步驟：

1. 使用 SSH 連接前端或邊緣節點：

    ```bash
    ssh sshuser@myedge.mycluster-ssh.azurehdinsight.net
    ```

2. 從 SSH 到前端或邊緣節點的連線，使用 `ssh` 命令來連接叢集中的背景工作角色節點︰

    ```bash
    ssh sshuser@wn0-myhdi
    ```

    若要擷取節點名稱清單，請參閱[使用 Apache Ambari REST API 管理 HDInsight](hdinsight-hadoop-manage-ambari-rest-api.md#get-the-fqdn-of-cluster-nodes) 文件。

如果使用__密碼__來保護 SSH 帳戶，請在連線時輸入密碼。

如果使用 __SSH 金鑰__來保護 SSH 帳戶，請確定用戶端上的 SSH 轉送已啟用。

> [!NOTE]  
> 若要直接存取叢集中的所有節點，另一種方式是將 HDInsight 安裝到 Azure 虛擬網路。 然後，您可以將遠端機器加入相同的虛擬網路，並直接存取叢集中的所有節點。
>
> 如需詳細資訊，請參閱 [規劃 HDInsight 的虛擬網路](hdinsight-plan-virtual-network-deployment.md)。

### <a name="configure-ssh-agent-forwarding"></a>設定 SSH 代理程式轉送

> [!IMPORTANT]  
> 以下步驟假設您使用以 Linux 或 UNIX 為基礎的系統，並使用 Bash on Windows 10 進行操作。 如果這些步驟不適用於您的系統，您可能需要參閱 SSH 用戶端的文件。

1. 使用文字編輯器開啟 `~/.ssh/config`。 如果該檔案不存在，您可以藉由在命令列中輸入 `touch ~/.ssh/config` 加以建立。

2. 將下列文字新增至 `config` 檔案。

    ```
    Host <edgenodename>.<clustername>-ssh.azurehdinsight.net
        ForwardAgent yes
    ```

    將 __Host__ 資訊替換為您使用 SSH 連線到的節點位址。 先前的範例使用邊緣節點。 這個項目會為指定節點設定 SSH 代理程式轉送。

3. 從終端機使用下列命令，測試 SSH 代理程式轉送：

    ```bash
    echo "$SSH_AUTH_SOCK"
    ```

    此命令會傳回類似以下文字的資訊：

    ```output
    /tmp/ssh-rfSUL1ldCldQ/agent.1792
    ```

    如果沒有傳回任何專案，則不會執行 `ssh-agent` 。 如需詳細資訊，請參閱[透過 ssh 使用 ssh-agent (http://mah.everybody.org/docs/ssh)](http://mah.everybody.org/docs/ssh)) 的代理程式啟動指令碼資訊，或參閱 SSH 用戶端文件。

4. 一旦您確認 **ssh 代理** 程式正在執行，請使用下列程式將 ssh 私密金鑰新增至代理程式：

    ```bash
    ssh-add ~/.ssh/id_rsa
    ```

    如果您的私密金鑰儲存在不同的檔案，請將 `~/.ssh/id_rsa` 取代為檔案的路徑。

5. 使用 SSH 連線到叢集的邊緣節點或前端節點。 然後，使用 SSH 命令連線到背景工作角色或 Zookeeper 節點。 該連線會使用轉送的金鑰來建立。

## <a name="next-steps"></a>接下來的步驟

* [對 HDInsight 使用 SSH 通道](hdinsight-linux-ambari-ssh-tunnel.md)
* [在 HDInsight 中使用邊緣節點](hdinsight-apps-use-edge-node.md#access-an-edge-node)
* [搭配使用 SCP 與 HDInsight](./use-scp.md)

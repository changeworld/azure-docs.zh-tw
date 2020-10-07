---
title: 為 Azure 中 RHEL 虛擬機器上的 SQL Server 設定可用性群組 - Linux 虛擬機器 | Microsoft Docs
description: 了解如何在 RHEL 叢集環境中設定高可用性及設定 STONITH
ms.service: virtual-machines-linux
ms.subservice: ''
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: jroth
ms.date: 06/25/2020
ms.openlocfilehash: 4411bd490ab72aa27fbf16a8598a9ff0dae7a5b5
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91358911"
---
# <a name="tutorial-configure-availability-groups-for-sql-server-on-rhel-virtual-machines-in-azure"></a>教學課程：在 Azure 中為 RHEL 虛擬機器上的 SQL Server 設定可用性群組 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!NOTE]
> 我們在本教學課程中，使用含 RHEL 7.6 的 SQL Server 2017 來設定高可用性，但使用 RHEL 7 或 RHEL 8 的 SQL Server 2019 也可行。 用以設定 Pacemake 叢集和可用性群組資源的命令，在 RHEL 8 中已有所變更。如需正確命令的詳細資訊，請參閱[建立可用性群組資源](/sql/linux/sql-server-linux-availability-group-cluster-rhel#create-availability-group-resource)一文與 RHEL 8 資源。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> - 建立新的資源群組、可用性設定組及 Azure Linux 虛擬機器 (VM)
> - 啟用高可用性 (HA)
> - 建立 Pacemaker 叢集
> - 藉由建立 STONITH 裝置來設定隔離代理程式
> - 在 RHEL 上安裝 SQL Server 和 mssql-tools
> - 設定 SQL Server Always On 可用性群組
> - 在 Pacemaker 叢集中設定可用性群組 (AG) 資源
> - 測試容錯移轉和隔離代理程式

本教學課程將使用 Azure CLI 在 Azure 中部署資源。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

如果您偏好在本機安裝和使用 CLI，在進行本教學課程時將需要 Azure CLI 2.0.30 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="create-a-resource-group"></a>建立資源群組

如果您有多個訂用帳戶，請[設定要部署這些資源的訂用帳戶](/cli/azure/manage-azure-subscriptions-azure-cli)。

使用下列命令，在區域中建立資源群組 `<resourceGroupName>`。 請將 `<resourceGroupName>` 取代為您選擇的名稱。 在本教學課程中，我們將使用 `East US 2`。 如需詳細資訊，請參閱下列[快速入門](../../../application-gateway/quick-create-cli.md)。

```azurecli-interactive
az group create --name <resourceGroupName> --location eastus2
```

## <a name="create-an-availability-set"></a>建立可用性設定組

接下來要建立可用性設定組。 請在 Azure Cloud Shell 中執行下列命令，並以您的資源群組名稱取代 `<resourceGroupName>`。 選擇 `<availabilitySetName>` 的名稱。

```azurecli-interactive
az vm availability-set create \
    --resource-group <resourceGroupName> \
    --name <availabilitySetName> \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

命令完成後，您應該會得到下列結果：

```output
{
  "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/availabilitySets/<availabilitySetName>",
  "location": "eastus2",
  "name": "<availabilitySetName>",
  "platformFaultDomainCount": 2,
  "platformUpdateDomainCount": 2,
  "proximityPlacementGroup": null,
  "resourceGroup": "<resourceGroupName>",
  "sku": {
    "capacity": null,
    "name": "Aligned",
    "tier": null
  },
  "statuses": null,
  "tags": {},
  "type": "Microsoft.Compute/availabilitySets",
  "virtualMachines": []
}
```

## <a name="create-rhel-vms-inside-the-availability-set"></a>在可用性設定組中建立 RHEL VM

> [!WARNING]
> 若選擇隨用隨付 (PAYG) RHEL 映像，並設定高可用性 (HA)，必須註冊您的訂閱。 這可能會導致您為訂用帳戶支付兩次費用，因為 VM 的 Microsoft Azure RHEL 訂用帳戶和 Red Hat 的訂用帳戶都會收費。 如需詳細資訊，請參閱 https://access.redhat.com/solutions/2458541 。
>
> 若要避免「重複計費」，在建立 Azure VM 時請使用 RHEL HA 映像。 以 RHEL-HA 映像的形式提供的映像，也是已預先啟用 HA 存放庫的 PAYG 映像。

1. 取得提供含 A 之 RHEL 的虛擬機器映像清單：

    ```azurecli-interactive
    az vm image list --all --offer "RHEL-HA"
    ```

    您應該會看見下列結果：

    ```output
    [
      {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "7.4",
    "urn": "RedHat:RHEL-HA:7.4:7.4.2019062021",
    "version": "7.4.2019062021"
       },
       {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "7.5",
    "urn": "RedHat:RHEL-HA:7.5:7.5.2019062021",
    "version": "7.5.2019062021"
        },
        {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "7.6",
    "urn": "RedHat:RHEL-HA:7.6:7.6.2019062019",
    "version": "7.6.2019062019"
         },
         {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "8.0",
    "urn": "RedHat:RHEL-HA:8.0:8.0.2020021914",
    "version": "8.0.2020021914"
         },
         {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "8.1",
    "urn": "RedHat:RHEL-HA:8.1:8.1.2020021914",
    "version": "8.1.2020021914"
          },
          {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "80-gen2",
    "urn": "RedHat:RHEL-HA:80-gen2:8.0.2020021915",
    "version": "8.0.2020021915"
           },
           {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "81_gen2",
    "urn": "RedHat:RHEL-HA:81_gen2:8.1.2020021915",
    "version": "8.1.2020021915"
           }
    ]
    ```

    在本教學課程中，我們會選擇 映像 `RedHat:RHEL-HA:7.6:7.6.2019062019` 作為 RHEL 7 的範例，然後選擇 `RedHat:RHEL-HA:8.1:8.1.2020021914` 作為 RHEL 8 的範例。
    
    您也可以選擇在 RHEL8-HA 映像上預先安裝 SQL Server 2019。 若要取得這些映像的清單，請執行下列命令：  
    
    ```azurecli-interactive
    az vm image list --all --offer "sql2019-rhel8"
    ```

    您應該會看見下列結果：

    ```output
    [
      {
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "enterprise",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:enterprise:15.0.200317",
    "version": "15.0.200317"
       },
       }
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "enterprise",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:enterprise:15.0.200512",
    "version": "15.0.200512"
       },
       {
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "sqldev",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:sqldev:15.0.200317",
    "version": "15.0.200317"
       },
       {
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "sqldev",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:sqldev:15.0.200512",
    "version": "15.0.200512"
       },
       {
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "standard",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:standard:15.0.200317",
    "version": "15.0.200317"
       },
       {
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "standard",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:standard:15.0.200512",
    "version": "15.0.200512"
       }
    ]
    ```

    如果您確實有使用上述其中一個映像來建立虛擬機器，則會預先安裝 SQL Server 2019。 如本文所述，跳過[安裝 SQL Server 和 mssql-tools](#install-sql-server-and-mssql-tools) 一節。
    
    
    > [!IMPORTANT]
    > 機器名稱不得超過 15 個字元，才能設定可用性群組。 使用者名稱不可包含大寫字元，且密碼必須有 12 個以上的字元。

1. 我們要在可用性設定組中建立 3 部 M。 請取代下列命令中的以下內容：

    - `<resourceGroupName>`
    - `<VM-basename>`
    - `<availabilitySetName>`
    - `<VM-Size>` - 例如 "Standard_D16_v3"
    - `<username>`
    - `<adminPassword>`

    ```azurecli-interactive
    for i in `seq 1 3`; do
           az vm create \
             --resource-group <resourceGroupName> \
             --name <VM-basename>$i \
             --availability-set <availabilitySetName> \
             --size "<VM-Size>"  \
             --image "RedHat:RHEL-HA:7.6:7.6.2019062019" \
             --admin-username "<username>" \
             --admin-password "<adminPassword>" \
             --authentication-type all \
             --generate-ssh-keys
    done
    ```

上述命令會建立 VM，並建立這些 VM 的預設 VNet。 如需不同設定的詳細資訊，請參閱 [az vm create](https://docs.microsoft.com/cli/azure/vm) 一文。

對每個 VM 完成此命令後，應該會產生如下的結果：

```output
{
  "fqdns": "",
  "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<VM1>",
  "location": "eastus2",
  "macAddress": "<Some MAC address>",
  "powerState": "VM running",
  "privateIpAddress": "<IP1>",
  "publicIpAddress": "",
  "resourceGroup": "<resourceGroupName>",
  "zones": ""
}
```

> [!IMPORTANT]
> 使用上述命令建立的預設映像，依預設會建立 32GB 的作業系統磁碟。 此預設安裝可能會耗盡可用空間。 您可以使用在上述 `az vm create` 命令中新增的參數，建立具有 128GB 的作業系統磁碟作為範例：`--os-disk-size-gb 128`。
>
> 接著，如果您需要擴充適當的資料夾磁碟區以容納您的安裝，您可以[設定邏輯磁碟區管理員 (LVM)](../../../virtual-machines/linux/configure-lvm.md)。

### <a name="test-connection-to-the-created-vms"></a>測試與已建立的 VM 之間的連線

在 Azure Cloud Shell 中使用下列命令，連線至 VM1 或其他 VM。 如果找不到您的 VM IP，請依照此 [Azure Cloud Shell 快速入門](../../../cloud-shell/quickstart.md#ssh-into-your-linux-vm)的指示操作。

```azurecli-interactive
ssh <username>@publicipaddress
```

如果連線成功，您應會看見下列表示 Linux 終端機的輸出：

```output
[<username>@<VM1> ~]$
```

輸入 `exit` 以退出 SSH 工作階段。

## <a name="enable-high-availability"></a>啟用高可用性

> [!IMPORTANT]
> 為了完成本教學課程的這個部分，您必須具有 RHEL 的訂用帳戶和高可用性附加元件。 如果您使用上一節中建議的映像，則不需要註冊另一個訂用帳戶。
 
連線至每個 VM 節點，並依照下列指南啟用 HA。 如需詳細資訊，請參閱[為 RHEL 啟用高可用性訂用帳戶](/sql/linux/sql-server-linux-availability-group-cluster-rhel#enable-the-high-availability-subscription-for-rhel)。

> [!TIP]
> 同時開啟每個 VM 的 SSH 工作階段，會比較方便作業，因為在這整篇文章中的每個 VM 上，將必須執行相同的命令。
>
> 如果您複製並貼上多個 `sudo` 命令，且系統會提示您輸入密碼，則不會執行其他命令。 請個別執行每個命令。


1. 在每個 VM 上執行下列命令，以開啟 Pacemaker 防火牆連接埠：

    ```bash
    sudo firewall-cmd --permanent --add-service=high-availability
    sudo firewall-cmd --reload
    ```

1. 使用下列命令，在所有節點上更新及安裝 Pacemaker 套件：

    > [!NOTE]
    > **nmap** 會安裝為此命令區塊中的一項工具，用以尋找您網路中可用的 IP 位址。 您不需要安裝 **nmap**，但其效用將在本教學課程稍後的步驟中彰顯出來。

    ```bash
    sudo yum update -y
    sudo yum install -y pacemaker pcs fence-agents-all resource-agents fence-agents-azure-arm nmap
    sudo reboot
    ```

1. 為安裝 Pacemaker 套件時所建立的預設使用者設定密碼。 在所有節點上使用相同的密碼。

    ```bash
    sudo passwd hacluster
    ```

1. 使用下列命令開啟 hosts 檔案，並設定主機名稱解析。 如需詳細資訊，請參閱[設定 AG](/sql/linux/sql-server-linux-availability-group-configure-ha#prerequisites) 以了解如何設定 hosts 檔案。

    ```
    sudo vi /etc/hosts
    ```

    在 **vi** 編輯器中，輸入 `i` 以插入文字，並在空白行上新增對應 VM 的**私人 IP**。 然後，在 IP 旁的空格後面新增 VM 名稱。 各行應包含個別的項目。

    ```output
    <IP1> <VM1>
    <IP2> <VM2>
    <IP3> <VM3>
    ```

    > [!IMPORTANT]
    > 建議您使用上方的**私人 IP** 位址。 在此設定中使用公用 IP 位址將導致設定失敗，而且我們不建議您將 VM 公開至外部網路。

    若要結束 **vi** 編輯器，請先按 **Esc** 鍵，然後輸入命令 `:wq` 以寫入檔案並結束。

## <a name="create-the-pacemaker-cluster"></a>建立 Pacemaker 叢集

在本節中，我們將啟用並啟動 pcsd 服務，然後設定叢集。 對於 Linux 上的 SQL Server，並不會自動建立叢集資源。 我們必須手動啟用並建立 pacemaker 資源。 如需詳細資訊，請參閱關於[為 RHEL 設定容錯移轉叢集執行個體](/sql/linux/sql-server-linux-shared-disk-cluster-red-hat-7-configure#install-and-configure-pacemaker-on-each-cluster-node)的文章

### <a name="enable-and-start-pcsd-service-and-pacemaker"></a>啟用並啟動 pcsd 服務和 Pacemaker

1. 在所有節點上執行命令。 這些命令可讓節點在重新啟動後重新加入叢集。

    ```bash
    sudo systemctl enable pcsd
    sudo systemctl start pcsd
    sudo systemctl enable pacemaker
    ``` 

1. 從所有節點移除任何現有的叢集設定。 執行以下命令：

    ```bash
    sudo pcs cluster destroy 
    sudo systemctl enable pacemaker 
    ```

1. 在主要節點上執行下列命令，以設定叢集。

    - 執行 `pcs cluster auth` 命令以驗證叢集節點時，系統會提示您輸入密碼。 請輸入先前建立之 **hacluster** 使用者的密碼。

    **RHEL7**

    ```bash
    sudo pcs cluster auth <VM1> <VM2> <VM3> -u hacluster
    sudo pcs cluster setup --name az-hacluster <VM1> <VM2> <VM3> --token 30000
    sudo pcs cluster start --all
    sudo pcs cluster enable --all
    ```

    **RHEL8**

    針對 RHEL 8，您將需要分別驗證節點。 出現提示時，請手動輸入 **hacluster** 的使用者名稱和密碼。

    ```bash
    sudo pcs host auth <node1> <node2> <node3>
    sudo pcs cluster setup <clusterName> <node1> <node2> <node3>
    sudo pcs cluster start --all
    sudo pcs cluster enable --all
    ```

1. 執行下列命令，以確認所有節點都已連線。

    ```bash
    sudo pcs status
    ```

   **RHEL 7** 
   
    如果所有節點都已連線，您將看到如下的輸出：

    ```output
    Cluster name: az-hacluster
     
    WARNINGS:
    No stonith devices and stonith-enabled is not false
     
    Stack: corosync
    Current DC: <VM2> (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
    Last updated: Fri Aug 23 18:27:57 2019
    Last change: Fri Aug 23 18:27:56 2019 by hacluster via crmd on <VM2>
     
    3 nodes configured
    0 resources configured
     
    Online: [ <VM1> <VM2> <VM3> ]
     
    No resources
     
     
    Daemon Status:
          corosync: active/enabled
          pacemaker: active/enabled
          pcsd: active/enabled
    ```
   
   **RHEL 8** 
   
    ```output
    Cluster name: az-hacluster
     
    WARNINGS:
    No stonith devices and stonith-enabled is not false
     
    Cluster Summary:
    * Stack: corosync
    * Current DC: <VM2> (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
    * Last updated: Fri Aug 23 18:27:57 2019
    * Last change: Fri Aug 23 18:27:56 2019 by hacluster via crmd on <VM2>
    * 3 nodes configured
    * 0 resource instances configured
     
   Node List:
    * Online: [ <VM1> <VM2> <VM3> ]
   
   Full List of Resources:
   * No resources
     
   Daemon Status:
          corosync: active/enabled
          pacemaker: active/enabled
          pcsd: active/enabled
    
    ```
    
1. 將即時叢集中的預期投票設定為 3。 此命令只會對即時叢集產生影響，而不會變更組態檔。

    在所有節點上，使用下列命令設定預期的投票：

    ```bash
    sudo pcs quorum expected-votes 3
    ```

## <a name="configure-the-fencing-agent"></a>設定隔離代理程式

STONITH 裝置提供隔離代理程式。 以下是針對本教學課程進行修改的指示。 如需詳細資訊，請參閱[建立 STONITH 裝置](../../../virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker.md#create-stonith-device)。
 
[檢查 Azure 隔離代理程式的版本，以確定版本已更新](../../../virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker.md#cluster-installation)。 使用下列命令：

```bash
sudo yum info fence-agents-azure-arm
```

您應該會看到類似於下列範例的輸出。

```output
Loaded plugins: langpacks, product-id, search-disabled-repos, subscription-manager
Installed Packages
Name        : fence-agents-azure-arm
Arch        : x86_64
Version     : 4.2.1
Release     : 11.el7_6.8
Size        : 28 k
Repo        : installed
From repo   : rhel-ha-for-rhel-7-server-eus-rhui-rpms
Summary     : Fence agent for Azure Resource Manager
URL         : https://github.com/ClusterLabs/fence-agents
License     : GPLv2+ and LGPLv2+
Description : The fence-agents-azure-arm package contains a fence agent for Azure instances.
```

### <a name="register-a-new-application-in-azure-active-directory"></a>在 Azure Active Directory 中註冊新的應用程式
 
 1. 移至 https://portal.azure.com 。
 2. 開啟 [Azure Active Directory 刀鋒視窗](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties)。 移至 [屬性]，並記下目錄識別碼。 這是 `tenant ID`
 3. 按一下[**應用程式註冊**](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)
 4. 按一下 [新增註冊]
 5. 輸入 `<resourceGroupName>-app` 之類的 [名稱]，然後選取 [僅限此組織目錄中的帳戶]
 6. 選取應用程式類型 [Web]，輸入登入 URL (例如 http://localhost) ，然後按一下 [新增]。 登入 URL 並未使用，而且可以是任何有效的 URL。 完成後，按一下 [註冊]
 7. 選取新應用程式註冊的 [憑證和祕密]，然後按一下 [新增用戶端密碼]
 8. 輸入新金鑰 (用戶端密碼) 的說明、選取 [永不過期]，然後按一下 [新增]
 9. 記下秘密的值。 此值用來作為服務主體的密碼
10. 選取 [概觀]。 記下應用程式識別碼。 此識別碼會作為服務主體的使用者名稱 (以下步驟中的「登入識別碼」)
 
### <a name="create-a-custom-role-for-the-fence-agent"></a>為隔離代理程式建立自訂角色

遵循教學課程來[使用 Azure CLI 建立 Azure 自訂角色](../../../role-based-access-control/tutorial-custom-role-cli.md#create-a-custom-role)。

您的 json 檔案應會顯示如下：

- 將 `<username>` 取代為您選擇的名稱。 這是為了避免在建立此角色定義時出現重複的狀況。
- 將 `<subscriptionId>` 取代為您的 Azure 訂用帳戶識別碼。

```json
{
  "Name": "Linux Fence Agent Role-<username>",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to power-off and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/powerOff/action",
    "Microsoft.Compute/virtualMachines/start/action"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscriptionId>"
  ]
}
```

若要新增角色，請執行下列命令：

- 將 `<filename>` 取代為檔案名稱。
- 如果您是從檔案儲存所在的資料夾以外的路徑執行命令，請在命令中包含檔案的資料夾路徑。

```bash
az role definition create --role-definition "<filename>.json"
```

您應該會看見下列輸出：

```output
{
  "assignableScopes": [
    "/subscriptions/<subscriptionId>"
  ],
  "description": "Allows to power-off and start virtual machines",
  "id": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/roleDefinitions/<roleNameId>",
  "name": "<roleNameId>",
  "permissions": [
    {
      "actions": [
        "Microsoft.Compute/*/read",
        "Microsoft.Compute/virtualMachines/powerOff/action",
        "Microsoft.Compute/virtualMachines/start/action"
      ],
      "dataActions": [],
      "notActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Linux Fence Agent Role-<username>",
  "roleType": "CustomRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="assign-the-custom-role-to-the-service-principal"></a>將自訂角色指派給服務主體

將在上一個步驟中建立的自訂角色 `Linux Fence Agent Role-<username>` 指派給服務主體。 不要再使用「擁有者」角色！
 
1. 移至 https://portal.azure.com 。
2. 開啟[所有資源刀鋒視窗](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAll)
3. 選取第一個叢集節點的虛擬機器
4. 按一下 [存取控制 (IAM)]
5. 按一下 [新增角色指派]
6. 從 [角色] 清單中選取角色 `Linux Fence Agent Role-<username>`
7. 在 [選取] 清單中，輸入您先前建立的應用程式名稱 `<resourceGroupName>-app`
8. 按一下 [儲存]
9. 對所有叢集節點重複上述步驟。

### <a name="create-the-stonith-devices"></a>建立 STONITH 裝置

在節點 1 上執行下列命令：

- 將 `<ApplicationID>` 取代為您的應用程式註冊中的識別碼值。
- 將 `<servicePrincipalPassword>` 取代為用戶端密碼中的值。
- 以本教學課程中所用之訂閱的資源群組取代 `<resourceGroupName>`。
- 取代您 Azure 訂用帳戶中的 `<tenantID>` 和 `<subscriptionId>`。

```bash
sudo pcs property set stonith-timeout=900
sudo pcs stonith create rsc_st_azure fence_azure_arm login="<ApplicationID>" passwd="<servicePrincipalPassword>" resourceGroup="<resourceGroupName>" tenantId="<tenantID>" subscriptionId="<subscriptionId>" power_timeout=240 pcmk_reboot_timeout=900
```

由於我們已在防火牆中新增允許 HA 服務的規則 (`--add-service=high-availability`)，因此不需要在所有節點上開啟下列防火牆連接埠：2224、3121、21064、5405。 不過，如果您的 HA 發生任何類型的連線問題，請使用下列命令開啟這些與 HA 相關聯的連接埠。

> [!TIP]
> 在本教學課程中，您可以選擇性地同時新增所有連接埠，以節省時間。 需要開啟的連接埠將在其後續的相關章節中說明。 如果您要在此時新增所有連接埠，請新增其他連接埠：1433 和 5022。

```bash
sudo firewall-cmd --zone=public --add-port=2224/tcp --add-port=3121/tcp --add-port=21064/tcp --add-port=5405/tcp --permanent
sudo firewall-cmd --reload
```

## <a name="install-sql-server-and-mssql-tools"></a>安裝 SQL Server 和 mssql-tools

> [!NOTE]
> 如果您已建立在 RHEL8-HA 上預先安裝 SQL Server 2019 的 VM，可以跳過下列安裝 SQL Server 和 mssql-tools 的步驟，並在所有 VM 上執行 `sudo /opt/mssql/bin/mssql-conf set-sa-password` 命令來設定 sa 密碼後，開始進行**設定可用性群組**區段。

依照下一節的指示，在 VM 上安裝 SQL Server 和 mssql-tools。 您可以選擇下列其中一個範例，將 SQL Server 2017 安裝在 RHEL 7，或將 SQL Server 2019 安裝在 RHEL 8 上。 請在所有節點上執行這些動作。 如需詳細資訊，請參閱[在 Red Hat VM 上安裝 SQL Server](/sql/linux/quickstart-install-connect-red-hat)。


### <a name="installing-sql-server-on-the-vms"></a>在 VM 上安裝 SQL Server

下列命令可用來安裝 SQL Server：

**RHEL 7 與 SQL Server 2017** 

```bash
sudo curl -o /etc/yum.repos.d/mssql-server.repo https://packages.microsoft.com/config/rhel/7/mssql-server-2017.repo
sudo yum install -y mssql-server
sudo /opt/mssql/bin/mssql-conf setup
sudo yum install mssql-server-ha
```

**RHEL 8 與 SQL Server 2019** 

```bash
sudo curl -o /etc/yum.repos.d/mssql-server.repo https://packages.microsoft.com/config/rhel/8/mssql-server-2019.repo
sudo yum install -y mssql-server
sudo /opt/mssql/bin/mssql-conf setup
sudo yum install mssql-server-ha
```
### <a name="open-firewall-port-1433-for-remote-connections"></a>開啟防火牆連接埠 1433 以進行遠端連線

您必須在 VM 上開啟連接埠 1433，才能從遠端連線。 請使用下列命令，在每個 VM 的防火牆中開啟連接埠 1433：

```bash
sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
sudo firewall-cmd --reload
```

### <a name="installing-sql-server-command-line-tools"></a>安裝 SQL Server 命令列工具

下列命令可用來安裝 SQL Server 命令列工具。 如需詳細資訊，請參閱[安裝 SQL Server 命令列工具](/sql/linux/quickstart-install-connect-red-hat#tools)。

**RHEL 7** 

```bash
sudo curl -o /etc/yum.repos.d/msprod.repo https://packages.microsoft.com/config/rhel/7/prod.repo
sudo yum install -y mssql-tools unixODBC-devel
```

**RHEL 8** 

```bash
sudo curl -o /etc/yum.repos.d/msprod.repo https://packages.microsoft.com/config/rhel/8/prod.repo
sudo yum install -y mssql-tools unixODBC-devel
```
 
> [!NOTE] 
> 為方便起見，請將 /opt/mssql-tools/bin/ 新增至您的 PATH 環境變數。 這可讓您不需要指定完整路徑，即可執行工具。 執行下列命令，以修改登入工作階段和互動式/非登入工作階段的 PATH：</br></br>
`echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile`</br>
`echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc`</br>
`source ~/.bashrc`


### <a name="check-the-status-of-the-sql-server"></a>檢查 SQL Server 的狀態

完成設定後，您可以檢查 SQL Server 的狀態，並確認伺服器正在執行中：

```bash
systemctl status mssql-server --no-pager
```

您應該會看見下列輸出：

```output
● mssql-server.service - Microsoft SQL Server Database Engine
   Loaded: loaded (/usr/lib/systemd/system/mssql-server.service; enabled; vendor preset: disabled)
   Active: active (running) since Thu 2019-12-05 17:30:55 UTC; 20min ago
     Docs: https://docs.microsoft.com/en-us/sql/linux
 Main PID: 11612 (sqlservr)
   CGroup: /system.slice/mssql-server.service
           ├─11612 /opt/mssql/bin/sqlservr
           └─11640 /opt/mssql/bin/sqlservr
```

## <a name="configure-an-availability-group"></a>設定可用性群組

使用下列步驟，為您的 VM 設定 SQL Server Always On 可用性群組。 如需詳細資訊，請參閱[在 Linux 上設定 SQL Server Always On 可用性群組，以達高可用性](/sql/linux/sql-server-linux-availability-group-configure-ha)

### <a name="enable-always-on-availability-groups-and-restart-mssql-server"></a>啟用 AlwaysOn 可用性群組並重新啟動 mssql-server

在每部裝載 SQL Server 執行個體的節點上，啟用 AlwaysOn 可用性群組。 然後，重新啟動 mssql-server。 執行下列指令碼：

```
sudo /opt/mssql/bin/mssql-conf set hadr.hadrenabled 1
sudo systemctl restart mssql-server
```

### <a name="create-a-certificate"></a>建立憑證

我們目前不支援對 AG 端點進行 AD 驗證。 因此，我們必須使用憑證為 AG 端點加密。

1. 使用 SQL Server Management Studio (SSMS) 或 SQL CMD 連線至**所有節點**。 執行下列命令，以啟用 AlwaysOn_health 工作階段，並建立主要金鑰：

    > [!IMPORTANT]
    > 如果您是從遠端連線至 SQL Server 執行個體，則必須在防火牆上開啟連接埠 1433。 您也必須在每個 VM 的 NSG 中，允許對連接埠 1433 的輸入連線。 如需詳細資訊，請參閱[建立安全性規則](../../../virtual-network/manage-network-security-group.md#create-a-security-rule)，以了解如何建立輸入安全性規則。

    - 將 `<Master_Key_Password>` 取代為您自己的密碼。


    ```sql
    ALTER EVENT SESSION  AlwaysOn_health ON SERVER WITH (STARTUP_STATE=ON);
    GO
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Master_Key_Password>';
    ```

 
1. 使用 SSMS 或 SQL CMD 連線至主要複本。 下列命令會在您主要 SQL Server 複本的 `/var/opt/mssql/data/dbm_certificate.cer` 上建立憑證，並在 `var/opt/mssql/data/dbm_certificate.pvk` 上建立私密金鑰：

    - 將 `<Private_Key_Password>` 取代為您自己的密碼。
    
    ```sql
    CREATE CERTIFICATE dbm_certificate WITH SUBJECT = 'dbm';
    GO
    
    BACKUP CERTIFICATE dbm_certificate
       TO FILE = '/var/opt/mssql/data/dbm_certificate.cer'
       WITH PRIVATE KEY (
               FILE = '/var/opt/mssql/data/dbm_certificate.pvk',
               ENCRYPTION BY PASSWORD = '<Private_Key_Password>'
           );
    GO
    ```

執行 `exit` 命令以結束 SQL CMD 工作階段，然後回到您的 SSH 工作階段。
 
### <a name="copy-the-certificate-to-the-secondary-replicas-and-create-the-certificates-on-the-server"></a>將憑證複製到次要複本並在伺服器上建立憑證

1. 將先前建立的兩個檔案複製到將裝載可用性複本的所有伺服器上的相同位置。
 
    在主要伺服器上執行下列 `scp` 命令，將憑證複製到目標伺服器：

    - 將 `<username>` 和 `<VM2>` 取代為您所使用的使用者名稱和目標 VM 名稱。
    - 對所有次要複本執行此命令。

    > [!NOTE]
    > 您不需要執行為您提供根環境的 `sudo -i`。 您可以直接在每個命令前面執行 `sudo` 命令，如同我們先前在本教學課程中所做的一樣。

    ```bash
    # The below command allows you to run commands in the root environment
    sudo -i
    ```

    ```bash
    scp /var/opt/mssql/data/dbm_certificate.* <username>@<VM2>:/home/<username>
    ```

1. 在目標伺服器上，執行下列命令：

    - 將 `<username>` 取代為您的使用者名稱。
    - `mv` 命令會將檔案或目錄移至另一個位置。
    - `chown` 命令可用來變更檔案、目錄或連結的擁有者和群組。
    - 對所有次要複本執行這些命令。

    ```bash
    sudo -i
    mv /home/<username>/dbm_certificate.* /var/opt/mssql/data/
    cd /var/opt/mssql/data
    chown mssql:mssql dbm_certificate.*
    ```

1. 下列 Transact-SQL 指令碼會從您在 SQL Server 主要複本上建立的備份建立憑證。 請以強式密碼更新指令碼。 解密密碼與您在上一個步驟中用來建立 .pvk 檔案的密碼相同。 若要建立憑證，請使用 SQL CMD 或 SSMS 在所有次要伺服器上執行下列指令碼：

    ```sql
    CREATE CERTIFICATE dbm_certificate
        FROM FILE = '/var/opt/mssql/data/dbm_certificate.cer'
        WITH PRIVATE KEY (
        FILE = '/var/opt/mssql/data/dbm_certificate.pvk',
        DECRYPTION BY PASSWORD = '<Private_Key_Password>'
                );
    GO
    ```

### <a name="create-the-database-mirroring-endpoints-on-all-replicas"></a>在所有複本上建立資料庫鏡像端點

使用 SQL CMD 或 SSMS，在所有 SQL Server 執行個體上，執行下列指令碼：

```sql
CREATE ENDPOINT [Hadr_endpoint]
    AS TCP (LISTENER_PORT = 5022)
    FOR DATABASE_MIRRORING (
    ROLE = ALL,
    AUTHENTICATION = CERTIFICATE dbm_certificate,
ENCRYPTION = REQUIRED ALGORITHM AES
);
GO

ALTER ENDPOINT [Hadr_endpoint] STATE = STARTED;
GO
```

### <a name="create-the-availability-group"></a>建立可用性群組

使用 SQL CMD 或 SSMS，連線至裝載主要複本的 SQL Server 執行個體。 執行下列命令，以建立可用性群組：

- 將 `ag1` 取代為您所需的可用性群組名稱。
- 將 `<VM1>`、`<VM2>` 和 `<VM3>` 值取代為裝載複本的 SQL Server 執行個體的名稱。

```sql
CREATE AVAILABILITY GROUP [ag1]
     WITH (DB_FAILOVER = ON, CLUSTER_TYPE = EXTERNAL)
     FOR REPLICA ON
         N'<VM1>'
          WITH (
             ENDPOINT_URL = N'tcp://<VM1>:5022',
             AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
             FAILOVER_MODE = EXTERNAL,
             SEEDING_MODE = AUTOMATIC
             ),
         N'<VM2>'
          WITH (
             ENDPOINT_URL = N'tcp://<VM2>:5022',
             AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
             FAILOVER_MODE = EXTERNAL,
             SEEDING_MODE = AUTOMATIC
             ),
         N'<VM3>'
         WITH(
            ENDPOINT_URL = N'tcp://<VM3>:5022',
            AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
            FAILOVER_MODE = EXTERNAL,
            SEEDING_MODE = AUTOMATIC
            );
GO

ALTER AVAILABILITY GROUP [ag1] GRANT CREATE ANY DATABASE;
GO
```

### <a name="create-a-sql-server-login-for-pacemaker"></a>為 Pacemaker 建立 SQL Server 登入

在所有 SQL Server 執行個體上，建立 Pacemaker 的 SQL Server 登入。 下列 Transact-SQL 會建立登入。

- 將 `<password>` 取代為您自己的複雜密碼。

```sql
USE [master]
GO

CREATE LOGIN [pacemakerLogin] with PASSWORD= N'<password>';
GO

ALTER SERVER ROLE [sysadmin] ADD MEMBER [pacemakerLogin];
GO
```

在所有 SQL Server 執行個體上，儲存 SQL Server 登入所使用的認證。 

1. 建立檔案：

    ```bash
    sudo vi /var/opt/mssql/secrets/passwd
    ```

1. 將以下這 2 行新增至該檔案：

    ```bash
    pacemakerLogin
    <password>
    ```

    若要結束 **vi** 編輯器，請先按 **Esc** 鍵，然後輸入命令 `:wq` 以寫入檔案並結束。

1. 使該檔案只能由 root 讀取：

    ```bash
    sudo chown root:root /var/opt/mssql/secrets/passwd
    sudo chmod 400 /var/opt/mssql/secrets/passwd
    ```

### <a name="join-secondary-replicas-to-the-availability-group"></a>將次要複本聯結至可用性群組

1. 為了將次要複本聯結至 AG，您必須在所有伺服器的防火牆上開啟連接埠 5022。 在 SSH 工作階段中執行下列命令：

    ```bash
    sudo firewall-cmd --zone=public --add-port=5022/tcp --permanent
    sudo firewall-cmd --reload
    ```

1. 在次要複本上執行下列命令，以將其聯結至 AG：

    ```sql
    ALTER AVAILABILITY GROUP [ag1] JOIN WITH (CLUSTER_TYPE = EXTERNAL);
    GO

    ALTER AVAILABILITY GROUP [ag1] GRANT CREATE ANY DATABASE;
    GO
    ```

1. 對主要複本與每個次要複本，執行下列 Transact-SQL 指令碼：

    ```sql
    GRANT ALTER, CONTROL, VIEW DEFINITION ON AVAILABILITY GROUP::ag1 TO pacemakerLogin;
    GO
    
    GRANT VIEW SERVER STATE TO pacemakerLogin;
    GO
    ```

1. 聯結次要複本後，您可以在 SSMS 物件總管中加以檢視，方法是展開 **Always On 高可用性**節點：

    ![此螢幕擷取畫面顯示主要和次要可用性複本。](./media/rhel-high-availability-stonith-tutorial/availability-group-joined.png)

### <a name="add-a-database-to-the-availability-group"></a>將資料庫新增至可用性群組

我們將依照[設定可用性群組一文中的指示新增資料庫](/sql/linux/sql-server-linux-availability-group-configure-ha#add-a-database-to-the-availability-group)。

此步驟使用下列 Transact-SQL 命令。 在主要複本上執行下列命令：

```sql
CREATE DATABASE [db1]; -- creates a database named db1
GO

ALTER DATABASE [db1] SET RECOVERY FULL; -- set the database in full recovery mode
GO

BACKUP DATABASE [db1] -- backs up the database to disk
   TO DISK = N'/var/opt/mssql/data/db1.bak';
GO

ALTER AVAILABILITY GROUP [ag1] ADD DATABASE [db1]; -- adds the database db1 to the AG
GO
```

### <a name="verify-that-the-database-is-created-on-the-secondary-servers"></a>確認已在次要伺服器上建立資料庫

在每個 SQL Server 次要複本上執行下列查詢，以確認 db1 資料庫是否已建立並處於 SYNCHRONIZED 狀態：

```
SELECT * FROM sys.databases WHERE name = 'db1';
GO
SELECT DB_NAME(database_id) AS 'database', synchronization_state_desc FROM sys.dm_hadr_database_replica_states;
```

若 `db1` 的 `synchronization_state_desc` 列示 [已同步]，表示複本已同步。 次要複本會在主要複本中顯示 `db1`。

## <a name="create-availability-group-resources-in-the-pacemaker-cluster"></a>在 Pacemaker 叢集中建立可用性群組資源

我們將依照指南[在 Pacemaker 叢集中建立可用性群組資源](/sql/linux/sql-server-linux-create-availability-group#create-the-availability-group-resources-in-the-pacemaker-cluster-external-only)。

### <a name="create-the-ag-cluster-resource"></a>建立 AG 叢集資源

1. 根據稍早選擇的環境，使用下列其中一個命令，在可用性群組 `ag1` 中建立資源 `ag_cluster`。

    **RHEL 7**
  
    ```bash
    sudo pcs resource create ag_cluster ocf:mssql:ag ag_name=ag1 meta failure-timeout=30s master notify=true
    ```

    **RHEL 8**
  
    ```bash
    sudo pcs resource create ag_cluster ocf:mssql:ag ag_name=ag1 meta failure-timeout=30s promotable notify=true
    ```

2. 檢查並確定您的資源已連線，然後再繼續使用下列命令：

    ```bash
    sudo pcs resource
    ```

    您應該會看見下列輸出：
    
    **RHEL 7** 
    
    ```output
    [<username>@VM1 ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
    Masters: [ <VM1> ]
    Slaves: [ <VM2> <VM3> ]
    ```
    
    **RHEL 8** 
    
    ```output
    [<username>@VM1 ~]$ sudo pcs resource
    * Clone Set: ag_cluster-clone [ag_cluster] (promotable):
    * ag_cluster             (ocf::mssql:ag) :            Slave VMrhel3 (Monitoring) 
    * ag_cluster             (ocf::mssql:ag) :            Master VMrhel1 (Monitoring)
    * ag_cluster             (ocf::mssql:ag) :            Slave VMrhel2 (Monitoring)
    ```


### <a name="create-a-virtual-ip-resource"></a>建立虛擬 IP 資源

1. 使用網路中可用的靜態 IP 位址建立虛擬 IP 資源。 您可以使用命令工具 `nmap` 來尋找位址。

    ```bash
    nmap -sP <IPRange>
    # For example: nmap -sP 10.0.0.*
    # The above will scan for all IP addresses that are already occupied in the 10.0.0.x space.
    ```

2. 將 **stonith-enabled** 屬性設定為 false

    ```bash
    sudo pcs property set stonith-enabled=false
    ```

3. 使用下列命令建立虛擬 IP 資源：

    - 下方的 `<availableIP>` 值取代為未使用的 IP 位址。

    ```bash
    sudo pcs resource create virtualip ocf:heartbeat:IPaddr2 ip=<availableIP>
    ```

### <a name="add-constraints"></a>新增限制式

1. 為了確保 IP 位址和 AG 資源在相同的節點上執行，必須設定共置限制式。 執行以下命令：

   **RHEL 7**
  
    ```bash
    sudo pcs constraint colocation add virtualip ag_cluster-master INFINITY with-rsc-role=Master
    ```

   **RHEL 8**
   
    ```bash
     sudo pcs constraint colocation add virtualip with master ag_cluster-clone INFINITY with-rsc-role=Master
    ```
  
2. 建立排序限制式，以確保 AG 資源會在 IP 位址之前啟動並執行。 雖然共置限制式隱含排序限制式，但此步驟會強制執行它。

   **RHEL 7**
   
    ```bash
    sudo pcs constraint order promote ag_cluster-master then start virtualip
    ```

   **RHEL 8**
   
    ```bash
    sudo pcs constraint order promote ag_cluster-clone then start virtualip
    ```
  
3. 若要驗證限制式，請執行下列命令：

    ```bash
    sudo pcs constraint list --full
    ```

    您應該會看見下列輸出：
    
    **RHEL 7**

    ```
    Location Constraints:
    Ordering Constraints:
          promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
    Colocation Constraints:
          virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```
    
    **RHEL 8**
    
    ```output
    Location Constraints:
    Ordering Constraints:
            promote ag_cluster-clone then start virtualip (kind:Mandatory) (id:order-ag_cluster-clone-virtualip-mandatory)
    Colocation Constraints:
            virtualip with ag_cluster-clone (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-clone-INFINITY)
    Ticket Constraints:
    ```

### <a name="re-enable-stonith"></a>重新啟用 stonith

我們已做好測試準備。 在節點 1 上執行下列命令，以在叢集中重新啟用 stonith：

```bash
sudo pcs property set stonith-enabled=true
```

### <a name="check-cluster-status"></a>檢查叢集狀態

您可以使用下列命令來檢查叢集資源的狀態：

```output
[<username>@VM1 ~]$ sudo pcs status
Cluster name: az-hacluster
Stack: corosync
Current DC: <VM3> (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
Last updated: Sat Dec  7 00:18:38 2019
Last change: Sat Dec  7 00:18:02 2019 by root via cibadmin on VM1

3 nodes configured
5 resources configured

Online: [ <VM1> <VM2> <VM3> ]

Full list of resources:

 Master/Slave Set: ag_cluster-master [ag_cluster]
     Masters: [ <VM2> ]
     Slaves: [ <VM1> <VM3> ]
 virtualip      (ocf::heartbeat:IPaddr2):       Started <VM2>
 rsc_st_azure   (stonith:fence_azure_arm):      Started <VM1>

Daemon Status:
  corosync: active/enabled
  pacemaker: active/enabled
  pcsd: active/enabled
```

## <a name="test-failover"></a>測試容錯移轉

為了確保設定到目前為止均順利進行，我們將測試容錯移轉。 如需詳細資訊，請參閱 [Linux 上的 Always On 可用性群組容錯移轉](/sql/linux/sql-server-linux-availability-group-failover-ha)。

1. 執行下列命令，手動將主要複本容錯移轉至 `<VM2>`。 將 `<VM2>` 取代為您伺服器名稱的值。

   **RHEL 7**
   
    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master
    ```

   **RHEL 8**
   
    ```bash
    sudo pcs resource move ag_cluster-clone <VM2> --master
    ```

2. 如果您再次檢查限制式，您會看到因為手動容錯移轉而新增了另一個限制式：
    
    **RHEL 7**
    
    ```output
    [<username>@VM1 ~]$ sudo pcs constraint list --full
    Location Constraints:
          Resource: ag_cluster-master
            Enabled on: VM2 (score:INFINITY) (role: Master) (id:cli-prefer-ag_cluster-master)
    Ordering Constraints:
            promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
    Colocation Constraints:
            virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```

    **RHEL 8**
    
    ```output
    [<username>@VM1 ~]$ sudo pcs constraint list --full
    Location Constraints:
          Resource: ag_cluster-master
            Enabled on: VM2 (score:INFINITY) (role: Master) (id:cli-prefer-ag_cluster-clone)
    Ordering Constraints:
            promote ag_cluster-clone then start virtualip (kind:Mandatory) (id:order-ag_cluster-clone-virtualip-mandatory)
    Colocation Constraints:
            virtualip with ag_cluster-clone (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-clone-INFINITY)
    Ticket Constraints:
    ```
    
3. 使用下列命令，移除識別碼為 `cli-prefer-ag_cluster-master` 的限制式：

    **RHEL 7**
    
    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-master
    ```

    **RHEL 8**
    
    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-clone
    ```

1. 使用命令 `sudo pcs resource` 檢查您的叢集資源，您應該會看到此時的主要執行個體為 `<VM2>`。

    ```output
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
         ag_cluster (ocf::mssql:ag):        FAILED <VM1> (Monitoring)
         Masters: [ <VM2> ]
         Slaves: [ <VM3> ]
    virtualip      (ocf::heartbeat:IPaddr2):       Started <VM2>
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
         Masters: [ <VM2> ]
         Slaves: [ <VM1> <VM3> ]
    virtualip      (ocf::heartbeat:IPaddr2):       Started <VM2>
    ```

## <a name="test-fencing"></a>測試隔離

您可以執行下列命令來測試 STONITH。 請嘗試對 `<VM3>` 執行以下來自 `<VM1>` 的命令。

```bash
sudo pcs stonith fence <VM3> --debug
```

> [!NOTE]
> 根據預設，隔離動作會先將節點關閉，然後再開啟。 如果您只想讓節點離線，請在命令中使用選項 `--off`。

您應該會取得下列輸出：

```output
[<username>@<VM1> ~]$ sudo pcs stonith fence <VM3> --debug
Running: stonith_admin -B <VM3>
Return Value: 0
--Debug Output Start--
--Debug Output End--
 
Node: <VM3> fenced
```
如需測試隔離裝置的詳細資訊，請參閱下列 [Red Hat](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/s1-stonithtest-haar) 文章。

## <a name="next-steps"></a>後續步驟

若要對您的 SQL Server 執行個體使用可用性群組接聽程式，必須建立及設定負載平衡器。

> [!div class="nextstepaction"]
> [教學課程：為 Azure 中 RHEL 虛擬機器上的 SQL Server 設定可用性群組接聽程式](rhel-high-availability-listener-tutorial.md)

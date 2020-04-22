---
title: 在 Azure 中排除 OpenShift 容器平臺 3.11 部署的疑難排解
description: 在 Azure 中排除 OpenShift 容器平臺 3.11 部署的疑難排解。
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 90fd3680cfdc4ecd1dcb0ce33b63f8d76dd8bfae
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759477"
---
# <a name="troubleshoot-openshift-container-platform-311-deployment-in-azure"></a>在 Azure 中排除 OpenShift 容器平臺 3.11 部署的疑難排解

如果 OpenShift 叢集部署不成功，則 Azure 入口網站會提供錯誤輸出。 輸出可能難以讀取，以致難以找出問題。 快速掃描此輸出中是否有結束代碼 3、4 或 5。 以下提供這三個結束代碼的資訊：

- 結束代碼 3：您的 Red Hat 訂用帳戶使用者名稱 / 密碼或組織識別碼 / 啟用金鑰不正確
- 結束代碼 4：您的 Red Hat 集區識別碼不正確，或沒有可用的權利
- 結束代碼 5：無法佈建 Docker 精簡集區磁碟區

對於所有其他結束代碼，透過 ssh 連線到主機以檢視記錄檔。

**OpenShift Container Platform 3.11**

透過 SSH 連線到 Ansible 腳本主機。 對於範本或 Marketplace 供應項目，請使用防禦主機。 從防禦主機，您可以透過 SSH 連線到叢集中的所有其他節點 (主要、基礎、CNS、計算)。 您必須是 Root 使用者才能檢視記錄檔。 根據預設，已針對 SSH 存取停用 Root 使用者，因此請勿使用 Root 身分透過 SSH 連線到其他節點。

**OKD**

透過 SSH 連線到 Ansible 腳本主機。 在 OKD 範本 (3.9 版及更早版本) 中，使用 master-0 主機。 在 OKD 範本 (3.10 版及更新版本) 中，使用防禦主機。 從 Ansible 腳本主機，您可以透過 SSH 連線到叢集中的所有其他節點 (主要、基礎、CNS、計算)。 您必須是 Root (sudo su -) 使用者才能檢視記錄檔。 根據預設，已針對 SSH 存取停用 Root 使用者，因此請勿使用 Root 身分透過 SSH 連線到其他節點。

## <a name="log-files"></a>記錄檔

主機準備腳本的日誌檔(斯特和斯特和斯特處理)位於所有主機`/var/lib/waagent/custom-script/download/0`上。 如果在準備主機期間發生錯誤，請檢視這些記錄檔以判斷錯誤。

如果準備文本成功運行,則需要檢查易存取操作手冊主`/var/lib/waagent/custom-script/download/1`機 目錄中的日誌檔。 如果在 OpenShift 實際安裝期間發生錯誤，則 stdout 檔案會顯示錯誤。 使用此資訊來連絡支援人員，以尋求進一步的協助。

範例輸出

```json
TASK [openshift_storage_glusterfs : Load heketi topology] **********************
fatal: [mycluster-master-0]: FAILED! => {"changed": true, "cmd": ["oc", "--config=/tmp/openshift-glusterfs-ansible-IbhnUM/admin.kubeconfig", "rsh", "--namespace=glusterfs", "deploy-heketi-storage-1-d9xl5", "heketi-cli", "-s", "http://localhost:8080", "--user", "admin", "--secret", "VuoJURT0/96E42Vv8+XHfsFpSS8R20rH1OiMs3OqARQ=", "topology", "load", "--json=/tmp/openshift-glusterfs-ansible-IbhnUM/topology.json", "2>&1"], "delta": "0:00:21.477831", "end": "2018-05-20 02:49:11.912899", "failed": true, "failed_when_result": true, "rc": 0, "start": "2018-05-20 02:48:50.435068", "stderr": "", "stderr_lines": [], "stdout": "Creating cluster ... ID: 794b285745b1c5d7089e1c5729ec7cd2\n\tAllowing file volumes on cluster.\n\tAllowing block volumes on cluster.\n\tCreating node mycluster-cns-0 ... ID: 45f1a3bfc20a4196e59ebb567e0e02b4\n\t\tAdding device /dev/sdd ... OK\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdf ... OK\n\tCreating node mycluster-cns-1 ... ID: 596f80d7bbd78a1ea548930f23135131\n\t\tAdding device /dev/sdc ... Unable to add device: Unable to execute command on glusterfs-storage-4zc42:   Device /dev/sdc excluded by a filter.\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdd ... OK\n\tCreating node mycluster-cns-2 ... ID: 42c0170aa2799559747622acceba2e3f\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdf ... OK\n\t\tAdding device /dev/sdd ... OK", "stdout_lines": ["Creating cluster ... ID: 794b285745b1c5d7089e1c5729ec7cd2", "\tAllowing file volumes on cluster.", "\tAllowing block volumes on cluster.", "\tCreating node mycluster-cns-0 ... ID: 45f1a3bfc20a4196e59ebb567e0e02b4", "\t\tAdding device /dev/sdd ... OK", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdf ... OK", "\tCreating node mycluster-cns-1 ... ID: 596f80d7bbd78a1ea548930f23135131", "\t\tAdding device /dev/sdc ... Unable to add device: Unable to execute command on glusterfs-storage-4zc42:   Device /dev/sdc excluded by a filter.", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdd ... OK", "\tCreating node mycluster-cns-2 ... ID: 42c0170aa2799559747622acceba2e3f", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdf ... OK", "\t\tAdding device /dev/sdd ... OK"]}

PLAY RECAP *********************************************************************
mycluster-cns-0       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-cns-1       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-cns-2       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-infra-0     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-infra-1     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-infra-2     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-master-0    : ok=502  changed=198  unreachable=0    failed=1   
mycluster-master-1    : ok=348  changed=140  unreachable=0    failed=0   
mycluster-master-2    : ok=348  changed=140  unreachable=0    failed=0   
mycluster-node-0      : ok=143  changed=55   unreachable=0    failed=0   
mycluster-node-1      : ok=143  changed=55   unreachable=0    failed=0   
localhost                  : ok=13   changed=0    unreachable=0    failed=0   

INSTALLER STATUS ***************************************************************
Initialization             : Complete (0:00:39)
Health Check               : Complete (0:00:24)
etcd Install               : Complete (0:01:24)
Master Install             : Complete (0:14:59)
Master Additional Install  : Complete (0:01:10)
Node Install               : Complete (0:10:58)
GlusterFS Install          : In Progress (0:03:33)
    This phase can be restarted by running: playbooks/openshift-glusterfs/config.yml

Failure summary:

  1. Hosts:    mycluster-master-0
     Play:     Configure GlusterFS
     Task:     Load heketi topology
     Message:  Failed without returning a message.
```

安裝期間最常見的錯誤如下：

1. 私密金鑰具有複雜密碼
2. 未正確建立具有私密金鑰的金鑰保存庫密碼
3. 輸入的服務主體認證不正確
4. 服務主體沒有資源群組的參與者存取權

### <a name="private-key-has-a-passphrase"></a>私密金鑰具有複雜密碼

您將看到一個錯誤,該錯誤是 ssh 的許可權被拒絕。 ssh 到可存取播放簿主機,以檢查私鑰上的密碼。

### <a name="key-vault-secret-with-private-key-wasnt-created-correctly"></a>未正確建立具有私密金鑰的金鑰保存庫密碼

私密金鑰被複製到可理解的玩法主機 -*/.ssh/id_rsa。 確認此檔案正確無誤。 從 Ansible 腳本主機，將 SSH 工作階段開啟至其中一個叢集節點進行測試。

### <a name="service-principal-credentials-were-entered-incorrectly"></a>輸入的服務主體認證不正確

對範本或 Marketplace 供應項目提供輸入時，提供的資訊不正確。 請確定您對服務主體使用正確的 appId (clientId) 和密碼 (clientSecret)。 發佈下列 azure cli 命令進行確認。

```azurecli
az login --service-principal -u <client id> -p <client secret> -t <tenant id>
```

### <a name="service-principal-doesnt-have-contributor-access-to-the-resource-group"></a>服務主體沒有資源群組的參與者存取權

如果已啟用 Azure 雲端提供者，則使用的服務主體必須具有資源群組的參與者存取權。 發佈下列 azure cli 命令進行確認。

```azurecli
az group update -g <openshift resource group> --set tags.sptest=test
```

## <a name="additional-tools"></a>其他工具

對於某些錯誤，您也可以使用下列命令來取得詳細資訊：

1. 系統狀態\<服務>
2. journalctl -xe

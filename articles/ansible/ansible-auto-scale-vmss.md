---
title: 教學課程 - 使用 Ansible 自動調整 Azure 中的虛擬機器擴展集
description: 了解如何使用 Ansible 與自動調整來調整 Azure 中的虛擬機器擴展集
keywords: ansible, azure, devops, bash, 劇本, 調整, 自動調整, 虛擬機器, 虛擬機器擴展集, vmss
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: fb8d2a4bfca32be4575ca8f11018e5cab17cd9a2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "74156825"
---
# <a name="tutorial-autoscale-virtual-machine-scale-sets-in-azure-using-ansible"></a>教學課程：使用 Ansible 自動調整 Azure 中的虛擬機器擴展集

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

自動調整 VM 執行個體數目的功能，稱為[自動調整](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview)。 自動調整的優點在於，它能夠降低監視和最佳化應用程式效能的管理負荷。 您可以依據需求或以定義的排程來設定自動調整。 使用 Ansible 可指定自動調整規則，以針對正面客戶體驗定義可接受的效能水準。

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * 定義自動調整設定檔
> * 根據週期性排程自動調整
> * 根據應用程式效能自動調整
> * 擷取自動調整設定資訊 
> * 停用自動調整設定

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 
[!INCLUDE [ansible-prereqs-vm-scale-set.md](../../includes/ansible-prereqs-vm-scale-set.md)]

## <a name="autoscale-based-on-a-schedule"></a>根據排程自動調整

若要對擴展集啟用自動調整，您必須先定義自動調整設定檔。 此設定檔會定義預設、最小和最大擴展集容量。 這些限制可讓您藉由不繼續建立 VM 執行個體來控制成本，並且在可接受的效能與保留在相應縮小事件中的執行個體數目下限之間取得平衡。 

Ansible 可讓您調整特定日期或週期性排程的擴展集。

本節中的劇本程式碼會在每週一的 10:00 將 VM 執行個體數目增加為三個。

請下列腳本儲存為 `vmss-auto-scale.yml`：

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
    name: autoscalesetting
  tasks: 
    - name: Create autoscaling
      azure_rm_autoscale:
         resource_group: "{{ resource_group }}"
         name: "{{ name }}"
         target:
           namespace: "Microsoft.Compute"
           types: "virtualMachineScaleSets"
           name: "{{ vmss_name }}"
         enabled: true
         profiles:
         - count: '3'
           min_count: '3'
           max_count: '3'
           name: Auto created scale condition
           recurrence_timezone: Pacific Standard Time
           recurrence_frequency: Week
           recurrence_days:
              - Monday
           recurrence_mins:
              - '0'
           recurrence_hours:
              - '10'
```

使用 `ansible-playbook` 命令執行劇本：

```bash
ansible-playbook vmss-auto-scale.yml
```

## <a name="autoscale-based-on-performance-data"></a>根據效能資料自動調整

如果您的應用程式需求增加，您擴展集內 VM 執行個體上的負載也會跟著增加。 如果這樣的負載增加會持續而非只是短暫的需求，您就可以設定自動調整規則來增加擴展集中的 VM 執行個體數目。 建立這些 VM 執行個體並部署應用程式後，擴展集就會開始透過負載平衡器將流量分散給它們。 Ansible 可讓您控制要監視哪些計量，例如 CPU 使用量、磁碟使用量和應用程式載入時間。 您可以根據效能標準臨界值、週期性排程或特定日期，對擴展集進行縮減和擴增。 

本節中的劇本程式碼會在每週一的 18:00 檢查過去 10 分鐘的 CPU 工作負載。 

根據 CPU 百分比計量，劇本會執行下列其中一個動作：

- 將 VM 執行個體數目相應放大至四個
- 將 VM 執行個體數目相應縮小為一個

請下列腳本儲存為 `vmss-auto-scale-metrics.yml`：

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
    name: autoscalesetting
  tasks:
  - name: Get facts of the resource group
    azure_rm_resourcegroup_facts:
      name: "{{ resource_group }}"
    register: rg

  - name: Get scale set resource uri
    set_fact:
      vmss_id: "{{ rg.ansible_facts.azure_resourcegroups[0].id }}/providers/Microsoft.Compute/virtualMachineScaleSets/{{ vmss_name }}"
    
  - name: Create autoscaling
    azure_rm_autoscale:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      target: "{{ vmss_id }}"
      enabled: true
      profiles:
      - count: '1'
        max_count: '1'
        min_count: '1'
        name: 'This scale condition is executed when none of the other scale condition(s) match'
        recurrence_days:
          - Monday
        recurrence_frequency: Week
        recurrence_hours:
          - 18
        recurrence_mins:
          - 0
        recurrence_timezone: Pacific Standard Time
      - count: '1'
        min_count: '1'
        max_count: '4'
        name: Auto created scale condition
        recurrence_days:
          - Monday
        recurrence_frequency: Week
        recurrence_hours:
          - 18
        recurrence_mins:
          - 0
        recurrence_timezone: Pacific Standard Time
        rules:
          - cooldown: 5
            direction: Increase
            metric_name: Percentage CPU
            metric_resource_uri: "{{ vmss_id }}"
            operator: GreaterThan
            statistic: Average
            threshold: 70
            time_aggregation: Average
            time_grain: 1
            time_window: 10
            type: ChangeCount
            value: '1'
          - cooldown: 5
            direction: Decrease
            metric_name: Percentage CPU
            metric_resource_uri: "{{ vmss_id }}"
            operator: LessThan
            statistic: Average
            threshold: 30
            time_aggregation: Average
            time_grain: 1
            time_window: 10
            type: ChangeCount
            value: '1'
```

使用 `ansible-playbook` 命令執行劇本：

```bash
ansible-playbook vmss-auto-scale-metrics.yml
```

## <a name="get-autoscale-settings-information"></a>取得自動調整設定資訊 

本節中的劇本程式碼會使用 `azure_rm_autoscale_facts` 模組來擷取自動調整設定的詳細資料。

請下列腳本儲存為 `vmss-auto-scale-get-settings.yml`：

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Retrieve autoscale settings information
      azure_rm_autoscale_facts:
        resource_group: "{{ resource_group }}"
        name: "{{ name }}"
      register: autoscale_query
    
    - debug:
        var: autoscale_query.autoscales[0]
```

使用 `ansible-playbook` 命令執行劇本：

```bash
ansible-playbook vmss-auto-scale-get-settings.yml
```

## <a name="disable-autoscale-settings"></a>停用自動調整設定

有兩種方式可停用自動調整設定。 其一是將 `enabled` 索引鍵從 `true` 變更為 `false`。 第二種方式是刪除該設定。

本節中的劇本程式碼會刪除自動調整設定。 

請下列腳本儲存為 `vmss-auto-scale-delete-setting.yml`：

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Delete autoscaling
      azure_rm_autoscale:
         resource_group: "{{ resource_group }}"
         name: "{{ name }}"
         state: absent
```

使用 `ansible-playbook` 命令執行劇本：

```bash
vmss-auto-scale-delete-setting.yml
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"] 
> [教學課程：使用 Ansible 更新 Azure 虛擬機器擴展集的自訂映像](./ansible-vmss-update-image.md)
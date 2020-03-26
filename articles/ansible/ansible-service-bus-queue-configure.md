---
title: 教學課程 - 使用 Ansible 在 Azure 服務匯流排中設定佇列
description: 了解如何使用 Ansible 建立 Azure 服務匯流排佇列
keywords: ansible, azure, devops, bash, 劇本, 服務匯流排, 佇列
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 8ba4c2296d903c4f35aa36eb92dfbc3b56ec4b18
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "76713229"
---
# <a name="tutorial-configure-queues-in-azure-service-bus-using-ansible"></a>教學課程：使用 Ansible 在 Azure 服務匯流排中設定佇列

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * 建立佇列
> * 建立 SAS 原則
> * 擷取命名空間資訊
> * 擷取佇列資訊
> * 撤銷佇列 SAS 原則

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-queue"></a>建立服務匯流排佇列

範例劇本程式碼會建立下列資源︰
- Azure 資源群組
- 資源群組內的服務匯流排命名空間
- 服務匯流排佇列與命名空間

請下列腳本儲存為 `servicebus_queue.yml`：

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      location: eastus
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Ensure resource group exist
      azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          location: "{{ location }}"
    - name: Create a namespace
      azure_rm_servicebus:
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
    - name: Create a queue
      azure_rm_servicebusqueue:
          name: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: queue
    - debug:
          var: queue
```

使用 `ansible-playbook` 命令執行劇本：

```bash
ansible-playbook servicebus_queue.yml
```

## <a name="create-the-sas-policy"></a>建立 SAS 原則

[共用存取簽章 (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) 是使用權杖的宣告式授權機制。 

範例劇本程式碼會使用不同的權限為服務匯流排佇列建立兩個 SAS 原則。

請下列腳本儲存為 `servicebus_queue_policy.yml`：

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Create a policy with send and listen privilege
      azure_rm_servicebussaspolicy:
          name: "{{ queue }}-policy"
          queue: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          rights: listen_send
      register: policy
    - debug:
          var: policy
```

在執行劇本之前，請參閱下列注意事項：
- `rights` 值表示使用者對佇列擁有的權限。 指定下列其中一個值：`manage`、`listen`、`send` 或 `listen_send`。

使用 `ansible-playbook` 命令執行劇本：

```bash
ansible-playbook servicebus_queue_policy.yml
```

## <a name="retrieve-namespace-information"></a>擷取命名空間資訊

範例劇本程式碼會查詢命名空間資訊。

請下列腳本儲存為 `servicebus_namespace_info.yml`：

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
  tasks:
    - name: Get a namespace's information
      azure_rm_servicebus_facts:
          type: namespace
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          show_sas_policies: yes
      register: ns
    - debug:
          var: ns
```

在執行劇本之前，請參閱下列注意事項：
- `show_sas_policies` 值會指出是否在指定的命名空間之下顯示 SAS 原則。 根據預設，此值是 `False` 以避免額外的網路負荷。

使用 `ansible-playbook` 命令執行劇本：

```bash
ansible-playbook servicebus_namespace_info.yml
```

## <a name="retrieve-queue-information"></a>擷取佇列資訊

範例劇本程式碼會查詢佇列資訊。 

請下列腳本儲存為 `servicebus_queue_info.yml`：

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Get a queue's information
      azure_rm_servicebus_facts:
          type: queue
          name: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          show_sas_policies: yes
      register: queue
    - debug:
          var: queue
```

在執行劇本之前，請參閱下列注意事項：
- `show_sas_policies` 值會指出是否在指定的佇列之下顯示 SAS 原則。 根據預設，此值會設為 `False` 以避免額外的網路負荷。

使用 `ansible-playbook` 命令執行劇本：

```bash
ansible-playbook servicebus_queue_info.yml
```

## <a name="revoke-the-queue-sas-policy"></a>撤銷佇列 SAS 原則

範例劇本程式碼會刪除佇列 SAS 原則。

請下列腳本儲存為 `servicebus_queue_policy_delete.yml`：

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Create a policy with send and listen privilege
      azure_rm_servicebussaspolicy:
          name: "{{ queue }}-policy"
          queue: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
```

使用 `ansible-playbook` 命令執行劇本：

```bash
ansible-playbook servicebus_queue_policy_delete.yml
```

## <a name="clean-up-resources"></a>清除資源

如果不再需要本文中建立的資源，請予以刪除。 

將下列程式碼儲存為 `cleanup.yml`：

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Delete queue
      azure_rm_servicebusqueue:
          name: "{{ queue }}"
          resource_group: "{{ resource_group }}"
          namespace: "{{ namespace }}"
          state: absent
    - name: Delete namespace
      azure_rm_servicebus:
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
    - name: Delete resource group
      azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          state: absent
          force_delete_nonempty: yes
```

使用 `ansible-playbook` 命令執行劇本：

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"] 
> [教學課程：使用 Ansible 在 Azure 服務匯流排中設定主題](ansible-service-bus-topic-configure.md)
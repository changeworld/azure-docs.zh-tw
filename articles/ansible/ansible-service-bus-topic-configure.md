---
title: 教學課程 - 使用 Ansible 在 Azure 服務匯流排中設定主題
description: 了解如何使用 Ansible 建立 Azure 服務匯流排主題
keywords: ansible, azure, devops, bash, 劇本, 服務匯流排, 主題, 訂用帳戶
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: d07c7622043353a79d5a82994c2fab4f0835b453
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "74155806"
---
# <a name="tutorial-configure-topics-in-azure-service-bus-using-ansible"></a>教學課程：使用 Ansible 在 Azure 服務匯流排中設定主題

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * 建立主題
> * 建立訂用帳戶
> * 建立 SAS 原則
> * 擷取命名空間資訊
> * 擷取主題和訂用帳戶資訊
> * 撤銷 SAS 原則

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-topic"></a>建立服務匯流排主題

範例劇本程式碼會建立下列資源︰
- Azure 資源群組
- 資源群組內的服務匯流排命名空間
- 服務匯流排主題與命名空間

請下列腳本儲存為 `servicebus_topic.yml`：

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      location: eastus
      namespace: servicebustestns
      topic: servicebustesttopic
  tasks:
    - name: Ensure resource group exist
      azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          location: "{{ location }}"
    - name: Create a namespace
      azure_rm_servicebus:
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
    - name: Create a topic
      azure_rm_servicebustopic:
          name: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: topic
    - debug:
          var: topic
```

使用 `ansible-playbook` 命令執行劇本：

```bash
ansible-playbook servicebus_topic.yml
```

## <a name="create-the-subscription"></a>建立訂用帳戶

範例劇本程式碼會在服務匯流排主題下建立訂用帳戶。 Azure 服務匯流排可以有多個訂用帳戶。 主題的訂閱者會收到傳送至該主題的每個訊息的複本。 訂用帳戶是具名實體，會以永久存在的方式建立，但您可以選擇讓其過期。

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      location: eastus
      namespace: servicebustestns
      topic: servicebustesttopic
      subscription: servicebustestsubs
  tasks:
    - name: Create a subscription
      azure_rm_servicebustopicsubscription:
          name: "{{ subscription }}"
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: subs
    - debug:
          var: subs
```

請下列腳本儲存為 `servicebus_subscription.yml`：

使用 `ansible-playbook` 命令執行劇本：

```bash
ansible-playbook servicebus_subscription.yml
```

## <a name="create-the-sas-policy"></a>建立 SAS 原則

[共用存取簽章 (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) 是使用權杖的宣告式授權機制。 

範例劇本程式碼會使用不同的權限為服務匯流排佇列建立兩個 SAS 原則。

請下列腳本儲存為 `servicebus_topic_policy.yml`：

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      topic: servicebustesttopic
  tasks:
    - name: Create a policy with send and listen privilege
      azure_rm_servicebussaspolicy:
          name: "{{ topic }}-{{ item }}"
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          rights: "{{ item }}"
      with_items:
        - send
        - listen
      register: policy
    - debug:
          var: policy
```

使用 `ansible-playbook` 命令執行劇本：

```bash
ansible-playbook servicebus_topic_policy.yml
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

## <a name="retrieve-topic-and-subscription-information"></a>擷取主題和訂用帳戶資訊

範例劇本程式碼會查詢下列資訊：
- 服務匯流排主題資訊
- 主題的訂用帳戶詳細資料清單
 
請下列腳本儲存為 `servicebus_list.yml`：

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      topic: servicebustesttopic
  tasks:
    - name: Get a topic's information
      azure_rm_servicebus_facts:
          type: topic
          name: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          show_sas_policies: yes
      register: topic_fact
    - name: "List subscriptions under topic {{ topic }}"
      azure_rm_servicebus_facts:
          type: subscription
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: subs_fact
    - debug:
          var: "{{ item }}"
      with_items:
        - topic_fact.servicebuses[0]
        - subs_fact.servicebuses
```

在執行劇本之前，請參閱下列注意事項：
- `show_sas_policies` 值會指出是否在指定的佇列之下顯示 SAS 原則。 根據預設，此值會設為 `False` 以避免額外的網路負荷。

使用 `ansible-playbook` 命令執行劇本：

```bash
ansible-playbook servicebus_list.yml
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
      topic: servicebustesttopic
  tasks:
    - name: Delete a policy
      azure_rm_servicebussaspolicy:
          name: "{{ topic }}-policy"
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
```

使用 `ansible-playbook` 命令執行劇本：

```bash
ansible-playbook servicebus_topic_policy_delete.yml
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
      topic: servicebustesttopic
      subscription: servicebustestsubs
  tasks:
    - name: Delete subscription
      azure_rm_servicebustopicsubscription:
          name: "{{ subscription }}"
          topic: "{{ topic }}"
          resource_group: "{{ resource_group }}"
          namespace: "{{ namespace }}"
          state: absent
    - name: Delete topic
      azure_rm_servicebustopic:
          name: "{{ topic }}"
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
> [Ansible on Azure](/azure/ansible/)
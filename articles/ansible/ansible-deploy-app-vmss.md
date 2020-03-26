---
title: 教學課程 - 使用 Ansible 將應用程式部署至 Azure 中的虛擬機器擴展集
description: 了解如何使用 Ansible 設定 Azure 虛擬機器擴展集，並在擴展集上部署應用程式
keywords: ansible, azure, devops, bash, 腳本, 虛擬機器, 虛擬機器擴展集, vmss
ms.topic: tutorial
ms.date: 01/13/2020
ms.openlocfilehash: d638ae3f0c33734b42ef5456772fcd2bc62e35a4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "75940850"
---
# <a name="tutorial-deploy-apps-to-virtual-machine-scale-sets-in-azure-using-ansible"></a>教學課程：使用 Ansible 將應用程式部署至 Azure 中的虛擬機器擴展集

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * 擷取 Azure VM 群組的主機資訊
> * 複製並建置應用程式範例
> * 在擴展集上安裝 JRE (Java Runtime Environment)
> * 將 Java 應用程式部署至擴展集

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 
[!INCLUDE [ansible-prereqs-vm-scale-set.md](../../includes/ansible-prereqs-vm-scale-set.md)]
- **git** - [git](https://git-scm.com) 可用來下載本教學課程中使用的 Java 範例。
- **Java SE 開發套件 (JDK)** - [JDK](https://aka.ms/azure-jdks) 可用來建置範例 Java 專案。
- **Apache Maven** - [Apache Maven](https://maven.apache.org/download.cgi) 可用來建置範例 Java 專案。

## <a name="get-host-information"></a>取得主機資訊

本節中的劇本程式碼會擷取虛擬機器群組的主機資訊。 下列程式碼會取得資源群組內指定的公用 IP 位址和負載平衡器，並在清查中建立名為 `scalesethosts` 的主機群組。

請下列範例腳本儲存為 `get-hosts-tasks.yml`：

  ```yml
  - name: Get facts for all Public IPs within a resource groups
    azure_rm_publicipaddress_facts:
      resource_group: "{{ resource_group }}"
    register: output_ip_address

  - name: Get loadbalancer info
    azure_rm_loadbalancer_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ loadbalancer_name }}"
    register: output

  - name: Add all hosts
    add_host:
      groups: scalesethosts
      hostname: "{{ output_ip_address.publicipaddresses[0].ip_address }}_{{ item.properties.frontendPort }}"
      ansible_host: "{{ output_ip_address.publicipaddresses[0].ip_address }}"
      ansible_port: "{{ item.properties.frontendPort }}"
      ansible_ssh_user: "{{ admin_username }}"
      ansible_ssh_pass: "{{ admin_password }}"
    with_items:
      - "{{ output.ansible_info.azure_loadbalancers[0].properties.inboundNatRules }}"
  ```

## <a name="prepare-an-application-for-deployment"></a>準備應用程式以進行開發

本節中的劇本程式碼會使用 `git` 從 GitHub 複製 Java 範例專案，然後建置專案。 

請下列腳本儲存為 `app.yml`：

  ```yml
  - hosts: localhost
    vars:
      repo_url: https://github.com/spring-guides/gs-spring-boot.git
      workspace: ~/src/helloworld

    tasks:
    - name: Git Clone sample app
      git:
        repo: "{{ repo_url }}"
        dest: "{{ workspace }}"

    - name: Build sample app
      shell: mvn package chdir="{{ workspace }}/complete"
  ```

使用下列命令執行範例 Ansible 腳本：

  ```bash
  ansible-playbook app.yml
  ```

執行劇本後，您會看到類似下列結果的輸出：

  ```Output
  PLAY [localhost] 

  TASK [Gathering Facts] 
  ok: [localhost]

  TASK [Git Clone sample app] 
  changed: [localhost]

  TASK [Build sample app] 
  changed: [localhost]

  PLAY RECAP 
  localhost                  : ok=3    changed=2    unreachable=0    failed=0

  ```

## <a name="deploy-the-application-to-a-scale-set"></a>將應用程式部署至擴展集

本節中的劇本程式碼會用來：

* 在名為 `saclesethosts` 的主機群組上安裝 JRE
* 將 Java 應用程式部署至名為 `saclesethosts` 的主機群組

有兩種方式可取得範例劇本：

* [下載劇本](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-setup-deploy.yml)並將其儲存至 `vmss-setup-deploy.yml`。
* 建立名為 `vmss-setup-deploy.yml` 的新檔案，並在其中複製下列內容：

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    scaleset_name: myScaleSet
    loadbalancer_name: myScaleSetLb
    admin_username: azureuser
    admin_password: "{{ admin_password }}"
  tasks:
  - include: get-hosts-tasks.yml

- name: Install JRE on a scale set
  hosts: scalesethosts
  become: yes
  vars:
    workspace: ~/src/helloworld
    admin_username: azureuser

  tasks:
  - name: Install JRE
    apt:
      name: default-jre
      update_cache: yes

  - name: Copy app to Azure VM
    copy:
      src: "{{ workspace }}/complete/target/gs-spring-boot-0.1.0.jar"
      dest: "/home/{{ admin_username }}/helloworld.jar"
      force: yes
      mode: 0755

  - name: Start the application
    shell: java -jar "/home/{{ admin_username }}/helloworld.jar" >/dev/null 2>&1 &
    async: 5000
    poll: 0
```

在執行劇本之前，請參閱下列注意事項：

* 在 `vars` 區段中，將 `{{ admin_password }}` 預留位置取代為您自己的密碼。
* 若要使用需要密碼的 SSH 連線類型，請安裝 sshpass 程式：

    Ubuntu：

    ```bash
    apt-get install sshpass
    ```

    CentOS：

    ```bash
    yum install sshpass
    ```

* 在某些環境中，您可能會看到關於使用了 SSH 密碼而非金鑰的錯誤。 如果出現此錯誤，您可以在 `/etc/ansible/ansible.cfg` 或 `~/.ansible.cfg` 中新增以下這一行，以停用主機金鑰檢查：

    ```bash
    [defaults]
    host_key_checking = False
    ```

使用下列命令執行腳本：

  ```bash
  ansible-playbook vmss-setup-deploy.yml
  ```

執行 ansible-playbook 命令的輸出，會指出範例 Java 應用程式已安裝至擴展集的主機群組：

  ```Output
  PLAY [localhost]

  TASK [Gathering Facts]
  ok: [localhost]

  TASK [Get facts for all Public IPs within a resource groups]
  ok: [localhost]

  TASK [Get loadbalancer info]
  ok: [localhost]

  TASK [Add all hosts]
  changed: [localhost] ...

  PLAY [Install JRE on scale set]

  TASK [Gathering Facts]
  ok: [40.114.30.145_50000]
  ok: [40.114.30.145_50003]

  TASK [Copy app to Azure VM]
  changed: [40.114.30.145_50003]
  changed: [40.114.30.145_50000]

  TASK [Start the application]
  changed: [40.114.30.145_50000]
  changed: [40.114.30.145_50003]

  PLAY RECAP
  40.114.30.145_50000        : ok=4    changed=3    unreachable=0    failed=0
  40.114.30.145_50003        : ok=4    changed=3    unreachable=0    failed=0
  localhost                  : ok=4    changed=1    unreachable=0    failed=0
  ```

## <a name="verify-the-results"></a>驗證結果

瀏覽至您擴展集的負載平衡器 URL，以確認您的工作結果：

![在 Azure 中的擴展集上執行的 Java 應用程式。](media/ansible-vmss-deploy/ansible-deploy-app-vmss.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教學課程：使用 Ansible 自動調整 Azure 中的虛擬機器擴展集](./ansible-auto-scale-vmss.md)
---
title: 教學課程 - 使用 Ansible 在 Azure Kubernetes Service (AKS) 中設定角色型存取控制 (RBAC) 角色
description: 了解如何使用 Ansible 在 Azure Kubernetes Service(AKS) 叢集中設定 RBAC
keywords: ansible, azure, devops, bash, cloudshell, 劇本, aks, 容器, aks, kubernetes, azure active directory, rbac
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 5fac42383ee56318cc4b8f39323c02d05853dbb6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "76836961"
---
# <a name="tutorial-configure-role-based-access-control-rbac-roles-in-azure-kubernetes-service-aks-using-ansible"></a>教學課程：使用 Ansible 在 Azure Kubernetes Service (AKS) 中設定角色型存取控制 (RBAC) 角色

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

AKS 可設定為使用 [Azure Active Directory (AD)](/azure/active-directory/) 進行使用者驗證。 設定之後，您必須使用 Azure AD 驗證權杖來登入 AKS 叢集。 RBAC 可用使用者的身分識別或目錄群組成員資格作為基礎。

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * 建立已啟用 Azure AD 的 AKS 叢集
> * 在叢集中設定 RBAC 角色

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- **安裝 RedHat OpenShift 程式庫** - `pip install openshift`

## <a name="configure-azure-ad-for-aks-authentication"></a>設定 Azure AD 以進行 AKS 驗證

設定 Azure AD 以進行 AKS 驗證時，會設定兩個 Azure AD 應用程式。 這項作業必須由 Azure 租用戶系統管理員完成。 如需詳細資訊，請參閱[整合 Azure Active Directory 與 AKS](/azure/aks/aad-integration#create-the-server-application)。 

向 Azure 租用戶系統管理員索取下列值：

- 伺服器應用程式祕密
- 伺服器應用程式識別碼
- 用戶端應用程式識別碼 
- 租用戶識別碼

執行範例劇本時需要使用這些值。  

## <a name="create-an-aks-cluster"></a>建立 AKS 叢集

在本節中，您會使用 [Azure AD 應用程式](#configure-azure-ad-for-aks-authentication)建立 AKS。

以下是使用範例劇本時所應考量的幾項要點：

- 劇本會從 `~/.ssh/id_rsa.pub` 載入 `ssh_key`。 如果您加以修改，請使用單行格式，且開頭應為 "ssh-rsa" (不含引號)。
- `client_id` 和 `client_secret` 值會從 `~/.azure/credentials` 載入，這是預設的認證檔案。 您可以將這些值設為服務主體，或從環境變數中載入這些值：

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

請下列腳本儲存為 `aks-create.yml`：

```yml
- name: Create resource group
  azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"

- name: List supported kubernetes version from Azure
  azure_rm_aksversion_facts:
      location: "{{ location }}"
  register: versions

- name: Create AKS cluster with RBAC enabled
  azure_rm_aks:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      dns_prefix: "{{ name }}"
      enable_rbac: yes
      kubernetes_version: "{{ versions.azure_aks_versions[-1] }}"
      agent_pool_profiles:
        - count: 3
          name: nodepool1
          vm_size: Standard_D2_v2
      linux_profile:
          admin_username: azureuser
          ssh_key: "{{ lookup('file', '~/.ssh/id_rsa.pub') }}"
      service_principal:
          client_id: "{{ lookup('ini', 'client_id section=default file=~/.azure/credentials') }}"
          client_secret: "{{ lookup('ini', 'secret section=default file=~/.azure/credentials') }}"
      aad_profile:
          client_app_id: "{{ client_app_id }}"
          server_app_id: "{{ server_app_id }}"
          server_app_secret: "{{ server_app_secret }}"
          tenant_id: "{{ app_tenant_id }}"
  register: aks

- name: Save cluster user config
  copy:
      content: "{{ aks.kube_config }}"
      dest: "aks-{{ name }}-kubeconfig-user"

- name: Get admin config of AKS
  azure_rm_aks_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      show_kubeconfig: admin
  register: aks

- name: Save the kubeconfig
  copy:
      content: "{{ aks.aks[0].kube_config }}"
      dest: "aks-{{ name }}-kubeconfig"
```

## <a name="get-the-azure-ad-object-id"></a>取得 Azure AD 物件識別碼

若要建立 RBAC 繫結，首先必須取得 Azure AD 物件識別碼。 

1. 登入 [Azure 入口網站](https://go.microsoft.com/fwlink/p/?LinkID=525040)。

1. 在頁面頂端的 [搜尋] 欄位中，輸入 `Azure Active Directory`。 

1. 按一下 [ `Enter`]。

1. 在 [管理]  功能表中，選取 [使用者]  。

1. 在 [名稱] 欄位中搜尋您的帳戶。

1. 在 [名稱]  資料行中，選取您帳戶的連結。

1. 在 [身分識別]  區段中，複製 [物件識別碼]  。

    ![複製 Azure AD 物件識別碼。](./media/ansible-aks-configure-rbac/ansible-aad-object-id.png)

## <a name="create-rbac-binding"></a>建立 RBAC 繫結

在本節中，您會在 AKS 中建立角色繫結或叢集角色繫結。 

請下列腳本儲存為 `kube-role.yml`：

```yml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: <your-aad-account>
```

請將 `<your-aad-account>` 預留位置取代為您的 Azure AD 租用戶[物件識別碼](#get-the-azure-ad-object-id)。

將下列劇本 (此劇本將您的新角色部署至 AKS) 儲存為 `aks-kube-deploy.yml`：

```yml
- name: Apply role to AKS
  k8s:
      src: kube-role.yml
      kubeconfig: "aks-{{ name }}-kubeconfig"
```

## <a name="run-the-sample-playbook"></a>執行範例劇本

本節列出會對本文中建立的工作進行呼叫的完整範例劇本。 

請下列腳本儲存為 `aks-rbac.yml`：

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
      name: aksansibletest
      location: eastus
  tasks:
     - name: Ensure resource group exist
       azure_rm_resourcegroup:
           name: "{{ resource_group }}"
           location: "{{ location }}"

     - name: Create AKS
       vars:
           client_app_id: <client id>
           server_app_id: <server id>
           server_app_secret: <server secret>
           app_tenant_id: <tenant id>
       include_tasks: aks-create.yml

     - name: Enable RBAC
       include_tasks: aks-kube-deploy.yml
```

在 `vars` 區段中，請將下列預留位置取代為您的 Azure AD 資訊：

- `<client id>`
- `<server id>`
- `<server secret>`
- `<tenant id>`

使用 `ansible-playbook` 命令執行完整劇本：

```bash
ansible-playbook aks-rbac.yml
```

## <a name="verify-the-results"></a>驗證結果

在本節中，您會使用 kubectl 列出在本文中建立的節點。

在終端機提示字元中輸入下列命令：

```bash
kubectl --kubeconfig aks-aksansibletest-kubeconfig-user get nodes
```

此命令會將您導向至驗證頁面。 使用您的 Azure 帳戶進行登入。

驗證之後，kubectl 會列出類似於下列結果的節點：

```txt
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XXXXXXXX to authenticate.
NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-33413200-0   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-1   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-2   Ready    agent   49m   v1.12.6
```

## <a name="clean-up-resources"></a>清除資源

如果不再需要本文中建立的資源，請予以刪除。 

將下列程式碼儲存為 `cleanup.yml`：

```yml
---
- hosts: localhost
  vars:
      name: aksansibletest
      resource_group: aksansibletest
  tasks:
      - name: Clean up resource group
        azure_rm_resourcegroup:
            name: "{{ resource_group }}"
            state: absent
            force: yes
      - name: Remove kubeconfig
        file:
            state: absent
            path: "aks-{{ name }}-kubeconfig"
```

使用 `ansible-playbook` 命令執行劇本：

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Ansible on Azure](/azure/ansible/)

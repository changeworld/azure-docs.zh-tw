---
title: 教學課程 - 使用 Ansible 在 Azure App Service 中設定應用程式
description: 了解如何在 Azure App Service 中建立具有 Java 8 和 Tomcat 容器執行階段的應用程式
keywords: ansible、azure、devops、bash、劇本、Azure App Service、Web App、Java
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 2891ff47b17900c4c1c8e1c21f22495b65108fd5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "74156564"
---
# <a name="tutorial-configure-apps-in-azure-app-service-using-ansible"></a>教學課程：使用 Ansible 在 Azure App Service 中設定應用程式

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-app-service.md](../../includes/open-source-devops-intro-app-service.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * 在 Azure App Service 中建立具有 Java 8 和 Tomcat 容器執行階段的應用程式
> * 建立 Azure 流量管理員設定檔
> * 使用已建立的應用程式定義流量管理員端點

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-basic-app-service"></a>建立基本應用程式服務

本節中的劇本程式碼會定義下列資源︰

* 在其中部署 App Service 方案和應用程式的 Azure 資源群組
* Linux 上具有 Java 8 和 Tomcat 容器執行階段的應用程式服務

請下列腳本儲存為 `firstwebapp.yml`：

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    webapp_name: myfirstWebApp
    plan_name: myAppServicePlan
    location: eastus
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

    - name: Create App Service on Linux with Java Runtime
      azure_rm_webapp:
        resource_group: "{{ resource_group }}"
        name: "{{ webapp_name }}"
        plan:
          resource_group: "{{ resource_group }}"
          name: "{{ plan_name }}"
          is_linux: true
          sku: S1
          number_of_workers: 1
        frameworks:
          - name: "java"
            version: "8"
            settings:
              java_container: tomcat
              java_container_version: 8.5
```

使用 `ansible-playbook` 命令執行劇本：

```bash
ansible-playbook firstwebapp.yml
```

執行劇本後，您會看到類似下列結果的輸出：

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create a resource group] 
changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] 
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

PLAY RECAP 
localhost                  : ok=3    changed=2    unreachable=0    failed=0
```

## <a name="create-an-app-and-use-azure-traffic-manager"></a>建立應用程式並使用 Azure 流量管理員

[Azure 流量管理員](/azure/app-service/web-sites-traffic-manager)可讓您控制如何將來自 Web 用戶端的要求分散至 Azure App Service 中的應用程式。 App Service 端點新增至 Azure 流量管理員設定檔後，流量管理員會追蹤您 App Service 應用程式的狀態。 這些狀態包括執行中、已停止和已刪除。 流量管理員可用來決定哪些端點應接收流量。

在 App Service 中，應用程式會在 [App Service 方案](/azure/app-service/overview-hosting-plans)中執行。 App Service 方案會針對要執行的應用程式定義一組計算資源。 您可以在不同的群組中管理 App Service 方案和 Web 應用程式。

本節中的劇本程式碼會定義下列資源︰

* 在其中部署 App Service 方案的 Azure 資源群組
* App Service 方案
* 在其中部署應用程式的 Azure 資源群組
* Linux 上具有 Java 8 和 Tomcat 容器執行階段的應用程式服務
* 流量管理員設定檔
* 使用已建立的應用程式的流量管理員端點

請下列腳本儲存為 `webapp.yml`：

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group_webapp: myResourceGroupWebapp
    resource_group: myResourceGroup
    webapp_name: myLinuxWebApp
    plan_name: myAppServicePlan
    location: eastus
    traffic_manager_profile_name: myTrafficManagerProfile
    traffic_manager_endpoint_name: myTrafficManagerEndpoint

  tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
        name: "{{ resource_group_webapp }}"
        location: "{{ location }}"

  - name: Create secondary resource group
    azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

  - name: Create App Service Plan
    azure_rm_appserviceplan:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
      location: "{{ location }}"
      is_linux: true
      sku: S1
      number_of_workers: 1

  - name: Create App Service on Linux with Java Runtime
    azure_rm_webapp:
        resource_group: "{{ resource_group_webapp }}"
        name: "{{ webapp_name }}"
        plan:
          resource_group: "{{ resource_group }}"
          name: "{{ plan_name }}"
          is_linux: true
          sku: S1
          number_of_workers: 1
        app_settings:
          testkey: "testvalue"
        frameworks:
          - name: java
            version: 8
            settings:
              java_container: "Tomcat"
              java_container_version: "8.5"

  - name: Get web app facts
    azure_rm_webapp_facts:
      resource_group: "{{ resource_group_webapp }}"
      name: "{{ webapp_name }}"
    register: webapp
    
  - name: Create Traffic Manager Profile
    azure_rm_trafficmanagerprofile:
      resource_group: "{{ resource_group_webapp }}"
      name: "{{ traffic_manager_profile_name }}"
      location: global
      routing_method: performance
      dns_config:
        relative_name: "{{ traffic_manager_profile_name }}"
        ttl:  60
      monitor_config:
        protocol: HTTPS
        port: 80
        path: '/'

  - name: Add endpoint to traffic manager profile, using created web site
    azure_rm_trafficmanagerendpoint:
      resource_group: "{{ resource_group_webapp }}"
      profile_name: "{{ traffic_manager_profile_name }}"
      name: "{{ traffic_manager_endpoint_name }}"
      type: azure_endpoints
      location: "{{ location }}"
      target_resource_id: "{{ webapp.webapps[0].id }}"
```

使用 `ansible-playbook` 命令執行劇本：

```bash
ansible-playbook webapp.yml
```

執行劇本後，您會看到類似下列結果的輸出：

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create resource group] 
changed: [localhost]

TASK [Create resource group for app service plan] 
changed: [localhost]

TASK [Create App Service Plan] 
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] 
changed: [localhost]

TASK [Get web app facts] 
ok: [localhost]

TASK [Create Traffic Manager Profile] 
 [WARNING]: Azure API profile latest does not define an entry for TrafficManagerManagementClient

changed: [localhost]

TASK [Add endpoint to traffic manager profile, using the web site created above] 
changed: [localhost]

TASK [Get Traffic Manager Profile facts] 
ok: [localhost]

PLAY RECAP 
localhost                  : ok=9    changed=6    unreachable=0    failed=0
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"] 
> [教學課程：使用 Ansible 調整 Azure App Service 中的應用程式](/azure/ansible/ansible-scale-azure-web-apps)
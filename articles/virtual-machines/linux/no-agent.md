---
title: 建立沒有布建代理程式的 Linux 映射
description: 建立一般化 Linux 映射，而不在 Azure 中提供布建代理程式。
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: d177e7fd7d18b24f9d8fd7f3e6662abe16bba317
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045326"
---
# <a name="creating-generalized-images-without-a-provisioning-agent"></a>建立沒有布建代理程式的一般化映射

Microsoft Azure 以[walinuxagent](https://github.com/Azure/WALinuxAgent)或[雲端 init](https://github.com/canonical/cloud-init)的形式提供 Linux vm 的布建代理程式（建議）。 但是，如果您不想要在布建代理程式中使用其中一個應用程式，就可能會發生這種情況，例如：

- 您的 Linux 散發版本/版本不支援雲端初始化/Linux 代理程式。
- 您需要設定特定的 VM 屬性，例如主機名稱。

> [!NOTE] 
>
> 如果您不需要設定任何屬性，或要進行任何形式的布建，您應該考慮建立特製化映射。

本文說明如何設定您的 VM 映射以滿足 Azure 平臺需求並設定主機名稱，而不需要安裝布建代理程式。

## <a name="networking-and-reporting-ready"></a>網路功能和報告就緒

若要讓 Linux VM 與 Azure 元件進行通訊，您需要 DHCP 用戶端從虛擬網路取得主機 IP，以及 DNS 解析和路由管理。 大部分的散發版本都隨附這些公用程式。 Linux 散發版本廠商已在 Azure 上測試過的工具組括 `dhclient` 、 `network-manager` `systemd-networkd` 和其他。

> [!NOTE]
>
> 目前在沒有布建代理程式的情況下建立一般化映射僅支援已啟用 DHCP 的 Vm。

設定並設定網路功能之後，您必須「準備好」。 這會告訴 Azure VM 已成功布建。

> [!IMPORTANT]
>
> 無法向 Azure 回報，將會導致您的 VM 重新開機！

## <a name="demosample"></a>示範/範例

此示範會示範如何使用現有的 Marketplace 映射（在此案例中為 Debian Buster VM）並移除 Linux 代理程式（walinuxagent），同時也會建立最基本的程式，向 Azure 回報 VM 已「就緒」。

### <a name="create-the-resource-group-and-base-vm"></a>建立資源群組和基礎 VM：

```bash
$ az group create --location eastus --name demo1
```

建立基底 VM：

```bash
$ az vm create \
    --resource-group demo1 \
    --name demo1 \
    --location eastus \
    --ssh-key-value <ssh_pub_key_path> \
    --public-ip-address-dns-name demo1 \
    --image "debian:debian-10:10:latest"
```

### <a name="remove-the-image-provisioning-agent"></a>移除映射布建代理程式

布建 VM 之後，您可以透過 SSH 連線並移除 Linux 代理程式：

```bash
$ sudo apt purge -y waagent
$ sudo rm -rf /var/lib/waagent /etc/waagent.conf /var/log/waagent.log
```

### <a name="add-required-code-to-the-vm"></a>將必要的程式碼新增至 VM

此外，在 VM 內，因為我們已移除 Azure Linux 代理程式，所以我們必須提供一個機制來回報準備就緒。 

#### <a name="python-script"></a>Python 指令碼

```python
import http.client
import sys
from xml.etree import ElementTree

wireserver_ip = '168.63.129.16'
wireserver_conn = http.client.HTTPConnection(wireserver_ip)

print('Retrieving goal state from the Wireserver')
wireserver_conn.request(
    'GET',
    '/machine?comp=goalstate',
    headers={'x-ms-version': '2012-11-30'}
)

resp = wireserver_conn.getresponse()

if resp.status != 200:
    print('Unable to connect with wireserver')
    sys.exit(1)

wireserver_goalstate = resp.read().decode('utf-8')

xml_el = ElementTree.fromstring(wireserver_goalstate)

container_id = xml_el.findtext('Container/ContainerId')
instance_id = xml_el.findtext('Container/RoleInstanceList/RoleInstance/InstanceId')
print(f'ContainerId: {container_id}')
print(f'InstanceId: {instance_id}')

# Construct the XML response we need to send to Wireserver to report ready.
health = ElementTree.Element('Health')
goalstate_incarnation = ElementTree.SubElement(health, 'GoalStateIncarnation')
goalstate_incarnation.text = '1'
container = ElementTree.SubElement(health, 'Container')
container_id_el = ElementTree.SubElement(container, 'ContainerId')
container_id_el.text = container_id
role_instance_list = ElementTree.SubElement(container, 'RoleInstanceList')
role = ElementTree.SubElement(role_instance_list, 'Role')
instance_id_el = ElementTree.SubElement(role, 'InstanceId')
instance_id_el.text = instance_id
health_second = ElementTree.SubElement(role, 'Health')
state = ElementTree.SubElement(health_second, 'State')
state.text = 'Ready'

out_xml = ElementTree.tostring(
    health,
    encoding='unicode',
    method='xml'
)
print('Sending the following data to Wireserver:')
print(out_xml)

wireserver_conn.request(
    'POST',
    '/machine?comp=health',
    headers={
        'x-ms-version': '2012-11-30',
        'Content-Type': 'text/xml;charset=utf-8',
        'x-ms-agent-name': 'custom-provisioning'
    },
    body=out_xml
)

resp = wireserver_conn.getresponse()
print(f'Response: {resp.status} {resp.reason}')

wireserver_conn.close()
```

#### <a name="generic-steps-without-using-python"></a>一般步驟（不使用 Python）

如果您的 VM 未安裝或無法使用 Python，您可以使用下列步驟，以程式設計方式重現上述腳本邏輯：

1. 藉 `ContainerId` `InstanceId` 由剖析 WireServer 的回應，來取出和： `curl -X GET -H 'x-ms-version: 2012-11-30' http://$168.63.129.16/machine?comp=goalstate`

2. 建立下列 XML 資料， `ContainerId` 並從上述步驟插入剖析的和 `InstanceId` ：
   ```xml
   <Health>
     <GoalStateIncarnation>1</GoalStateIncarnation>
     <Container>
       <ContainerId>CONTAINER_ID</ContainerId>
       <RoleInstanceList>
         <Role>
           <InstanceId>INSTANCE_ID</InstanceId>
           <Health>
             <State>Ready</State>
           </Health>
         </Role>
       </RoleInstanceList>
     </Container>
   </Health>
   ```

3. 將此資料張貼至 WireServer：`curl -X POST -H 'x-ms-version: 2012-11-30' -H "x-ms-agent-name: WALinuxAgent" -H "Content-Type: text/xml;charset=utf-8" -d "$REPORT_READY_XML" http://168.63.129.16/machine?comp=health`

### <a name="automating-running-the-code-at-first-boot"></a>在第一次開機時自動化執行程式碼

此示範使用 systemd，這是現代化 Linux 散發版本中最常見的 init 系統。 因此，最簡單且最完整的原生方式可確保此「報表就緒」機制在適當的時間執行，以建立 systemd 服務單位。 您可以將下列單元檔案新增至 `/etc/systemd/system` （此範例會命名單位檔案 `azure-provisioning.service` ）：

```
[Unit]
Description=Azure Provisioning

[Service]
Type=oneshot
ExecStart=/usr/bin/python3 /usr/local/azure-provisioning.py
ExecStart=/bin/bash -c "hostnamectl set-hostname $(curl \
    -H 'metadata: true' \
    'http://169.254.169.254/metadata/instance/compute/name?api-version=2019-06-01&format=text')"
ExecStart=/usr/bin/systemctl disable azure-provisioning.service

[Install]
WantedBy=multi-user.target
```

此 systemd 服務會執行三項基本布建：

1. 已準備好 Azure 的報告（表示其已成功啟動）。
1. 從 IMDS 提取此資料，以根據使用者提供的 VM 名稱來重新命名 VM。
1. 停用本身，使其只在初次開機時執行，而不是在後續的重新開機時執行。

使用檔案系統上的單位，執行下列動作以啟用它：

```
$ sudo systemctl enable azure-provisioning.service
```

現在，VM 已準備好進行一般化，並從中建立映射。 

#### <a name="completing-the-preparation-of-the-image"></a>正在完成映射的準備工作

回到您的開發電腦，執行下列步驟，以準備從基底 VM 建立映射：

```
$ az vm deallocate --resource-group demo1 --name demo1
$ az vm generalize --resource-group demo1 --name demo1
```

並從這個 VM 建立映射：

```
$ az image create \
    --resource-group demo1 \
    --source demo1 \
    --location eastus \
    --name demo1img
```

現在，我們已準備好從映射建立新的 VM （或多個 Vm）：

```
$ IMAGE_ID=$(az image show -g demo1 -n demo1img --query id -o tsv)
$ az vm create \
    --resource-group demo12 \
    --name demo12 \
    --location eastus \
    --ssh-key-value <ssh_pub_key_path> \
    --public-ip-address-dns-name demo12 \
    --image "$IMAGE_ID" 
    --enable-agent false
```

> [!NOTE]
>
> 請務必將設定 `--enable-agent` 為， `false` 因為 walinuxagent 不存在於要從映射建立的 VM 上。

此 VM 應已成功布建。 登入新布建的 VM 時，您應該可以看到 [已準備好報告] systemd 服務的輸出：

```
$ sudo journalctl -u azure-provisioning.service
-- Logs begin at Thu 2020-06-11 20:28:45 UTC, end at Thu 2020-06-11 20:31:24 UTC. --
Jun 11 20:28:49 thstringnopa systemd[1]: Starting Azure Provisioning...
Jun 11 20:28:54 thstringnopa python3[320]: Retrieving goal state from the Wireserver
Jun 11 20:28:54 thstringnopa python3[320]: ContainerId: 7b324f53-983a-43bc-b919-1775d6077608
Jun 11 20:28:54 thstringnopa python3[320]: InstanceId: fbb84507-46cd-4f4e-bd78-a2edaa9d059b._thstringnopa2
Jun 11 20:28:54 thstringnopa python3[320]: Sending the following data to Wireserver:
Jun 11 20:28:54 thstringnopa python3[320]: <Health><GoalStateIncarnation>1</GoalStateIncarnation><Container><ContainerId>7b324f53-983a-43bc-b919-1775d6077608</ContainerId><RoleInstanceList><Role><InstanceId>fbb84507-46cd-4f4e-bd78-a2edaa9d059b._thstringnopa2</InstanceId><Health><State>Ready</State></Health></Role></RoleInstanceList></Container></Health>
Jun 11 20:28:54 thstringnopa python3[320]: Response: 200 OK
Jun 11 20:28:56 thstringnopa bash[472]:   % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
Jun 11 20:28:56 thstringnopa bash[472]:                                  Dload  Upload   Total   Spent    Left  Speed
Jun 11 20:28:56 thstringnopa bash[472]: [158B blob data]
Jun 11 20:28:56 thstringnopa2 systemctl[475]: Removed /etc/systemd/system/multi-user.target.wants/azure-provisioning.service.
Jun 11 20:28:56 thstringnopa2 systemd[1]: azure-provisioning.service: Succeeded.
Jun 11 20:28:56 thstringnopa2 systemd[1]: Started Azure Provisioning.
```

## <a name="support"></a>支援

如果您執行自己的布建程式碼/代理程式，則您擁有此程式碼的支援，Microsoft 支援服務只會調查與布建介面無法使用相關的問題。 我們會持續在此區域中進行改良和變更，因此您必須監視雲端初始化和 Azure Linux 代理程式中的變更，以提供 API 變更。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱[Linux](provisioning.md)布建。

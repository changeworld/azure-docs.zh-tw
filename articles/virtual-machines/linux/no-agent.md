---
title: 在沒有布建代理程式的情況下建立 Linux 映射
description: 在 Azure 中建立沒有布建代理程式的一般化 Linux 映射。
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 09/01/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 9f0309f4e8273c2ef19ea86636de8e3aa6b6c4bc
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96435095"
---
# <a name="creating-generalized-images-without-a-provisioning-agent"></a>在沒有布建代理程式的情況下建立一般化映射

Microsoft Azure 會以 [walinuxagent](https://github.com/Azure/WALinuxAgent) 或雲端初始化的形式提供 Linux vm 的布 [建代理程式](https://github.com/canonical/cloud-init) (建議的) 。 但是，如果您不想要將其中一個應用程式用於布建代理程式，則可能會發生案例，例如：

- 您的 Linux 發行版本/版本不支援 cloud init/Linux 代理程式。
- 您需要設定特定的 VM 屬性，例如主機名稱。

> [!NOTE] 
>
> 如果您不需要設定任何屬性，或發生任何形式的布建，則應該考慮建立特製化映射。

本文說明如何設定 VM 映射來滿足 Azure 平臺需求並設定主機名稱，而不需要安裝布建代理程式。

## <a name="networking-and-reporting-ready"></a>網路功能和報告就緒

為了讓 Linux VM 與 Azure 元件進行通訊，您需要 DHCP 用戶端從虛擬網路中取出主機 IP，以及 DNS 解析和路由管理。 大部分的散發版本都會隨附現成的公用程式。 Linux 發行版本廠商在 Azure 上測試過的工具組括 `dhclient` 、 `network-manager` `systemd-networkd` 和其他。

> [!NOTE]
>
> 目前，在沒有布建代理程式的情況下建立一般化映射只支援啟用 DHCP 的 Vm。

設定並設定網路功能之後，您必須「回報就緒」。 這會告訴 Azure VM 已成功布建。

> [!IMPORTANT]
>
> 無法向 Azure 回報，會導致您的 VM 重新開機！

## <a name="demosample"></a>示範/範例

此示範將示範如何使用現有的 Marketplace 映射 (在此案例中，Debian Buster VM) 並移除 Linux 代理程式 (walinuxagent) ，但也會建立最基本的程式，向 Azure 回報 VM 為「就緒」。

### <a name="create-the-resource-group-and-base-vm"></a>建立資源群組和基底 VM：

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

此外，在 VM 內，由於我們已移除 Azure Linux 代理程式，因此我們必須提供可供報告的機制。 

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

#### <a name="generic-steps-without-using-python"></a>不使用 Python 的一般步驟 () 

如果您的 VM 未安裝 Python 或無法使用，您可以使用下列步驟，以程式設計方式重現上述腳本邏輯：

1. `ContainerId` `InstanceId` 從 WireServer 剖析回應，以取出和： `curl -X GET -H 'x-ms-version: 2012-11-30' http://$168.63.129.16/machine?comp=goalstate` 。

2. 建立下列 XML 資料， `ContainerId` 並插入上述步驟中已剖析的和 `InstanceId` ：
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

3. 將此資料張貼至 WireServer： `curl -X POST -H 'x-ms-version: 2012-11-30' -H "x-ms-agent-name: WALinuxAgent" -H "Content-Type: text/xml;charset=utf-8" -d "$REPORT_READY_XML" http://168.63.129.16/machine?comp=health`

### <a name="automating-running-the-code-at-first-boot"></a>在首次開機時自動執行程式碼

此示範會使用 systemd，這是新式 Linux 散發版本中最常見的 init 系統。 因此，最簡單且最原生的方式可確保此報表就緒機制在適當的時間執行，以建立 systemd 服務單位。 您可以新增下列單元檔來 `/etc/systemd/system` (此範例將單元檔案命名為 `azure-provisioning.service`) ：

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

這項 systemd 服務會執行基本布建的三項作業：

1. 已準備好 Azure (的報告，指出已成功) 。
1. 從 [Azure Instance Metadata Service (IMDS) ](./instance-metadata-service.md)提取此資料，以根據使用者提供的 vm 名稱來重新命名 VM。 **注意** IMDS 也提供其他的 [實例中繼資料](./instance-metadata-service.md#access-azure-instance-metadata-service)（例如 SSH 公開金鑰），因此您可以設定超過主機名稱。
1. 停用本身，使其只在第一次開機時執行，而不是在後續重新開機時執行。

在檔案系統上，執行下列步驟以啟用該單元：

```
$ sudo systemctl enable azure-provisioning.service
```

現在已準備好將 VM 一般化，並已從該 VM 建立映射。 

#### <a name="completing-the-preparation-of-the-image"></a>完成映射的準備工作

回到您的開發電腦，執行下列程式來準備從基底 VM 建立映射：

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

現在，我們已準備好從映射建立新的 VM (或多個 Vm) ：

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
> 請務必將設定 `--enable-agent` 為， `false` 因為 walinuxagent 不存在於即將從映射建立的 VM 上。

此 VM 應可成功布建。 若要登入新布建的 VM，您應該能夠看到 report ready systemd service 的輸出：

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

如果您執行自己的布建程式碼/代理程式，則您擁有此程式碼的支援，Microsoft 支援服務只會調查與無法使用的布建介面相關的問題。 我們會持續在此區域進行改進和變更，因此您必須監視雲端初始化和 Azure Linux 代理程式中的變更，以布建 API 變更。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱 [Linux](provisioning.md)布建。
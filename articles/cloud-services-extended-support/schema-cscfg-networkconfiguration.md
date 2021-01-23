---
title: Azure 雲端服務 (延伸支援) NetworkConfiguration 架構 |Microsoft Docs
description: '雲端服務的網路設定架構相關資訊 (延伸支援) '
ms.topic: article
ms.service: cloud-services-extended-support
ms.date: 10/14/2020
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 13fe83710c94c1ca37f05d59cb91f31aa8ca1bae
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744388"
---
# <a name="azure-cloud-services-extended-support-config-networkconfiguration-schema"></a>Azure 雲端服務 (延伸支援) config networkConfiguration 架構

服務組態檔的 `NetworkConfiguration` 元素會指定虛擬網路和 DNS 值。 這些設定對於雲端服務而言是選擇性的。

若要深入了解虛擬網路和相關聯的結構描述，您可以使用下列資源：

- [雲端服務 () 設定架構的延伸支援](schema-cscfg-file.md)。
- [雲端服務 (延伸支援) 定義架構](schema-csdef-file.md)。
- [建立虛擬網路](https://docs.microsoft.com/azure/virtual-network/manage-virtual-network)。

## <a name="networkconfiguration-element"></a>NetworkConfiguration 元素
下列範例說明 `NetworkConfiguration` 元素和其子元素。

```xml
<ServiceConfiguration>
  <NetworkConfiguration>
    <AccessControls>
      <AccessControl name="aclName1">
        <Rule order="<rule-order>" action="<rule-action>" remoteSubnet="<subnet-address>" description="rule-description"/>
      </AccessControl>
    </AccessControls>
    <EndpointAcls>
      <EndpointAcl role="<role-name>" endpoint="<endpoint-name>" accessControl="<acl-name>"/>
    </EndpointAcls>
    <Dns>
      <DnsServers>
        <DnsServer name="<server-name>" IPAddress="<server-address>" />
      </DnsServers>
    </Dns>
    <VirtualNetworkSite name="Group <RG-VNet> <VNet-name>"/>
    <AddressAssignments>
      <InstanceAddress roleName="<role-name>">
        <Subnets>
          <Subnet name="<subnet-name>"/>
        </Subnets>
      </InstanceAddress>
      <ReservedIPs>
        <ReservedIP name="<reserved-ip-name>"/>
      </ReservedIPs>
    </AddressAssignments>
  </NetworkConfiguration>
</ServiceConfiguration>
```

下表說明 `NetworkConfiguration` 元素的子項目。

| 元素       | 描述 |
| ------------- | ----------- |
| AccessControl | 選擇性。 指定存取雲端服務中端點的規則。 存取控制名稱是由 `name` 屬性的字串所定義。 `AccessControl` 元素包含一或多個 `Rule` 元素。 可以定義多個 `AccessControl` 元素。|
| 規則 | 選擇性。 指定 IP 位址的指定子網路範圍所應採取的動作。 規則的順序是由 `order` 屬性的字串值所定義。 規則編號愈低，優先順序愈高。 例如，可以 100、200 和 300 的順序編號來指定規則。 順序編號為 100 的規則優先於順序為 200 的規則。<br /><br /> 規則的動作是由 `action` 屬性的字串所定義。 可能的值包括：<br /><br /> -   `permit` – 指定只有指定子網路範圍的封包可與端點通訊。<br />-   `deny` – 指定拒絕存取指定子網路範圍內的端點。<br /><br /> 受規則影響的 IP 位址的子網路範圍是由 `remoteSubnet` 屬性的字串所定義。 規則的說明是由 `description` 屬性的字串所定義。|
| EndpointAcl | 選擇性。 指定對端點之存取控制規則的指派。 包含端點的角色名稱是由 `role` 屬性的字串所定義。 端點的名稱是由 `endpoint` 屬性的字串所定義。 應套用至端點的 `AccessControl` 規則集名稱是由 `accessControl` 屬性的字串所定義。 可以定義多個 `EndpointAcl` 元素。|
| DnsServer | 選擇性。 指定 DNS 伺服器的設定。 您無須使用虛擬網路就可以指定 DNS 伺服器的設定。 DNS 伺服器的名稱是由 `name` 屬性的字串所定義。 DNS 伺服器的 IP 位址是由 `IPAddress` 屬性的字串所定義。 IP 位址必須是有效的 IPv4 位址。|
| VirtualNetworkSite | 選擇性。 指定您要在其中部署雲端服務的虛擬網路網站的名稱。 此設定不會建立虛擬網路站台。 它會參考先前已在虛擬網路網路檔案中定義的站台。 雲端服務只能是一個虛擬網路的成員。 如果您未指定此設定，雲端服務將不會部署到虛擬網路。 虛擬網路站台的名稱是由 `name` 屬性的字串所定義。|
| InstanceAddress | 選擇性。 在虛擬網路中，將角色的關聯指定為子網路或子網路集。 當您將角色名稱與執行個體位址建立關聯時，可以指定您需要與此角色相關聯的子網路。 `InstanceAddress` 包含一個 Subnets 元素。 與一個或多個子網路相關聯的角色名稱是由 `roleName` 屬性的字串所定義。|
| 子網路 | 選擇性。 指定對應至網路組態檔中之子網路名稱的子網路。 子網路的名稱是由 `name` 屬性的字串所定義。|
| ReservedIP | 選擇性。 指定應與部署相關聯的保留 IP 位址。 您必須使用「建立保留的 IP 位址」才能建立保留的 IP 位址。 雲端服務中的每個部署都可以與一個保留的 IP 位址相關聯。 保留 IP 位址的名稱是由 `name` 屬性的字串所定義。|

## <a name="see-also"></a>另請參閱
[雲端服務 () 設定架構的延伸支援](schema-cscfg-file.md)。
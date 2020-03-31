---
title: Azure VMware 雲簡單解決方案 - 為雲簡單私有雲選擇負載平衡解決方案
description: 描述在私有雲中部署應用程式的負載平衡選項
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6c98b699b1d3aba15ce69c519d35d7ce3e90d123
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77014873"
---
# <a name="choose-a-load-balancing-solution-for-cloudsimple-private-clouds"></a>為雲簡單私有雲選擇負載平衡解決方案

在 CloudSimple 私有雲中部署應用程式時，您可以選擇多個選項中的任何一個進行負載平衡。

您可以在 CloudSimple 私有雲中選擇虛擬或基於軟體的負載等化器，甚至使用 Azure 訂閱中運行的 Azure L7 負載等化器，以在雲簡單私有雲中運行的前端 Web 層 VM。 在這裡，我們列出了幾個選項：

## <a name="virtual-load-balancers"></a>虛擬負載等化器

您可以通過 vCenter 介面在 VMware 環境中部署虛擬負載等化器設備，並將它們配置為應用程式流量的前端。

一些流行的供應商是： NginX： http://nginx.org/en/docs/http/load_balancing.html F5- BigIP https://www.f5.com/products/big-ip-services/virtual-editions - 流量管理器： Citrix ADC：https://www.citrix.com/products/citrix-adc/

## <a name="azure-l7-load-balancer"></a>Azure L7 負載等化器

當您使用 Azure 應用程式閘道作為在私有雲中運行的應用程式的 L7 負載等化器時，不需要管理負載等化器軟體。 負載等化器軟體由 Azure 管理。 私有雲中的所有 Web 層 VM 都使用私人 IP 位址，無需其他 NAT 規則或公共 IP 位址來解析名稱。 Web 層 VM 通過專用、低延遲、高頻寬連接與 Azure 應用程式閘道通信。

要瞭解有關如何配置此解決方案的詳細資訊，請參閱有關使用 Azure 應用程式閘道作為 L7 負載等化器的解決方案指南。

## <a name="azure-internal-load-balancer"></a>Azure 內部負載等化器

如果選擇在混合部署中運行應用程式，其中 Web 前端層在 Azure 訂閱中的 Azure vNet 中運行，並且應用程式的資料庫層在 CloudSimple 私有雲中的 VMware VM 中運行，則可以使用 Azure 內部負載用於流量管理的資料庫層 VM 前面的平衡器（L4 負載等化器）。

要瞭解詳細資訊，請參閱 Azure[內部負載等化器](../load-balancer/concepts-limitations.md#internalloadbalancer)文檔。

## <a name="global-server-load-balancer"></a>全域伺服器負載等化器

如果要查找基於 DNS 的負載等化器，則可以使用 Azure 應用商店中提供的協力廠商解決方案，也可以使用本機 Azure 解決方案。

Azure 流量管理器是基於 DNS 的流量負載等化器，使您能夠以最佳方式將流量分發到跨全域 Azure 區域和本地的服務，同時提供高可用性和回應能力。 要瞭解詳細資訊，請參閱 Azure[流量管理器](../traffic-manager/traffic-manager-configure-geographic-routing-method.md)文檔。

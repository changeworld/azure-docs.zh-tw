---
title: Azure VMware Solution by CloudSimple-選擇適用于 CloudSimple 私人雲端的負載平衡解決方案
description: 說明在私人雲端中部署應用程式的負載平衡選項
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6c98b699b1d3aba15ce69c519d35d7ce3e90d123
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77014873"
---
# <a name="choose-a-load-balancing-solution-for-cloudsimple-private-clouds"></a>選擇適用于 CloudSimple 私人雲端的負載平衡解決方案

在 CloudSimple 私用雲端中部署應用程式時，您可以選擇多個負載平衡選項。

您可以選擇 CloudSimple 私人雲端中的虛擬或軟體型負載平衡器，或甚至是使用 Azure 訂用帳戶中執行的 Azure L7 負載平衡器，在 CloudSimple 私人雲端中執行的 web 層 Vm 前端。 在這裡，我們會列出幾個選項：

## <a name="virtual-load-balancers"></a>虛擬負載平衡器

您可以透過 vCenter 介面在 VMware 環境中部署虛擬負載平衡器設備，並將其設定為前端應用程式流量。

有些熱門廠商是： NginX： http://nginx.org/en/docs/http/load_balancing.html F5-BigIP-流量管理員： https://www.f5.com/products/big-ip-services/virtual-editions Citrix ADC：https://www.citrix.com/products/citrix-adc/

## <a name="azure-l7-load-balancer"></a>Azure L7 負載平衡器

當您使用 Azure 應用程式閘道做為在私人雲端中執行之應用程式的 L7 負載平衡器時，您不需要管理負載平衡器軟體。 負載平衡器軟體是由 Azure 管理。 私人雲端中的所有 web 層 Vm 都會使用私人 IP 位址，而不需要額外的 NAT 規則或公用 Ip 位址來解析名稱。 Web 層 Vm 會透過私人、低延遲、高頻寬的連線與 Azure 應用程式閘道通訊。

若要深入瞭解如何設定此解決方案，請參閱使用 Azure 應用程式閘道作為 L7 負載平衡器的解決方案指南。

## <a name="azure-internal-load-balancer"></a>Azure 內部負載平衡器

如果您選擇在混合式部署中執行應用程式，其中 web 前端層是在 Azure 訂用帳戶中的 Azure vNet 內執行，而應用程式的 DB 層是在 CloudSimple 私用雲端的 VMware Vm 中執行，您可以在您的 DB 層 Vm 前面使用 Azure 內部負載平衡器（L4 負載平衡器）來進行流量管理。

若要深入瞭解，請參閱 Azure[內部 Load Balancer](../load-balancer/concepts-limitations.md#internalloadbalancer)檔。

## <a name="global-server-load-balancer"></a>全域伺服器負載平衡器

如果您要尋找以 DNS 為基礎的負載平衡器，您可以使用 Azure Marketplace 或 go 中提供的協力廠商解決方案來搭配原生 Azure 解決方案。

Azure 流量管理員是以 DNS 為基礎的流量負載平衡器，可讓您以最佳方式將流量分散到全球 Azure 區域和內部部署的服務，同時提供高可用性和回應性。 若要深入瞭解，請參閱 Azure[流量管理員](../traffic-manager/traffic-manager-configure-geographic-routing-method.md)檔。

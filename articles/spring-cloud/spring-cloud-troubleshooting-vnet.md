---
title: 針對虛擬網路中的 Azure 春季 Cloud 進行疑難排解
description: Azure 春季雲端虛擬網路的疑難排解指南。
author: mikedodaro
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/19/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: c2b0488663233546411b3a1b5ac1b1eb6c0b899c
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98887150"
---
# <a name="troubleshooting-azure-spring-cloud-in-virtual-networks"></a>針對虛擬網路中的 Azure 春季 Cloud 進行疑難排解

本檔將協助您解決在虛擬網路中使用 Azure 春季雲端時可能發生的各種問題。

## <a name="i-encountered-a-problem-with-creating-an-azure-spring-cloud-service-instance"></a>我在建立 Azure 春季雲端服務實例時遇到問題

若要建立 Azure 春季 Cloud 的實例，您必須擁有足夠的許可權，才能將實例部署至虛擬網路。  彈簧雲端服務實例本身必須將 [虛擬網路的 Azure 春季雲端服務許可權授與 Azure](spring-cloud-tutorial-deploy-in-azure-virtual-network.md#grant-service-permission-to-the-virtual-network)。

如果您使用 Azure 入口網站設定 Azure 春季雲端服務實例，則 Azure 入口網站將會驗證許可權。

若要使用 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)設定 Azure 春季 Cloud 服務實例，請確認：

- 訂用帳戶是作用中狀態。
- Azure 春季雲端支援此位置。
- 已經建立執行個體的資源群組。
- 資源名稱符合命名規則。 它必須只包含小寫字母、數位和連字號。 第一個字元必須是字母。 最後一個字元必須是字母或數位。 值必須包含2到32個字元。

若要使用 Resource Manager 範本來設定 Azure 春季 Cloud 服務實例，請參閱 [瞭解 Azure Resource Manager 範本的結構和語法](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)。

### <a name="common-creation-issues"></a>常見的建立問題

| 錯誤訊息 | 修正方法 |
|------|------|
| 原則不允許 Azure 春季 Cloud 所建立的資源。 | 當您在自己的虛擬網路中部署 Azure 春季雲端時，會建立網路資源。 請檢查您是否已定義 [Azure 原則](https://docs.microsoft.com/azure/governance/policy/overview) 來封鎖這些建立。 您可以在錯誤訊息中找到無法建立的資源。 |
| 提供的子網與路由表相關聯，請將它們解除關聯。 | 目前不支援在與現有路由表相關聯的子網中部署 Azure 春季 Cloud，請將其解除關聯，然後再試一次。 |
| 必要的流量未列入允許清單。 | 請參閱 [在 VNET 中執行 Azure 春季 Cloud 的客戶責任](spring-cloud-vnet-customer-responsibilities.md) ，以確保所需的流量會列入允許清單。 |

## <a name="my-application-cant-be-registered"></a>無法註冊我的應用程式

如果您的虛擬網路設定了自訂 DNS 設定，就會發生此問題。 在此情況下，Azure 春季雲端所使用的私人 DNS 區域將會無效。 將 Azure DNS IP 168.63.129.16 新增為自訂 DNS 伺服器中的上游 DNS 伺服器。

## <a name="other-issues"></a>其他問題

針對[常見的 Azure 春季雲端問題進行疑難排解](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-troubleshoot)。
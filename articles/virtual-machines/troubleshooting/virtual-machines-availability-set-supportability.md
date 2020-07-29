---
title: 在現有的可用性設定組中新增 Azure VM 的可支援性 | Microsoft Docs
description: 本文提供可支援性的矩陣，說明您可以在同一個可用性設定組中混合的 VM 系列
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: a9ca8f219bef787de04b51600209bfd3a24dd166
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77122918"
---
# <a name="supportability-of-adding-azure-vms-to-an-existing-availability-set"></a>在現有的可用性設定組中新增 Azure VM 的可支援性

當您將新的虛擬機器 (VM) 新增至現有的可用性設定組時，偶爾可能會遇到限制。 下表詳細列出您可以在同一個可用性設定組中混合的 VM 系列。

以下是混合不同 VM 類型的可支援性對照表：

系列與可用性設定組|第二部 VM|A|Av2|D|Dv2|Dv3|
|---|---|---|---|---|---|---|
|第一部 VM|||||||
|A||確定|確定|確定|確定|確定|
|Av2||確定|確定|確定|確定|確定|
|D||確定|確定|確定|確定|確定|
|Dv2||確定|確定|確定|確定|確定|
|Dv3||確定|確定|確定|確定|確定|

其他所有系列由於需要特定硬體，因此無法存在於相同的可用性設定組。

因為專用 RDMA 後端網路的需求，所以無法混合 A8/A9 VM 大小。

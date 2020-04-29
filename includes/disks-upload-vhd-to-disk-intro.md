---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/27/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e96d205ef1a8f94baa3a0cfe6c5127b6cf570e5a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80420977"
---
本文說明如何使用 AzCopy，將 VHD 從本機電腦上傳至 Azure 受控磁片，或將受控磁碟複製到另一個區域。 這項程式直接上傳，也可讓您將 VHD 直接上傳至 32 TiB 到受控磁片。 目前，標準 HDD、標準 SSD 和 premium SSD 受控磁片支援直接上傳。 但對 ultra 磁片尚不支援。

如果您要在 Azure 中提供 IaaS Vm 的備份解決方案，建議您使用直接上傳來將客戶備份還原至受控磁片。 從外部的來源將 VHD 上傳至 Azure 時，速度會取決於您的本機頻寬。 從 Azure VM 上傳或複製時，您的頻寬會與標準 Hdd 相同。
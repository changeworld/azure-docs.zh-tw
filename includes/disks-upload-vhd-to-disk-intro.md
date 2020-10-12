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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "80420977"
---
本文說明如何使用 AzCopy，從本機電腦將 VHD 上傳至 Azure 受控磁片，或將受控磁碟複製到另一個區域。 此程式（直接上傳）也可讓您將 VHD 直接上傳至受控磁片，其大小最高可達 32 TiB。 目前，標準 HDD、標準 SSD 和 premium SSD 受控磁片支援直接上傳。 但目前不支援 ultra 磁片。

如果您要為 Azure 中的 IaaS Vm 提供備份解決方案，我們建議使用直接上傳將客戶備份還原至受控磁片。 從 Azure 外部的來源上傳 VHD 時，速度會取決於您的本機頻寬。 從 Azure VM 上傳或複製時，您的頻寬會與標準 Hdd 相同。
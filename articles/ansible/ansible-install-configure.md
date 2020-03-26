---
title: 快速入門 - 在 Azure 中的 Linux 虛擬機器上安裝 Ansible
description: 在本快速入門中，您將了解如何安裝及設定 Ansible，以便管理 Ubuntu、CentOS 和 SLES 上的 Azure 資源
keywords: ansible、azure、devops、bash、cloudshell、腳本、bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: gwallace
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 44007000475793005560914fd816cd0c16927f9a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "77202440"
---
# <a name="quickstart-install-ansible-on-linux-virtual-machines-in-azure"></a>快速入門：在 Azure 中的 Linux 虛擬機器上安裝 Ansible

Ansible 可讓您將環境中的資源部署和設定自動化。 本文將說明如何對某些最常見的 Linux 發行版設定 Ansible。 若要在其他發行版上安裝 Ansible，您可以配合特定平台來調整安裝的套件。 

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-sp.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
- **Linux 或 Linux 虛擬機器的存取權** - 如果您沒有 Linux 機器，請建立 [Linux 虛擬機器](/azure/virtual-network/quick-create-cli)。

## <a name="install-ansible-on-an-azure-linux-virtual-machine"></a>在 Azure Linux 虛擬機器上安裝 Ansible

登入您的 Linux 機器，並選取下列其中一個散發版本，以執行相關步驟來了解如何安裝 Ansible：

- [CentOS 7.4](#centos-74)
- Ubuntu 16.04 LTS
- [SLES 12 SP2](#sles-12-sp2)

### <a name="centos-74"></a>CentOS 7.4

在本節中，您將設定要使用 Ansible 的 CentOS。

1. 開啟終端機視窗。

1. 輸入下列命令來安裝 Azure Python SDK 模組所需的套件：

    ```bash
    sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
    sudo yum install -y python-pip python-wheel
    ```

1. 輸入下列命令以安裝必要的 Ansible 套件：

    ```bash
    sudo pip install ansible[azure]
    ```

1. [建立 Azure 認證](#create-azure-credentials)。

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

在本節中，您將設定要使用 Ansible 的 Ubuntu。

1. 開啟終端機視窗。

1. 輸入下列命令來安裝 Azure Python SDK 模組所需的套件：

    ```bash
    sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip
    ```

1. 輸入下列命令以安裝必要的 Ansible 套件：

    ```bash
    sudo pip install ansible[azure]
    ```

1. [建立 Azure 認證](#create-azure-credentials)。

### <a name="sles-12-sp2"></a>SLES 12 SP2

在本節中，您將設定要使用 Ansible 的 SLES。

1. 開啟終端機視窗。

1. 輸入下列命令來安裝 Azure Python SDK 模組所需的套件：

    ```bash
    sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
        python-devel libopenssl-devel libtool python-pip python-setuptools
    ```

1. 輸入下列命令以安裝必要的 Ansible 套件：

    ```bash
    sudo pip install ansible[azure]
    ```

1. 輸入下列命令以移除衝突的 Python 加密套件：

    ```bash
    sudo pip uninstall -y cryptography
    ```

1. [建立 Azure 認證](#create-azure-credentials)。

## <a name="create-azure-credentials"></a>建立 Azure 認證

若要設定 Ansible 認證，您需要下列資訊：

* 您的 Azure 訂用帳戶 ID 
* 服務主體值

如果您使用 Ansible Tower 或 Jenkins，請將服務主體值宣告為環境變數。

使用下列其中一種技術來設定 Ansible 認證：

- [建立 Ansible 認證檔案](#file-credentials)
- [使用 Ansible 環境變數](#env-credentials)

### <a name="span-idfile-credentials-create-ansible-credentials-file"></a><span id="file-credentials"/>建立 Ansible 認證檔案

在本節中，您將建立為 Ansible 提供認證的本機認證檔案。 

如需有關定義 Ansible 認證的詳細資訊，請參閱[提供認證給 Azure 模組](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules)。

1. 針對開發環境，請在主機虛擬機器上建立名為 `credentials` 的檔案：

    ```bash
    mkdir ~/.azure
    vi ~/.azure/credentials
    ```

1. 將下列幾行插入檔案。 以服務主體值取代預留位置。

    ```bash
    [default]
    subscription_id=<your-subscription_id>
    client_id=<security-principal-appid>
    secret=<security-principal-password>
    tenant=<security-principal-tenant>
    ```

1. 儲存並關閉檔案。

### <a name="span-idenv-credentialsuse-ansible-environment-variables"></a><span id="env-credentials"/>使用 Ansible 環境變數

在本節中，您會匯出服務主體值來設定 Ansible 認證。

1. 開啟終端機視窗。

1. 匯出服務主體值：

    ```bash
    export AZURE_SUBSCRIPTION_ID=<your-subscription_id>
    export AZURE_CLIENT_ID=<security-principal-appid>
    export AZURE_SECRET=<security-principal-password>
    export AZURE_TENANT=<security-principal-tenant>
    ```

## <a name="verify-the-configuration"></a>驗證組態

若要確認設定成功，請使用 Ansible 建立 Azure 資源群組。

[!INCLUDE [create-resource-group-with-ansible.md](../../includes/ansible-snippet-create-resource-group.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"] 
> [快速入門：使用 Ansible 在 Azure 中設定 Linux 虛擬機器](./ansible-create-vm.md)
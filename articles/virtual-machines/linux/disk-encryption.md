---
title: Azure 托管磁盘的服务器端加密 - Azure CLI
description: Azure 存储在将数据保存到存储群集之前会对其进行静态加密，以此保护数据。 可以使用 Microsoft 托管密钥加密你的托管磁盘，也可以使用客户托管密钥以通过自己的密钥管理所做的加密。
author: roygara
ms.date: 07/10/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: e0a1f97cc7467d115ecc8462a301e45f90d73818
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2020
ms.locfileid: "87449148"
---
# <a name="server-side-encryption-of-azure-disk-storage"></a>Azure 磁盘存储的服务器端加密

服务器端加密 (SSE) 可保护数据，并帮助实现组织安全性和符合性承诺。 默认情况下，在将存储在 Azure 托管磁盘（OS 和数据磁盘）上的数据保存到云时，SSE 会自动对其进行加密。 

Azure 托管磁盘中的数据使用 256 位 [AES 加密](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)（可用的最强大分组加密之一）以透明方式加密，且符合 FIPS 140-2 规范。 有关加密模块基础 Azure 托管磁盘的详细信息，请参阅[加密 API：下一代](/windows/desktop/seccng/cng-portal)

服务器端加密不会对托管磁盘的性能产生影响，并且不会产生额外的费用。 

## <a name="about-encryption-key-management"></a>关于加密密钥管理

可以依赖于平台托管的密钥来加密托管磁盘，也可以使用自己的密钥来管理加密。 如果选择使用自己的密钥管理加密，可以指定一个客户托管密钥，用于加密和解密托管磁盘中的所有数据。 

以下部分更详细地介绍了密钥管理的每个选项。

### <a name="platform-managed-keys"></a>平台托管的密钥

默认情况下，托管磁盘使用平台托管的加密密钥。 写入到现有托管磁盘的所有托管磁盘、快照、映像和数据都将通过平台管理的密钥自动进行静态加密。

### <a name="customer-managed-keys"></a>客户管理的密钥

[!INCLUDE [virtual-machines-managed-disks-description-customer-managed-keys](../../../includes/virtual-machines-managed-disks-description-customer-managed-keys.md)]

#### <a name="restrictions"></a>限制

目前，客户托管密钥具有以下限制：

- 如果为磁盘启用了此功能，则无法禁用它。
    如果需要解决此问题，则必须[复制所有数据](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk)到完全不同的托管磁盘（未使用客户托管密钥）。
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

> [!IMPORTANT]
> 客户托管密钥依赖于 Azure 资源的托管标识（Azure Active Directory (Azure AD) 的一项功能）。 配置客户托管密钥时，实际上会自动将托管标识分配给你的资源。 如果随后将订阅、资源组或托管磁盘从一个 Azure AD 目录移到另一个目录，则与托管磁盘关联的托管标识不会传输到新租户，因此，客户管理的密钥可能不再工作。 有关详细信息，请参阅[在 Azure AD 目录之间转移订阅](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)。

## <a name="encryption-at-host---end-to-end-encryption-for-your-vm-data"></a>VM 数据的主机端对端加密加密

当你在主机上启用加密时，将在 VM 主机上启动该加密，并将 VM 分配到 Azure 服务器。 临时磁盘和 OS/数据磁盘缓存的数据存储在该虚拟机主机上。 在主机上启用加密后，将静态加密所有这些数据，并将其传输到存储服务，并在其中保存。 实质上，主机加密会从端到端加密数据。 主机加密不会使用 VM 的 CPU，并且不会影响 VM 的性能。 

启用端对端加密时，临时磁盘使用平台管理的密钥进行静态加密。 操作系统和数据磁盘缓存会根据所选的磁盘加密类型，与客户管理的或平台管理的密钥加密。 例如，如果使用客户管理的密钥对磁盘进行加密，则使用客户管理的密钥对磁盘缓存进行加密，如果磁盘是使用平台托管密钥加密的，则使用平台托管密钥对磁盘缓存进行加密。

### <a name="restrictions"></a>限制

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

#### <a name="supported-regions"></a>支持的区域

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](../../../includes/virtual-machines-disks-encryption-at-host-regions.md)]

#### <a name="supported-vm-sizes"></a>支持的 VM 大小

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

## <a name="double-encryption-at-rest"></a>双静态加密

如果高安全敏感客户担心与任何特定加密算法、实现或密钥相关的风险，则现在可以选择使用平台托管的加密密钥，在基础结构层使用不同的加密算法/模式。 这一新的层可以应用于磁盘、快照和映像，所有这些都将以双加密方式静态加密。

### <a name="supported-regions"></a>支持的区域

[!INCLUDE [virtual-machines-disks-double-encryption-at-rest-regions](../../../includes/virtual-machines-disks-double-encryption-at-rest-regions.md)]

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>服务器端加密与 Azure 磁盘加密

[Azure 磁盘加密](../../security/fundamentals/azure-disk-encryption-vms-vmss.md)利用 Linux 的[DM dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt)功能，通过来宾 VM 中的客户托管密钥来加密托管磁盘。  使用客户托管密钥的服务器端加密改进了 ADE，它通过加密存储服务中的数据使你可以为 VM 使用任何 OS 类型和映像。

## <a name="next-steps"></a>后续步骤

- 使用[CLI](disks-enable-host-based-encryption-cli.md)或[Azure 门户](disks-enable-host-based-encryption-portal.md)在主机上启用端到端加密。
- 使用[CLI](disks-enable-double-encryption-at-rest-cli.md)或[Azure 门户](disks-enable-double-encryption-at-rest-portal.md)为托管磁盘启用静态加密。
- 使用[CLI](disks-enable-customer-managed-keys-cli.md)或[Azure 门户](disks-enable-customer-managed-keys-portal.md)为托管磁盘启用客户管理的密钥。
- [什么是 Azure 密钥保管库？](../../key-vault/general/overview.md)

---
title: 为虚拟机装载 Azure NetApp 文件卷
description: 了解如何在 Azure 中装载或卸载 Windows 虚拟机或 Linux 虚拟机的卷。
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 07/06/2020
ms.openlocfilehash: 4ad3800748330d5c3a6a32c6c0824bc72a05d0ef
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2020
ms.locfileid: "87533081"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>为 Windows 或 Linux 虚拟机装载或卸载卷 

可以根据需要为 Windows 或 Linux 虚拟机装载或卸载卷。  Azure NetApp 文件上提供了 Linux 虚拟机的装载说明。  

> [!IMPORTANT] 
> 必须至少有一个导出策略才能访问 NFS 卷。

1. 单击 "**卷**" 边栏选项卡，然后选择要装入的卷。 
2. 单击所选卷中的 "**装载说明**"，然后按照说明来装载卷。 

    ![装载说明 NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![装载说明 SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)  
    * 如果要装载 NFS 卷，请确保使用 `vers` 命令中的选项 `mount` 来指定与要装载的卷相对应的 NFS 协议版本。 
    * 如果使用的是 NFSv 4.1，请使用以下命令来装载文件系统：`sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  

3. 如果希望在启动或重新启动 Azure VM 时自动装载 NFS 卷，请将条目添加到 `/etc/fstab` 主机上的文件中。 

    例如：`$ANFIP:/$FILEPATH        /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

    * `$ANFIP`是在 "卷属性" 边栏选项卡中找到的 Azure NetApp 文件卷的 IP 地址。
    * `$FILEPATH`是 Azure NetApp 文件卷的导出路径。
    * `$MOUNTPOINT`是在 Linux 主机上创建的用于装载 NFS 导出的目录。

4. 如果要使用 NFS 将卷装载到 Windows，请执行以下操作：

    a. 首先将卷装载到 Unix 或 Linux VM。  
    b. `chmod 777` `chmod 775` 对该卷运行或命令。  
    c. 通过 Windows 上的 NFS 客户端装载卷。
    
5. 如果要装载 NFS Kerberos 卷，请参阅[Configure nfsv 4.1 Kerberos encryption](configure-kerberos-encryption.md)了解更多详细信息。 

## <a name="next-steps"></a>后续步骤

* [为 Azure NetApp 文件配置 NFSv4.1 默认域](azure-netapp-files-configure-nfsv41-domain.md)
* [NFS 常见问题解答](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#nfs-faqs)
* [网络文件系统概述](https://docs.microsoft.com/windows-server/storage/nfs/nfs-overview)
* [装载 NFS Kerberos 卷](configure-kerberos-encryption.md#kerberos_mount)

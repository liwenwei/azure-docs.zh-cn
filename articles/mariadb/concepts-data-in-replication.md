---
title: 数据复制-Azure Database for MariaDB
description: 了解如何使用数据传入复制从外部服务器同步到 Azure Database for MariaDB 服务。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 1fbcc1fb27d5e6df4641f79c0d634580f74000b8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "79532054"
---
# <a name="replicate-data-into-azure-database-for-mariadb"></a>将数据复制到 Azure Database for MariaDB

通过数据传入复制，可将数据从本地运行的 MariaDB 服务器、虚拟机中或其他云提供程序托管的数据库服务中同步到 Azure Database for MariaDB 服务。 数据传入复制功能依靠的是基于二进制日志 (binlog) 文件位置、从本机到 MariaDB 的复制。 要详细了解 binlog 复制，请参阅 [binlog 复制概述](https://mariadb.com/kb/en/library/replication-overview/)。

## <a name="when-to-use-data-in-replication"></a>何时使用配置复制中数据
可以考虑使用复制中数据的主要场景有：

- **混合数据同步**：通过数据传入复制，可在本地服务器与 Azure Database for MariaDB 之间同步数据。 此同步可用于创建混合应用程序。 如果有现有的本地数据库服务器，但想要将数据移到更靠近最终用户的区域，那么此方法很有吸引力。
- **多云同步**：在复杂的云解决方案中，可利用数据传入复制功能在 Azure Database for MariaDB 与不同的云提供程序之间同步数据，包括在虚拟机与这些云中托管的数据库服务之间。

## <a name="limitations-and-considerations"></a>限制和注意事项

### <a name="data-not-replicated"></a>不会复制的数据
不会复制主服务器上的 [mysql 系统数据库](https://mariadb.com/kb/en/library/the-mysql-database-tables/)**。 不会复制对主服务器上的帐户和权限所做的更改。 如果在主服务器上创建帐户，并且此帐户需要访问副本服务器，则在副本服务器上手动创建相同的帐户。 要了解系统数据库中包含哪些表，请参阅 [MariaDB 文档](https://mariadb.com/kb/en/library/the-mysql-database-tables/)。

### <a name="requirements"></a>要求
- 主服务器版本不得低于 MariaDB 10.2 版本。
- 主服务器版本和副本服务器版本必须相同。 例如，两者都必须是 MariaDB 10.2 版本。
- 每个表都必须有主键。
- 主服务器应使用 InnoDB 引擎。
- 用户必须具有权限才能在主服务器上配置二进制日志记录和创建新用户。
- 如果主服务器启用了 SSL，请确保为域提供的 SSL CA 证书已包含在 `mariadb.az_replication_change_master` 存储过程中。 请参阅以下[示例](https://docs.microsoft.com/azure/mariadb/howto-data-in-replication#link-the-master-and-replica-servers-to-start-data-in-replication)和 `master_ssl_ca` 参数。
- 确保主服务器的 IP 地址已添加到 Azure Database for MariaDB 副本服务器的防火墙规则中。 使用 [Azure 门户](https://docs.microsoft.com/azure/mariadb/howto-manage-firewall-portal)或 [Azure CLI](https://docs.microsoft.com/azure/mariadb/howto-manage-firewall-cli) 更新防火墙规则。
- 确保托管主服务器的计算机在端口 3306 上允许入站和出站流量。
- 请确保主服务器具有**公共 IP 地址**，DNS 可公开访问，或具有完全限定的域名 (FQDN)。

### <a name="other"></a>其他
- 仅可在常规用途和优化内存定价层中使用数据传入复制功能。

## <a name="next-steps"></a>后续步骤
- 了解如何[设置数据复制](howto-data-in-replication.md)。

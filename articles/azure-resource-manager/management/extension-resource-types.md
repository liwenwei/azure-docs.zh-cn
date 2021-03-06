---
title: 扩展资源类型
description: 列出用于扩展其他资源类型的功能的 Azure 资源类型。
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: 84de9b66f9001985b8c7b92882f03ff8c7cbf431
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87374008"
---
# <a name="resource-types-that-extend-capabilities-of-other-resources"></a>用于扩展其他资源的功能的资源类型

扩展资源是扩展另一资源的功能的资源。 例如，资源锁是扩展资源。 对另一资源应用资源锁可以防止该资源被删除或修改。 单独创建资源锁没有意义。 扩展资源始终会应用到其他资源。

## <a name="extension-resource-types"></a>扩展资源类型

- Microsoft.Advisor/configurations
- Microsoft.Advisor/recommendations
- Microsoft.Advisor/suppressions
- Microsoft.AlertsManagement/alerts
- Microsoft.AlertsManagement/alertsSummary
- Microsoft.Authorization/checkAccess
- Microsoft.Authorization/denyAssignments
- Microsoft.Authorization/findOrphanRoleAssignments
- Microsoft.Authorization/locks
- Microsoft.Authorization/permissions
- Microsoft.Authorization/policyAssignments
- Microsoft.Authorization/policyDefinitions
- Microsoft. Authorization/policyExemptions
- Microsoft.Authorization/policySetDefinitions
- Microsoft. Authorization/privateLinkAssociations
- Microsoft.Authorization/roleAssignments
- Microsoft.Authorization/roleAssignmentsUsageMetrics
- Microsoft.Authorization/roleDefinitions
- Microsoft.Billing/billingPeriods
- Microsoft.Billing/billingPermissions
- Microsoft.Billing/billingRoleAssignments
- Microsoft.Billing/billingRoleDefinitions
- Microsoft.Billing/createBillingRoleAssignment
- Microsoft.Blueprint/blueprintAssignments
- Microsoft.Blueprint/blueprints
- ChangeAnalysis/resourceChanges
- Microsoft.Consumption/AggregatedCost
- Microsoft.Consumption/Balances
- Microsoft.Consumption/Budgets
- Microsoft.Consumption/Charges
- Microsoft.Consumption/CostTags
- Microsoft.Consumption/Forecasts
- Microsoft.Consumption/Marketplaces
- Microsoft.Consumption/OperationResults
- Microsoft.Consumption/OperationStatus
- Microsoft.Consumption/Pricesheets
- Microsoft.Consumption/ReservationDetails
- ReservationRecommendationDetails/
- Microsoft.Consumption/ReservationRecommendations
- Microsoft.Consumption/ReservationSummaries
- Microsoft.Consumption/ReservationTransactions
- Microsoft.Consumption/Tags
- Microsoft.Consumption/Terms
- Microsoft.Consumption/UsageDetails
- Microsoft.Consumption/credits
- Microsoft.Consumption/events
- Microsoft.Consumption/lots
- Microsoft.Consumption/products
- Microsoft.Consumption/tenants
- Microsoft.ContainerInstance/serviceAssociationLinks
- Microsoft.CostManagement/Alerts
- Microsoft.CostManagement/Budgets
- CostManagement/costAllocationRules
- Microsoft.CostManagement/Dimensions
- Microsoft.CostManagement/Exports
- Microsoft.CostManagement/ExternalSubscriptions
- Microsoft.CostManagement/Forecast
- Microsoft.CostManagement/Query
- Microsoft.CostManagement/Reportconfigs
- Microsoft.CostManagement/Reports
- Microsoft.CostManagement/showbackRules
- Microsoft.CostManagement/Views
- Microsoft.CustomProviders/associations
- Microsoft.EventGrid/eventSubscriptions
- Microsoft.EventGrid/extensionTopics
- Microsoft.GuestConfiguration/configurationProfileAssignments
- Microsoft.GuestConfiguration/guestConfigurationAssignments
- Microsoft.GuestConfiguration/software
- Microsoft.GuestConfiguration/softwareUpdateProfile
- Microsoft.GuestConfiguration/softwareUpdates
- microsoft.insights/baseline
- microsoft.insights/calculatebaseline
- microsoft.insights/dataCollectionRuleAssociations
- microsoft.insights/diagnosticSettings
- microsoft.insights/diagnosticSettingsCategories
- microsoft.insights/eventtypes
- microsoft.insights/extendedDiagnosticSettings
- microsoft.insights/guestDiagnosticSettingsAssociation
- microsoft.insights/logDefinitions
- microsoft.insights/logs
- microsoft.insights/metricDefinitions
- microsoft.insights/metricNamespaces
- microsoft.insights/metricbaselines
- microsoft.insights/metrics
- microsoft.insights/myWorkbooks
- microsoft.insights/topology
- microsoft.insights/transactions
- microsoft.insights/vmInsightsOnboardingStatuses
- Microsoft.KubernetesConfiguration/sourceControlConfigurations
- Microsoft.Maintenance/applyUpdates
- Microsoft.Maintenance/configurationAssignments
- Microsoft.Maintenance/updates
- Microsoft.ManagedIdentity/Identities
- Microsoft.ManagedServices/registrationAssignments
- Microsoft.ManagedServices/registrationDefinitions
- Microsoft.OperationalInsights/storageInsightConfigs
- Microsoft.OperationsManagement/managementassociations
- PolicyInsights/证明
- Microsoft.PolicyInsights/policyEvents
- Microsoft.PolicyInsights/policyStates
- Microsoft.PolicyInsights/policyTrackedResources
- Microsoft.PolicyInsights/remediations
- Microsoft.RecoveryServices/backupProtectedItems
- Microsoft.RecoveryServices/replicationEligibilityResults
- Microsoft.ResourceHealth/availabilityStatuses
- Microsoft.ResourceHealth/childAvailabilityStatuses
- Microsoft.ResourceHealth/childResources
- Microsoft.ResourceHealth/events
- Microsoft.ResourceHealth/impactedResources
- Microsoft.ResourceHealth/notifications
- Microsoft.Resources/links
- Microsoft.Resources/tags
- Microsoft.Security/Compliances
- Microsoft.Security/InformationProtectionPolicies
- Microsoft.Security/adaptiveNetworkHardenings
- Microsoft.Security/advancedThreatProtectionSettings
- Microsoft.Security/assessmentMetadata
- Microsoft.Security/assessments
- Microsoft.Security/complianceResults
- Microsoft.Security/dataCollectionAgents
- Microsoft.Security/deviceSecurityGroups
- Microsoft. Security/jitPolicies
- Microsoft.Security/serverVulnerabilityAssessments
- Microsoft.SecurityInsights/aggregations
- Microsoft.SecurityInsights/alertRuleTemplates
- Microsoft.SecurityInsights/alertRules
- SecurityInsights/automationRules
- Microsoft.SecurityInsights/bookmarks
- Microsoft.SecurityInsights/cases
- Microsoft.SecurityInsights/dataConnectors
- SecurityInsights/dataConnectorsCheckRequirements
- Microsoft.SecurityInsights/entities
- Microsoft.SecurityInsights/entityQueries
- SecurityInsights/事件
- Microsoft.SecurityInsights/officeConsents
- Microsoft.SecurityInsights/settings
- SecurityInsights/threatIntelligence
- Microsoft.SoftwarePlan/hybridUseBenefits
- Microsoft.Subscription/CreateSubscription
- microsoft.support/supporttickets
- Microsoft.WorkloadMonitor/components
- Microsoft.WorkloadMonitor/monitorInstances
- Microsoft.WorkloadMonitor/monitors
- Microsoft.WorkloadMonitor/notificationSettings

## <a name="next-steps"></a>后续步骤

- 若要在 Azure 资源管理器模板中获取扩展资源的资源 ID，请使用 [extensionResourceId](../templates/template-functions-resource.md#extensionresourceid)。
- 有关在模板中创建扩展资源的示例，请参阅[事件网格事件订阅](/azure/templates/microsoft.eventgrid/2019-06-01/eventsubscriptions)。

---
layout: post
title: 'Location Resource IDs in Azure'
date: '2023-03-09T07:00:00.000-05:00'
author: Tom
tags:
- azure
- terraform
- continuous-delivery
- hybrid-connection
---
I have been using [Terraform][terraform] for a few months in a continuous delivery pipeline to automatically deploy
Azure Function apps. One of the apps requires accessing an on-prem database that is well-protected due to the sensitive
nature of the data it contains. There are a couple of ways to facilitate this connection - [VNet Integration][vnet-integration]
and [Hybrid Connections][hybrid-connection]. We have been using hybrid connections.

There are multiple apps that need to access this database and each app is provisioned and deployed through its own
continuous delivery pipeline. The way Terraform works is that each pipeline has its own state file that tracks the
resources created and updated for each run. Ideally, you want all the resources required for an application to be
managed by the same pipeline. However, from a security standpoint, we do not want a hole through the firewall for each
of the apps.

Therefore, there is only one hybrid connection, and it is managed separately from the pipelines. Terraform makes it
simple to declare a resource and then refer to it elsewhere in the configuration. You can view an example of this
process in the [documentation][terraform-hybrid-connection] for the `azurerm-function-app-hybrid-connection` statement.
Our pipeline needs to refer to a hybrid connection that is provisioned separately, however.
 
It turns out that the `azurerm_function_app_hybrid_connection` is still the correct declarative statement to use. I just
need to determine the correct value for the `relay_id` argument, since I cannot point to a relay that was created in the
same pipeline. How do you find the ID of the relay? The documentation for this argument is sparse, to say the least.

To save you some trouble, Terraform is not expecting a database ID or GUID, instead it is expecting a resource ID. Each
resource in Azure has an ID that looks similar to a URL. Sometimes the resources have a "JSON View" link in the Overview
tab where you can find the resource ID. However, Microsoft also correlated the URLs in Azure with the resource IDs. If
you go to the resource in Azure and look at the URL, the resource ID is most everything after `resource`. For example
with the URL of `https://portal.azure.com/#@tpwalke2.com/resource/subscriptions/bae52486-f21a-41b0-9a56-2029d55c7fff/resourceGroups/tpwalke2azure/providers/Microsoft.Relay/namespaces/tpwalke2blogpost/hybridconnections/testing/overview`
the resource ID is `/subscriptions/bae52486-f21a-41b0-9a56-2029d55c7fff/resourceGroups/tpwalke2azure/providers/Microsoft.Relay/namespaces/tpwalke2blogpost/hybridConnections/testing`.

Note that `overview` at the end of the URL is not part of the resource ID. Another gotcha I encountered with this method
of finding the resource ID for terraform is that the URL uses `hybridconnections` while the resource ID should actually
be `hybridConnections`.

[terraform]: https://www.terraform.io/
[vnet-integration]: https://learn.microsoft.com/en-us/azure/app-service/configure-vnet-integration-enable
[hybrid-connection]: https://learn.microsoft.com/en-us/azure/app-service/app-service-hybrid-connections
[terraform-hybrid-connection]: https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/function_app_hybrid_connection
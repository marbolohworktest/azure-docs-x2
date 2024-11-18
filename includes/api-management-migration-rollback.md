---
author: dlepow
ms.service: azure-api-management
ms.topic: include
ms.date: 03/01/2024
ms.author: danlep
---

## Revert automatically if migration fails

If there's a failure during the migration process, the instance will automatically revert to the `stv1` platform. If the migration completes successfully (the platform version of the instance shows as `stv2` or `stv2.1` and the status as **Online**), you can't roll back to the `stv1` platform.

For help if migration fails, contact Azure support.

If you need the capability to roll back manually, the recommendation is to deploy a new `stv2` instance [side-by-side with your original API Management instance](../articles/api-management/migrate-stv1-to-stv2.md#alternative-side-by-side-deployment). 

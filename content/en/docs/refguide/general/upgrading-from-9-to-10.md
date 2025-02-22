---
title: "Upgrading from Mendix Studio Pro 9 to 10"
url: /refguide/upgrading-from-9-to-10/
linktitle: "Upgrade from Studio Pro 9 to 10"
category: "General Info"
weight: 20
description: "Provides details on upgrading your app from Studio Pro 9 to Studio Pro 10, including sections on converting your app and deprecated features."
tags: ["studio pro", "upgrade", "runtime", "xpath"]
---

## 1 Introduction

Mendix Studio Pro 10 is a [major version](/releasenotes/studio-pro/lts-mts/#major-version) release that provides powerful tools for building your apps and brings a host of improvements and fixes. For the full list of changes, see the [Studio Pro 10 release notes](/releasenotes/studio-pro/10.0/).

{{% alert color="warning" %}}
Mendix Studio Pro [10.0](/releasenotes/studio-pro/10.0/) is currently in [Beta](/releasenotes/beta-features/). The GA release is in June, 2023, as scheduled in the [Studio Pro release timeline](/releasenotes/studio-pro/lts-mts/#major-version).
{{% /alert %}}

### 1.1 Upgrading from Studio Pro 9 to 10

The sections below describe upgrading from Studio Pro 9 to Studio Pro 10.

Mendix recommends reviewing the [Breaking Changes](/releasenotes/studio-pro/10.0/#breaking-changes) section of the *Studio Pro 10.0* release notes as well as the updated [System Requirements](/refguide/system-requirements/).

### 1.2 Upgrading from Older Versions of Studio Pro

If your app is on a Studio Pro version below 9, you must upgrade sequentially. That means you must go from 7 to 8 (see details in [Moving from Desktop Modeler Version 7 to Studio Pro 8](https://docs.mendix.com/refguide8/moving-from-7-to-8/), from 8 to 9 (see details in [Moving from Mendix Studio Pro 8 to 9](/refguide9/moving-from-8-to-9/), and finally from 9 to 10. 

If your app is running on Mendix Cloud, you can check what version the app is currently on by referring to the [Control Center dashboard](/developerportal/control-center/#dashboard). Alternatively, contact your Customer Success Manager to find out how to check the Mendix version of your app.

## 2 Backing Up Your App

Make sure that you have either committed your latest changes to Team Server, or created a backup of your local app before you start the conversion.

## 3 Preparing Your App in Studio Pro 9.24

Mendix recommends preparing your app in Studio Pro [9.24](/releasenotes/studio-pro/9.24/) first to be able to upgrade it to Mendix 10.

To prepare your app in Studio Pro 9.24, follow these steps:

1. Download the latest patch release of Studio Pro [9.24](/releasenotes/studio-pro/9.24/).
2. Open your app in Studio Pro 9.24.
3. Allow Studio Pro to update the app.
4. Run your app, test all functionality, and ensure it works without error. 
5. Fix any depreciation warnings you see both in development in Studio Pro as well as in the Mendix Runtime using your console and browser console.
6. Review your app in combination with the sections below and assess if further action is needed before upgrading to Studio Pro 10.
7. Back up or commit your 9.24 app so that you can return to it if necessary.

Your app is now ready to be upgraded to Mendix 10. You can now close the app in Studio Pro 9.

## 4 Upgrading to Studio Pro 10

Open your app in Studio Pro 10 and allow Studio Pro to update your app to version 10. Mendix will update your app for you automatically.

Review all error messages and messages about deprecated items, then make changes where necessary.

## 5 Upgrading Widgets, Modules, and Marketplace Components {#upgrade-widgets}

To minimize the chance of problems, you should update all the widgets, modules, and other Marketplace components your app uses to the latest version.

Check if there is a newer version of your component available in the Marketplace. Read the version release notes in the Marketplace to see if you need to perform specific actions when upgrading.

Be sure to update these key widgets, resources, and actions:

* [Native Mobile Resources](https://marketplace.mendix.com/link/component/109513)
* [Nanoflow Commons](https://marketplace.mendix.com/link/component/109515)
* [Community Commons](https://marketplace.mendix.com/link/component/170)
* [Data Widgets](https://marketplace.mendix.com/link/component/116540)

In general you should not remove and re-import modules unless this is recommended in the component's release notes. If you do remove and re-import a component, you may lose data or configuration related to the component.

## 6 Reviewing and Testing Your App

Finally, review the sections below and ensure that you have made all the changes necessary. Test the app for any unexpected results.

{{% alert color="success" %}}
Congratulations! Your app has been successfully upgraded to Studio Pro 10 and you can continue working as normal.
{{% /alert %}}

## 7 Notable and Breaking Changes

### 7.1 Legacy Scheduled Events

Legacy scheduled events (namely, those that are non-repeating or have a start time) are no longer supported. These were already visible as a deprecation warning in Studio Pro 9, and this has now changed to an error. The error describes the issue and how to fix it. The Mendix Runtime will fail to start if legacy scheduled events exist.

### 7.2 Runtime API Changes

Most of the Java API calls that were deprecated in Mendix 9 have been removed. If you were still using such methods in your Java actions, you must replace or delete them. To check which calls were deprecated, see the [Mendix 9 Server Runtime API](https://apidocs.rnd.mendix.com/9/runtime/index.html).

Additionally, refer to the [Studio Pro 10 release notes](/releasenotes/studio-pro/10.0/) for more Runtime API change details.

### 7.3 XPath Query Engine Updates

With Studio Pro 9, we introduced a new query engine. During that introduction, some behavior changed (as described in the [XPath Query Engine 9](/refguide9/moving-from-8-to-9/#query-engine-9) section of *Moving from Mendix Studio Pro 8 to 9*). 

In Studio Pro 10, there are some improvements to consistency and behavior:

* Ranges that are collections of ranges are handled correctly. Prior to Studio Pro 10, the processing stopped at a `NULL` value in a collection (and did not process any range after that).
* In Studio Pro 9 and below, Long paths in the conditions of outer joins did not work correctly and yielded an incorrect result. This was because the predicate was moved from the `ON` clause to the `WHERE` clause. As of Studio Pro 10, it is prohibited to use associations in the `ON` clause of an outer `JOIN` (meaning, `LEFT JOIN`, `RIGHT JOIN`, or `FULL JOIN`).
* Prior to Studio Pro 10, the operator in a binary expression with a collection or range as (right-hand) operand was ignored. Regardless of the operator, it was interpreted as `IN`. As of Studio Pro 10, this is no longer the case, and an exception is thrown for all operators other than `IN` or `=`. Please note this includes `!=`, which is not supported.

### 7.4 Range Expressions

The following function invocations are always `True` or `False`:

* Comparisons to `RANGEBEGIN` with a range having a `NULL` lower bound
* Comparisons to `RANGEEND` with a range having a `NULL` upper bound
* `IN` with an infinite range (meaning, `NULL` for both lower and upper bound)

Prior to Studio Pro 10, these functions were removed from a predicate, which was not always correct. For example, `predicate OR` `attribute >` `RANGEBEGIN((NULL, 42))` is equivalent to `predicate OR True`, which is not the same as just `predicate` (with the range function removed).

As of Studio Pro 10, the tautology range functions are no longer removed, but simply substituted by `True` or `False`. Subsequently, the expressions are optimized where possible (for example, `x OR True` becomes `True`, `x AND False` becomes `False`, and `NOT(True)` becomes `False`).

## 8 Read More

* [Studio Pro 10 Release Notes](/releasenotes/studio-pro/10.0/)

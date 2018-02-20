---
layout: post
title: "Deploy Microsoft Office Add-ins from O365 Admin"
date: 2018-02-04
---

## Purpose

Provice a standard method for deploying Microsoft Office Add-ins to native and web apps.

## Scope

You must have access to the client O365 Admin account.

Following are the requirements necessary to use the Centralized Deployment feature:

- Your users must be using Office Professional Plus 2016 on the following operating systems:
- Windows: build 16.0.8027 or later
- Mac: build 15.33.170327 or above
- Your users must sign into Office 2016 using their Organizational ID.

## Process

### Check compatibility

*This appears to be only for Word and Excel add-ins. The add-in itself is only available for those apps. This appears to be a Mac dead-end as of 12 Dec 2017 as add-ins in those apps are not currently supported.*

1. Log into the O365 Admin account.
2. Go to 'Settings > Services & add-ins'
2. Get the Compatibility Checker https://aka.ms/officeaddindeploymentselfcompatibilitychecker

### Add an Add-in

1. Log into the O365 Admin account.
2. Go to 'Settings > Services & add-ins'
3. Click ***Upload Add-in***.
4. In the **Centralized Deployment** slide-over, click ***Next***.
5. Choose the radio button next to *I want to add an Add-In from the Office Store* then click ***Next***.
6. In the *Search* box, type the name of your add-in and tap enter.
7. To the right of your desired add-in, click ***Add***.
8. In the next screen, verify the add-in and click ***Next***.
9. Choose who will get assigned the add-in then click ***Next*** *In most cases, you will choose "Everyone". If you have a special request, you will need to have created a group for the target users before getting here.*
10. Click ***Close***.

### Remove an Add-in

**NOTE:** *This will remove the add-in for all users. You should plan ahead, assure that your POC is on board with the removal and make plans for employees who want to keep the add-in (if allowed by the client).*

1. Log into the O365 Admin account.
2. Go to 'Settings > Services & add-ins'
3. Click on the add-in you want to remove.
4. Click ***Delete Add-in***.
5. Verify the removal by clicking ***Yes***.
6. Click ***Close***.

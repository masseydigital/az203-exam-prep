# AZ-203 Exam Prep Course Notes
This is my course notes repo for AZ-203 Developing Solutions for Microsoft Azure Exam Prep.

## Virtual Machines
A _Virtual Machine (VM)_ is essentially an instance of a computer that includes its own memory, ram, OS, etc.  Infrastructure as a Service (IAAS).  Can use SSH or RDP to control remotely.  Each Azure VM has its own pricing plan which bases with per second pricing, and also has yearly pricing plans.  Some high performance VM's are blocked by default to protect the user from purchasing something that costs a lot of money.

Every resource that you create must live in a resource group.  Resource groups allow you to bundle resources into similar functions, and helps manage cost planning.

By default, VM's block public communication on their ports.  Users are able to allow selected ports to be open based on their funtionality.

You can create _ARM templates_ (JSON files) that can be stored in Azure or downloaded to machine to expedite the creation process for VM's and other Azure resources.

You can configure a DNS name to map to your public ip address.

To connect as a web server through public ip address, go to Connect in the toolbar.  For windows, you will connect through RDP and for linux you will connect through SSH.  RDP is port 3389.  You can download an RDP file to download and connect through RDP to your VM.

The files in an Azure storage account are by default encrypted _secure storage encryption_.  The VHD itself is not encrypted though, but you can encrypt with Bitlocker.  Keys can be stored in a _Key Vault_ azure resource.  Key lockers can store Keys, Secrets, and Certificates.  Encryption can only be done with standard resources and above.

The _Azure Cloud Shell_ allows you to run commands in bash or powershell within the browser.  To run Azure Cloud Shell, you need to have a storage account.

## Azure Batch

# Office 365 IP Sync to VMware VCO

This Python script fetches the current list of Office 365 service IP ranges from Microsoft's public endpoint and syncs them to corresponding object groups in a VMware VeloCloud Orchestrator (VCO) environment.

---

## Overview

VMware VeloCloud Orchestrator (VCO) **does not natively support dynamic syncing of third-party IP address feeds**, such as those published by Microsoft for Office 365. As Microsoft frequently updates its service IP ranges, this lack of native support creates an operational pain for admins to **manually monitor, classify, and update firewall or object group entries** as the lists can be very very long and also helps avoid any unexpected late night pager duties.

This script eliminates that manual overhead by automatically:

* Pulling Microsoft’s latest IP list for Office 365 services
* Classifying IPs into Exchange, SharePoint, Skype, and Common service groups
* Converting CIDR blocks into subnet masks
* Updating the corresponding object groups on VCO via API

This ensures endpoint definitions are always current when using object groups.

---

## Features

* Retrieves IPv4 addresses for Office 365 services (Common, Exchange, SharePoint, Skype)
* Converts CIDR to subnet masks
* Groups IPs by service and formats them for VCO object group updates
* Sends updates to VCO API for predefined object group IDs

---

## Requirements

* Python 3.6+
* Internet access to reach `https://endpoints.office.com`
* Access to a VMware VCO environment
* Valid API token and enterprise ID for VCO
* Installed Python modules:

  ```bash
  pip install requests
  ```

---

## Configuration

Update the following variables in the script before running:

```python
client_id = "your Office 365 client id here"
api_token = "your VCO API token"
enterprise_id = "your VCO enterprise ID"
```

Ensure you replace the `object_group_id` values if your VCO object groups differ from the defaults:

```python
# Example:
"O365-Common-API": "99999"
```

---

## How It Works

1. Calls the Office 365 endpoints API with your `client_id`.
2. Parses the returned IP blocks and classifies them into groups (Exchange, SharePoint, Skype, etc.).
3. Converts CIDR notation to subnet masks.
4. Constructs a payload for each group.
5. Sends the data to the appropriate object group in your VCO.

---

## Example Output

```
Object group O365-Common-API updated successfully.
Object group O365-Exchange-API updated successfully.
Object group O365-SharePoint-API updated successfully.
Object group O365-Skype-API updated successfully.
```

If a failure occurs:

```
Failed to update object group O365-Exchange-API.
<Response text with error details>
```

---

## Notes

* The script skips IPv6 addresses (`NoIPv6=true`).
* If a service doesn't return any IPs, it will be skipped.
* All service areas default to the group `O365-Common-API` unless matched to Exchange, SharePoint, or Skype explicitly.

---

## Disclaimer

This script is provided as-is. Always test in a non-production environment before deploying to production.

# Palo Alto Venafi Certificate Automation

This Ansible playbook automates the process of generating and managing certificates for Palo Alto Networks devices using Venafi as the Certificate Authority (CA).

A key benefit of this playbook is that the private key for the certificate is not included in the certificate file, which is a security feature to prevent unauthorized access to the private key. The key is left on the device as a security measure.

## Overview

The playbook performs the following operations:
1. Generates a Certificate Signing Request (CSR) on the Palo Alto device
2. Exports the CSR from the device
3. Submits the CSR to Venafi for signing
4. Downloads the signed certificate
5. Imports the certificate back to the Palo Alto device

## Prerequisites

- Ansible
- Access to a Palo Alto Networks device
- Venafi account and access token
- Ansible packages:
  - paloaltonetworks.panos (https://pypi.org/project/paloaltonetworks.panos/) and it's dependencies.

## Configuration

Before running the playbook, update the following variables in `palo_venafi_cert_auto.yml`:

- `cert_filename`: The name for the certificate on the device
- `cert_name`: The fully qualified domain name for the certificate
- `venafi_server`: Your Venafi server subdomain
- `device.ip_address`: IP address of your Palo Alto device
- `device.api_key`: API key for your Palo Alto device
- Organization details in the CSR generation task:
  - `org`: Your organization name
  - `oum`: Your organization unit member
  - Email address
  - Location details (country, state, locality)

## Usage

Run the playbook using:

```bash
ansible-playbook palo_venafi_cert_auto.yml -e "ansible_limit=your_host_group"
```

Make sure to set the following environment variables or include them in your inventory:
- `pa_api_key`: Palo Alto API key
- `access_token`: Venafi API access token

## Alternative Certificate Authorities

While this playbook is configured for Venafi, it can be adapted to work with any third-party Certificate Authority. The key sections to modify would be:

1. Replace the Venafi API endpoints in the "Enroll CSR import" and "Download certificate(s)" tasks
2. Adjust the API authentication method to match your CA's requirements
3. Modify the request/response format according to your CA's API specifications

The core functionality of CSR generation and certificate handling on the Palo Alto device remains the same regardless of the CA used.

## Security Notes

- The playbook includes `no_log: true` for sensitive operations to prevent exposing confidential information in Production logs, such as API keys. You can remove this option if needed.
- API keys and access tokens should be stored securely and not in plain text
- Certificate operations use 2048-bit RSA keys

## Error Handling

The playbook includes retry logic for Venafi operations:
- Certificate enrollment: 10 retries with 15-second delays
- Certificate download: 10 retries with 15-second delays

## Support

For issues or questions, please contact your system administrator or create an issue in the repository.

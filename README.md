
# role_openscap

## Overview

This Ansible role is designed to automate the installation and configuration of OpenSCAP, along with the download and use of the SCAP Security Guide for security auditing on various Linux distributions. The role dynamically handles the selection of SCAP profiles and can be customized based on the operating system and version.

## Supported Operating Systems

This role supports the following distributions and versions:

- **Ubuntu**: 16.04, 18.04, 20.04, 22.04
- **RedHat**: 7, 8, 9
- **SUSE**: 12, 15

## Role Variables

This section describes the variables available in this role and their default values.

| Variable                   | Default Value                                           | Description                                                                                                      |
|----------------------------|---------------------------------------------------------|------------------------------------------------------------------------------------------------------------------|
| `openscap_package_name`     | `openscap-scanner`                                      | The name of the OpenSCAP package.                                                                                |
| `ssg_package_name`          | `scap-security-guide`                                   | The name of the SCAP Security Guide package.                                                                     |
| `unzip_package_name`        | `unzip`                                                 | The name of the package to handle unzipping.                                                                     |
| `openscap_version_command`  | `oscap -V`                                              | Command to check the version of OpenSCAP installed.                                                              |
| `ssg_version`               | `0.1.69`                                                | Version of the SCAP Security Guide.                                                                              |
| `ssg_url`                   | `https://github.com/ComplianceAsCode/content/releases/download/{{ ssg_version }}/scap-security-guide-{{ ssg_version }}.zip` | URL to download the SCAP Security Guide zip file.                                                                |
| `ssg_content_directory`     | `/usr/share/xml/scap/ssg/content/`                      | Directory where the SCAP Security Guide content will be stored.                                                  |
| `ssg_file_check`            | `.scap_security_guide_installed`                        | Hidden file to indicate that the SCAP Security Guide has been installed.                                          |
| `ssg_guide_files`           | `{}` (see below)                                        | Dictionary mapping OS versions to the appropriate SCAP guide files.                                              |
| `ssg_default_guide_file`    | `scap-security-guide-{{ ssg_version }}/ssg-generic-ds-1.2.xml` | Default SCAP guide file to use if no specific version is found for the OS.                                        |
| `ssg_profile`               | `xccdf_org.ssgproject.content_profile_cis_level1_server` | The default SCAP profile to use for scans.                                                                       |
| `report_directory`          | `/var/log/openscap/reports`                                  | Directory where the reports will be stored node.                                                   |
| `report_filename`           | `{{ inventory_hostname }}-{{ ansible_date_time.iso8601 }}-report.html` | Name of the report file generated after the scan.                                                                |
| `report_storage_server`     | `localhost`                                             | Hostname or IP address of the server where the report will be stored.                                             |

## OS-specific SCAP Guide Files

The SCAP guide files are selected based on the OS and version using the `ansible_distribution` and `ansible_distribution_version` variables. The role dynamically chooses the appropriate guide file based on these variables.

If a specific SCAP guide file does not exist for a given OS version, a default guide file (`ssg_default_guide_file`) will be used instead. You can add support for additional OS versions or modify the existing ones by overriding the `ssg_guide_files` variable in your playbook, inventory, or `group_vars/host_vars`. This allows for easy extensibility to support new distributions or versions as needed.

Here is a matrix showing the mapping between the supported OS versions and their respective SCAP guide files:


| OS      | Version | SCAP Guide File                                         |
|---------|---------|---------------------------------------------------------|
| Ubuntu  | 16.04   | scap-security-guide-0.1.69/ssg-ubuntu1604-ds-1.2.xml    |
| Ubuntu  | 18.04   | scap-security-guide-0.1.69/ssg-ubuntu1804-ds-1.2.xml    |
| Ubuntu  | 20.04   | scap-security-guide-0.1.69/ssg-ubuntu2004-ds-1.2.xml    |
| Ubuntu  | 22.04   | scap-security-guide-0.1.69/ssg-ubuntu2204-ds-1.2.xml    |
| RedHat  | 7       | scap-security-guide-0.1.69/ssg-rhel7-ds-1.2.xml         |
| RedHat  | 8       | scap-security-guide-0.1.69/ssg-rhel8-ds-1.2.xml         |
| RedHat  | 9       | scap-security-guide-0.1.69/ssg-rhel9-ds-1.2.xml         |
| SUSE    | 12      | scap-security-guide-0.1.69/ssg-sle12-ds-1.2.xml         |
| SUSE    | 15      | scap-security-guide-0.1.69/ssg-sle15-ds-1.2.xml         |

To add support for a new OS or version, simply add a new entry in the `ssg_guide_files` dictionary.

Example for adding support for CentOS 8:

```yaml
ssg_guide_files:
  CentOS:
    "8": "scap-security-guide-{{ ssg_version }}/ssg-centos8-ds-1.2.xml"
```

## Usage

1. Install this role by cloning it into your roles directory.
2. Ensure the appropriate variables are configured in your playbooks or inventory files to match your target system's OS and version.
3. Include the role in your playbook:

```yaml
- hosts: all
  roles:
    - role_openscap
```

4. Review the generated reports in the specified `report_directory`.

## Customization

You can customize the following by overriding the default values in your `host_vars` or `group_vars`:

- **`ssg_version`**: To use a different version of the SCAP Security Guide.
- **`ssg_profile`**: To specify a different SCAP profile to scan against.
- **`report_directory`**: To store the reports in a custom location.

## License

This role is licensed under the MIT License.

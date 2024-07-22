# macadmins-extension

## Usage

For testing, you can load the extension with `osqueryi`.

By default, osquery does not want to load extensions not owned by root. You can either change the ownership of macadmins_extension.ext to root, or run osquery with the `--allow_unsafe` flag.

```bash
osqueryi --extension /path/to/macadmins_extension.ext
```

For production deployment, you should refer to the [osquery documentation](https://osquery.readthedocs.io/en/stable/deployment/extensions/).

## Tables

| Table                    | Description                                                                                   | Platforms               | Notes                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ------------------------ | --------------------------------------------------------------------------------------------- | ----------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `authdb` | macOS Authorization database | macOS | Use the constraint `name` to specify a right name to query, otherwise all rights will be returned. |
| `file_lines`             | Read an arbitrary file                                                                        | Linux / macOS / Windows | Use the constraint `path` and `last` to specify the file to read lines from                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| `filevault_users`        | Information on the users able to unlock the current boot volume when encrypted with Filevault | macOS                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| `google_chrome_profiles` | Profiles configured in Google Chrome.                                                         | Linux / macOS / Windows |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| `macos_profiles`         | High level information on installed profiles enrollment                                       | macOS                   |
| `mdm`                    | Information on the device's MDM enrollment                                                    | macOS                   | Code based on work by [Kolide](https://github.com/kolide/launcher). Due to changes in macOS 12.3, the output of `profiles show -type enrollment` can only be generated once a day. If you are running this command with another tool, you should set the `PROFILES_SHOW_ENROLLMENT_CACHE_PATH` environment variable to the path you are caching this. The cache file should be `json` with the keys `dep_capable` and `rate_limited` present, both booleans representing whether the device is capable of DEP enrollment and whether the response from `profiles show -type enrollment` is being rate limited or not. |
| `munki_info`             | Information from the last [Munki](https://github.com/munki/munki) run                         | macOS                   | Code based on work by [Kolide](https://github.com/kolide/launcher)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| `munki_installs`         | Items [Munki](https://github.com/munki/munki) is managing                                     | macOS                   | Code based on work by [Kolide](https://github.com/kolide/launcher)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| `network_quality`        | Output from the `networkQuality` binary                                                       | macOS                   | This binary is only present on macOS 12                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| `puppet_facts`           | [Puppet](https://puppetlabs.com) facts                                                        | Linux / macOS / Windows |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| `puppet_info`            | Information on the last [Puppet](https://puppetlabs.com) run                                  | Linux / macOS / Windows |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| `puppet_logs`            | Logs from the last [Puppet](https://puppetlabs.com) run                                       | Linux / macOS / Windows |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| `puppet_state`           | State of every resource [Puppet](https://puppetlabs.com) is managing                          | Linux / macOS / Windows |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| `sofa_security_release_info` | The information on the security release the device is running from [Sofa](https://sofa.macadmins.io) | macOS                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                       Use the `url` constraint to specify a data source other than `https://sofafeed.macadmins.io/v1/macos_data_feed.json` . By default this table will return vulnerability data for the running operating system. For historical data, use the `os_version` predicate (e.g `select * from sofa_security_release_info where os_version="14.4.0";`)                                                                                                                                                                                                                                                          |
| `sofa_unpatched_cves` | The CVEs that are unpatched on the device from [Sofa](https://sofa.macadmins.io) | macOS                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                       Use the `url` constraint to specify a data source other than `https://sofafeed.macadmins.io/v1/macos_data_feed.json`. By default this table will return all unpatched vulnerability data. For historical data, use the `os_version` predicate (e.g `select * from sofa_unpatched_cves where os_version="14.4.0";`)                                                                                                                                                               |
| `unified_log`            | Results from macOS' Unified Log                                                               | macOS                   | Use the constraints `predicate` and `last` to limit the number of results you pull, or this will not be very performant at all. Use `level` with a value of `info` to include info level messages. Use `level` with a value of `debug` to include info and debug level messages. (`select * from unified_log where last="1h" and level="debug" and predicate='processImagePath contains "mdmclient"';`)                                                                                                                                                                                                               |
| `wifi_network` | Table to get the current wifi network name since the Osquery `wifi_info` table no longer does this. Includes the rest of the working fields in `wifi_info`. | macOS | See [osquery issue #8220](https://github.com/osquery/osquery/issues/8220) |

## Development

- Install Go 1.21 (either directly from [go.dev](https://go.dev/dl/) or via [GVM](https://github.com/moovweb/gvm#installing))
  - `gvm install go1.21`
- Install [Bazelisk](https://github.com/bazelbuild/bazelisk/blob/master/README.md)

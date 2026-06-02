# Jamf Object Architecture

How exported object types interact on the Lotus Home Academy Jamf Pro server.

```mermaid
flowchart TB
  subgraph scope [Scope Layer]
    CG[computer_groups]
    MDG[mobile_device_groups]
  end
  subgraph config [Configuration Layer]
    CCP[computer_configuration_profiles]
    MAS[mac_app_store_apps]
  end
  subgraph automation [Automation Layer]
    SCR[scripts]
    PKG[packages]
    POL[policies]
  end
  CG --> POL
  MDG --> POL
  CCP --> POL
  SCR --> POL
  PKG --> POL
  MAS --> POL
  POL --> Macs[Managed Macs]
```

## Layer Descriptions

| Layer | Object Types | Purpose |
|-------|--------------|---------|
| Scope | computer_groups, mobile_device_groups | Define which devices receive policies and profiles |
| Configuration | computer_configuration_profiles, mac_app_store_apps | MDM payloads and App Store titles |
| Automation | policies, scripts, packages | Execute tasks on scoped devices |

## Policy Execution Flow

```mermaid
flowchart LR
  Trigger[Policy trigger] --> Scope[Scope check]
  Scope --> Scripts[Run scripts]
  Scope --> Packages[Install packages]
  Scripts --> Mac[Managed Mac]
  Packages --> Mac
```

Source: Derived from exported policy XML (scripts, packages, scope sections) and [`crosslinks.md`](relationships/crosslinks.md).

## Related

- [Policy Crosslinks](relationships/crosslinks.md)
- [Policies catalog](object-types/policies.md)

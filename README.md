# homelab-net
Network Documentation for my Homelab network

## Network Topology

```mermaid
graph TD
    subgraph Internet
        WAN[Internet]
    end

    subgraph "Core Infrastructure"
        UDM[UDM Pro Gateway/Firewall]
        USW[USW 2.5G PoE Switch]
    end

    subgraph "Wireless Access"
        U7[U7 Pro]
        U6[U6 LR]
    end

    WAN --> UDM
    UDM -- "Port 1 (Tagged V10, V20, V30)" --- Caddy
    UDM --- USW
    UDM --- U7
    USW --- U6

    subgraph VLANs
        V10[VLAN 10: Roommate 1]
        V20[VLAN 20: Roommate 2]
        V30[VLAN 30: Roommate 3]
        V40[VLAN 40: IoT / SmartHome / Edge]
        V50[VLAN 50: Guest]
    end

    UDM -.-> V10
    UDM -.-> V20
    UDM -.-> V30
    UDM -.-> V40
    UDM -.-> V50

    subgraph Services
        Caddy[Raspberry Pi: Caddy]
    end

    %% Caddy connections to roommates VLANs
    Caddy --- V10
    Caddy --- V20
    Caddy --- V30

    %% Port Forwarding Rule
    UDM -- "Port Forward (80/443)" --> Caddy
```

### Overview
This repository documents the home network architecture, hardware configurations, and VLAN segmentation.

- **VLANs 10-30:** Dedicated isolated networks for each roommate.
- **VLAN 40:** IoT, Smart Home, and Edge devices.
- **VLAN 50:** Guest network.
- **Reverse Proxy:** A Raspberry Pi running Caddy is connected to **Port 1 of the UDM Pro** with VLAN tagging for VLANs 10, 20, and 30. It handles incoming traffic via port forwarding from the UDM Pro to serve internal services to the internet.

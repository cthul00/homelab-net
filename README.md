# homelab-net
Network Documentation for my Homelab network

## Network Topology

```mermaid
graph TD
    %% Internet Connection
    WAN([Internet]) --> UDM

    subgraph Core ["Core Infrastructure"]
        UDM[UDM Pro Gateway]
        USW[USW 2.5G PoE Switch]
    end

    subgraph Wireless ["Wireless Access"]
      U7[U7 Pro]
      U6[U6 LR]
    end

    %% Physical Connections
    UDM -- "Port 1 (Tagged: 10,20,30)" --- Caddy
    UDM -- "Port 2 (PoE)" --- U7
    USW -- "Port 1 (PoE)" --- U6

    subgraph Services ["Services"]
        Caddy[Raspberry Pi: Caddy]
    end

    subgraph VLANs ["Logical Networks"]
        V10[VLAN 10: Roommate 1]
        V20[VLAN 20: Roommate 2]
        V30[VLAN 30: Roommate 3]
        V40[VLAN 40: IoT/SmartHome]
        V50[VLAN 50: Guest]
    end

    %% Logical Routing flow
    UDM -.-> VLANs
    
    %% Caddy's logical access
    Caddy -.-> V10
    Caddy -.-> V20
    Caddy -.-> V30

    %% Traffic Rules
    UDM -- "Port Forward 443" --- Caddy
```

### Overview
This repository documents the home network architecture, hardware configurations, and VLAN segmentation.

- **VLANs 10-30:** Dedicated isolated networks for each roommate.
- **VLAN 40:** IoT, Smart Home, and Edge devices.
- **VLAN 50:** Guest network.
- **Reverse Proxy:** A Raspberry Pi running Caddy w/ Cloudflare DNS is connected to **Port 1 of the UDM Pro** with VLAN tagging for VLANs 10, 20, and 30. It handles incoming traffic via port forwarding from the UDM Pro to serve internal services to the internet.

### Hardware Mapping
| Device | Physical Connection | Notes |
| :--- | :--- | :--- |
| **Raspberry Pi (Caddy)** | UDM Pro Port 1 | Tagged VLANs 10, 20, 30 |
| **U7 Pro** | UDM Pro Port 2 | PoE Powered |
| **U6 LR** | USW 2.5G PoE Port 1 | Core Switch |

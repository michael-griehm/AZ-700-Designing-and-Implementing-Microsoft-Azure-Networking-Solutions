# Azure Global Network

- One of the largest networks in the world
- 165,000 miles of lit fibre-optic and undersea cable
- 61+ regions (more added all the time)
- 100+ data centers
- 185 points-of-presence (POPs)
- Not the public Internet; this is a private network

Reference: https://azure.microsoft.com/explore/global-infrastructure/

Reference (map landing page): https://infrastructuremap.microsoft.com/

Reference (interactive map): https://datacenters.microsoft.com/globe/explore

## Azure Virtual Network (VNet)

- The cloud representation of a physical network (virtual)
- Subdivided into “subnets”
- All VNets must have at least one subnet
- Handles DHCP services
	- DHCP = Dynamic Host Configuration Protocol
	- Automatically provides IP configuration (IP address, subnet mask, default gateway, DNS)
	- In Azure, DHCP is provided by the VNet/subnet so VMs get network settings automatically (you can also specify custom DNS servers)
- Can be secured using an access control list (NSG)

## VMs on VNets

- All virtual machines must be part of a network
- Like physical hardware, VMs can have multiple NIC cards (so VMs can belong to multiple networks)
- All virtual networks have an “address block” of internal IP addresses
- All subnets have an “address block” which is a subdivision of the larger one
- Azure uses CIDR notation for IP addresses
	- CIDR = Classless Inter-Domain Routing
	- Format: IP address + prefix length (e.g., 10.0.0.0/16)
	- The /n is how many bits are the network portion (larger /n = smaller range)
	- Example: 10.0.0.0/24 contains 256 addresses (10.0.0.0–10.0.0.255)
	- In Azure, VNet and subnet address spaces are defined with CIDR blocks

## Example VNet layout (diagram)

```mermaid
flowchart TB
	Internet((Internet))
	AzureInfra[Azure Infrastructure]
	Internet --> AzureInfra

	subgraph VNet[Virtual Network]
		direction TB

		subgraph Frontend[Front end subnet]
			direction TB
			NSG_FE{{NSG}}
			PLB[Public facing load balancer]
			WEB1[(Web server VM)]
			WEB2[(Web server VM)]
			WEB3[(Web server VM)]
			DNS[(DNS)]
			NSG_FE
			PLB --> WEB1
			PLB --> WEB2
			PLB --> WEB3
			DNS
		end

		subgraph Backend[Backend subnet]
			direction TB
			NSG_BE{{NSG}}
			ILB[Internal load balancer]
			DB1[(Database server VM)]
			DB2[(Database server VM)]
			DB3[(Database server VM)]
			DC[(DC VM)]
			NSG_BE
			ILB --> DB1
			ILB --> DB2
			ILB --> DB3
			DC
		end
	end

	AzureInfra --> PLB
	WEB1 --> ILB
	WEB2 --> ILB
	WEB3 --> ILB
```


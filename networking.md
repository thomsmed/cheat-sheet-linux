# Network

## Paper/articles

- [Linux network configuration](https://wiki.debian.org/NetworkConfiguration)
- [NIC (Network Interface Controller)](https://en.wikipedia.org/wiki/Network_interface_controller)

## Commands

```shell
#  Man page for network interface configuration
man interfaces
```

## Open Systems Interconnection model (OSI model) / Internet protocol suite

### Application layer (part of Internet protocol suite)

#### DNS - Domain Name System

[dnsmasq](https://wiki.debian.org/dnsmasq)

```bash
apt update
apt install dnsmasq
```

Basic configuration
```config
# Configuration file for dnsmasq (typically located at /etc/dnsmasq.conf)

# ...

# Add domains which you want to force to an IP address here.
address=/my.domain.com/123.12.3.123 # Custom mapping for 'my.domain.com'

# ...

# By default dnsmasq only accept request coming from the same subnet as the machine it is running on. 
# And which interfaces and/or addresses dnsmasq should listen to can be configured.
# The below setting will configure dnsmasq to listen on all request coming from the eth0 interface (typically the internet facing interface)
interface=eth0

# It's also a good idea to add other name servers dnsmasq can query for addresses it self do not know about.
# dnsmasq also reads /run/dnsmasq/resolv.conf and /etc/hosts
server=8.8.8.8 # Google public DNS
server=8.8.4.4 # Google public DNS

# ...

```

Query a DNS server
```bash
nslookup my.domain.com 8.8.8.8 # IP or domain name for DNS server last
```

#### DHCP - Dynamic Host Configuration Protocol

#### HTTP(S) - Hypertext Transfer Protocol (Secure)

#### MQTT - Message Queuing Telemetry Transport

### Presentation layer

### Session layer

### Transport layer (part of Internet protocol suite)

#### Transmission Control Protocol (TCP)

#### User Datagram Protocol (UDP)

### Network layer (Internet layer - part of Internet protocol suite)

#### Internet Protocol (IPv4/IPv6)

##### netmask

##### gateway

### Data link layer (part of Internet protocol suite)

#### Ethernet Protocol

#### USB Protocol

#### Wireless LAN (WLAN - WiFi)

#### Bluetooth Protocol

### Physical layer

#### Ethernet Cable

#### Optical fiber

#### USB Cable

#### WiFi Radio

#### Bluetooth Radio

# Suricata IDS Deployment (Docker)

## Docker Compose Configuration

```yaml
services:
  suricata:
    image: jasonish/suricata:latest
    container_name: suricata
    network_mode: host
    cap_add:
      - NET_ADMIN
      - NET_RAW
    volumes:
      - ./etc-suricata:/etc/suricata
      - ./logs-suricata:/var/log/suricata
      - ./varsuricata:/var/lib/suricata
    command: >
      suricata -i enp0s10
```

## Network Mode
```network_mode: host```

Allows Suricata to capture traffic directly from host interfaces

Required for IDS packet sniffing

No Docker network isolation

## Capabilities
cap_add:
  - NET_ADMIN
  - NET_RAW


NET_RAW: required for packet capture

NET_ADMIN: required for advanced network operations

## Volumes (Persistent Data)
volumes:
  - ./etc-suricata:/etc/suricata
  - ./logs-suricata:/var/log/suricata
  - ./varsuricata:/var/lib/suricata

Host Path	Container Path	Purpose
etc-suricata	/etc/suricata	Suricata configuration
logs-suricata	/var/log/suricata	Alerts and logs
varsuricata	/var/lib/suricata	Rules and runtime files
## Interface Selection
command: suricata -i enp0s10
Suricata listens on interface enp0s10, which receives mirrored traffic from OpenWRT.

## Suricata Rules Management
Rules Location
On the host:
./varsuricata/rules/suricata.rules

## Inside the container:
/var/lib/suricata/rules/suricata.rules

## Edit Rules File
``` vi ./varsuricata/rules/suricata.rules ``` 


Example rule:

alert tcp any any -> any 22 (
 msg:"SSH connection detected";
 sid:1000001;
 rev:1;
)

### Test Suricata Configuration

Run Suricata in test mode:

```  docker exec -it suricata \
docker exec -it suricata suricata -T -c /etc/suricata/suricata.yaml
``` 

### Monitor Alerts

Follow the Suricata fast log:

```
docker exec -it suricata tail -f ./logs-suricata/fast.log
``` 

Example alert:

<img width="742" height="233" alt="image" src="https://github.com/user-attachments/assets/b6f9730a-24cc-4249-8dc2-2ea7c70e707a" />

This reloads: Configuration Rules: 
```
docker restart suricata
```




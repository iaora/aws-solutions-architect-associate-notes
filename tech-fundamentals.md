# Tech Fundamentals

### YAML 101
- Definition:
  - human-readable and data serialization language 
  - unordered collection of key:value pairs. Each key has a value

### Network Starter Pack 0 - Intro
![](osi-model.png)

- Media layers: how data is moved from point A to point B
- Host layers: how the data is chopped up and reassembled for either side of the networking section

### Layer 1 - Physical Layer (OSI Model)
- Connect a network cable from point A to point B (some type of copper, fibre, or WIFI/eletrical, light, radio frequency)
- Needs to carry unstructured information (raw bit streams) 
- HUB:
  - Anything received on any port is transmitted on every port (including errors and collisions)
  - if multiple devices transmit at once, a collision occurs 
  - No device addressing, all data is processed by all devices
  - L1 has no media access control and no collision detection 
  - L1 network doesn't scale well because of increased amount of collisions
    - To help prevent this, L2 is built on top of L1
- Summary:
  - Physical shared medium 
  - Standards for transmitting onto medium 
  - Standards for receiving from the medium 
  - No access control 
  - No uniquely identified devices 
  - No device -> device communications 

### Layer 2 - Data Link (OSI Model)
- One of the most critical layer in the OSI model 
- Frame:
  - Devices at L2 have a unique hardware (MAC) address. 48 bits
  - Preamble (56 bits)
  - Destination MAC address
  - Source MAC address
  - ET (16 bits) - ethernet protocol from L3
  - Payload (46-1500 bits) 
  - FCS (32 bits) 
- Summary:
  - identifiable devices
  - media access control (sharing)
  - collision detection
  - unicast 1:1
  - broadcast 1:all
  - switches - like hubs with super powers (layer 2)

### Layer 3 - Network (OSI Model)


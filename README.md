# MAnycastR Anycast Census

This repository contains the dataset of the Anycast Census (detected /24 Anycast Prefixes), discovered using MAnycastR.
The repository is updated daily.

## Anycast Detection Data Structure

Following we describe the structure of the provided census files.

### Detection Files
#### Path:
```
YYYY/MM/DD/YYYY-MM-DD_v4.json
YYYY/MM/DD/YYYY-MM-DD_v6.json
```
#### Structure:
```json
[{
  "prefix": "1.0.0.0/24",
  "characterization": {
    "MAnycastICMPv4": {
      "anycast": true,
      "instances": 28
    },
    "MAnycastTCPv4": {
      "anycast": true,
      "instances": 28
    },
    "MAnycastUDPv4": {
      "anycast": null,
      "instances": 0
    },
    "iGreedyICMPv4": {
      "anycast": true,
      "instances": 25
    },
    "iGreedyTCPv4": {
      "anycast": true,
      "instances": 26
    }
  },
  ....
}]
```

- **Prefix**: The candidate anycast /24 prefix being analyzed (e.g., "1.0.0.0/24").
- **Characterization**: Contains details about the anycast detection and  the number of detected instances for the various measurement protocols.
	- Type:
	  - `MAnycastICMPv4/v6`: Anycast detection using ICMP protocol.
	  - `MAnycastTCPv4/v6`: Anycast detection using TCP (SYNACK scan) protocol.
	  - `MAnycastUDPv4/v6`: Anycast detection using UDP (DNS scan) protocol.
	  - `iGreedyICMPv4/v6`: Anycast detection using ICMP protocol with GCD methodology (latency data).
	  - `iGreedyTCPv4/v6`: Anycast detection using TCP (SYNACK scan) protocol with GCD methodology (latency data).
	- State: 
	    - `anycast`: Indicates whether anycast was detected (`true` for anycast, `false` for unicast, `null` for unresponsive).
	    - `instances`: Number of anycast instances detected.

Due to NDA agreements with hitlists providers, only /24 prefixes marked at least by one measurement method as anycast are reported.

### Geolocation (GCD) Files
#### Path:
```
YYYY/MM/DD/YYYY-MM-DD_v4_location.json
YYYY/MM/DD/YYYY-MM-DD_v6_location.json
```
#### Structure:
```json
[[
  {
    "prefix": "2620:49:4::/48",
    "count": 5,
    "instances": [
      {
        "marker": {
          "city": "Sydney",
          "code_country": "AU",
          "id": "BWU",
          "latitude": -33.9244003296,
          "longitude": 150.9880065918
        }
      },
      ....
    ]
  },
  ....
}]
```

- **Prefix**: The candidate anycast /24 prefix being analyzed (e.g., "1.0.0.0/24").
- **count**: Contains details about the anycast detection and  the number of detected (duplicate).
- **instances**: Contains details about the geographical location of instances detected using GCD approach.
	-	`city`: City Level Geolocation
	-	`code_country`: Country ISO 3166-1 alpha-2
	-	`id`: Closest Airport IATA 3 Letter Code 
	-	`latitude`: Estimated Latitude
	-	`longitude`: Estimated Longitude

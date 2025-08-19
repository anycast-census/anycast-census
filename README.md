# LACeS Anycast Census

This repository contains the dataset of the Anycast Census (detected /24 Anycast Prefixes), discovered using LACeS.
The repository is updated daily.

Contact [anycastcensus@gmail.com](mailto:anycastcensus@gmail.com)

## Recommendations for using the census

TLDR:
* We recommend filtering on MAnycast && iGreedy == True when high confidence is needed.
* We recommend filtering on MAnycast || iGreedy == True (all) when completeness is needed. 

### False detection of anycast
The anycast-based approach suffers from FPs (see [MAnycast2](https://www.sysnet.ucsd.edu/sysnet/miscpapers/manycast2-imc20.pdf)).
These FPs are especially prevalent when MAnycast receives replies at two locations.

### False detection of unicast
The GCD-based approach is highly accurate.
However, it has rare cases of FNs when anycast is deployed in small geographic regions (i.e., regional anycast).

### Recommendations
If high confidence is needed, ensure that MAnycast detects more than two instances or iGreedy detects anycast.
If Completeness is needed, use all prefixes in this census (either methodology detects anycast).

## Partial anycast
To minimize the impact of our daily probing methodology we scan at a /24 granularity.
However, there are cases of partial anycast where the /24 contains both unicast and anycast addresses.
Scanning at /32 granularity reveals ~1.0k /24s are partially anycast.
We will flag these cases in the near future.

## IPv6
We use TUM's public IPv6 hitlist [IPv6Hitlist](https://ipv6hitlist.github.io/).
To maintain reasonable probing times, we scan only the first /48 of aliased prefixes.
A join with the aliased prefixes set, should give a more complete list of anycast prefixes.

Finally, we use AAAA record addresses from [OpenINTEL](https://www.openintel.nl/).


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
	-	`longtiude`: Estimated Longitude

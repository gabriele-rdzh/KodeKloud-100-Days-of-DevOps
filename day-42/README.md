# Day 42: Create a Docker Network

## Objective

The Nautilus DevOps team needs to set up several docker environments for different applications. One of the team members has been assigned a ticket where he has been asked to create some docker networks to be used later. Complete the task based on the following ticket description:


a. Create a docker network named as `official` on App Server `2` in `Stratos DC`.


b. Configure it to use `bridge` drivers.


c. Set it to use subnet `192.168.0.0/24` and iprange `192.168.0.0/24`.

## Solution

To create a network, we use the following command:
`docker network create [OPTIONS] NETWORK_NAME`

Now let's change it a bit
```bash
docker network create \
> --driver bridge \
> --subnet 192.168.0.0/24 \
> --ip-range 192.168.0.0/24 \
> official
bab390bcf548b6a1e1e5a10eaeb72fadabdeadcfe8295c45f581fb1a1e6559b3
```

We can verify that the network was created correctly
```bash
docker network ls
# Output
NETWORK ID     NAME       DRIVER    SCOPE
c690831a3b3f   bridge     bridge    local
aec949511699   host       host      local
7651646603f8   none       null      local
bab390bcf548   official   bridge    local
```

and take a closer look at it
```bash
docker network inspect official
# Output
[
    {
        "Name": "official",
        "Id": "bab390bcf548b6a1e1e5a10eaeb72fadabdeadcfe8295c45f581fb1a1e6559b3",
        "Created": "2026-09-09T06:22:45.759226987Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "192.168.0.0/24",
                    "IPRange": "192.168.0.0/24"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {},
        "Options": {},
        "Labels": {}
    }
]
```

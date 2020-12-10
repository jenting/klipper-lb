Klipper Service Load Balancer
=================

This is the runtime image for the integrated service load balancer in klipper. This
works by using a host port for each service load balancer and setting up
iptables to forward the request to the cluster IP. The regular k8s scheduler will
find a free host port. If there are no free host ports, the service load balancer
will stay in pending.

```shell
# Enable IP forward
echo 1 > /proc/sys/net/ipv4/ip_forward

# Change source IP address to the host interface IP (outgoing)
iptables -t nat -I POSTROUTING -d ${TRAEFIK_SVC_LB_IP}/32 -p TCP -j MASQUERADE
# Change target IP address to traefik LB for TCP port 80 and the source IP != traefik service load balancer IP (incoming)
iptables -t nat -I PREROUTING ! -s ${TRAEFIK_SVC_LB_IP}/32 -p TCP --dport 80 -j DNAT --to ${TRAEFIK_SVC_LB_IP}:80

# Change source IP address to the host interface IP (outgoing)
iptables -t nat -I POSTROUTING -d ${TRAEFIK_SVC_LB_IP}/32 -p TCP -j MASQUERADE
# Change target IP address to traefik LB for TCP port 443 and the source IP != traefik service load balancer IP (incoming)
iptables -t nat -I PREROUTING ! -s ${TRAEFIK_SVC_LB_IP}/32 -p TCP --dport 443 -j DNAT --to ${TRAEFIK_SVC_LB_IP}:443
```


## Building

`make`

## License
Copyright (c) 2019 [Rancher Labs, Inc.](http://rancher.com)

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

[http://www.apache.org/licenses/LICENSE-2.0](http://www.apache.org/licenses/LICENSE-2.0)

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.

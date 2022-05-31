# Server Overview

This section of the documentation aims to describe which servers we have, how they are configured and what is running
on them.

## List of Servers

| Name           | Hoster  |                         Hostname | Kubernetes Role |
|----------------|---------|---------------------------------:|-----------------|
| VcA CRM        | Robhost |        `crm.srv.vivaconagua.org` |                 |
| VcA Stage      | Hetzner |      `stage.srv.vivaconagua.org` |                 |
| VcA Pool       | Hetzner |       `pool.srv.vivaconagua.org` | Worker          |
| VcA Live       | Hetzner |       `live.srv.vivaconagua.org` | Worker          |
| VcA Backend    | Hetzner |    `backend.srv.vivaconagua.org` |                 |
| VcA Production | Hetzner | `production.srv.vivaconagua.org` | Control Plane   |
| VcA BI Server  | Hetzner |         `bi.srv.vivaconagua.org` |                 |

## IPAM

We do not have a dedicated tool for *IP Address Management (IPAM)* but this section documents which IP addresses we use where, why and which scheme they are assigned from.

#### Public Server IPs

Our Servers naturally have IP addresses of their own that are assigned from the respective hosting provider.

!> Servers that have an IPv6 prefix defined have the whole prefix routed to them by the hosting provider but only have the given address actually allocated.

| Name           |       IPv4       |           IPv6            |
|----------------|:----------------:|:-------------------------:|
| VcA CRM        | `212.83.50.133`  |         *unknown*         |
| VcA Stage      |  `176.9.147.57`  | `2a01:4f8:160:300b::2/64` |
| VcA Pool       |   `5.9.31.149`   | `2a01:4f8:161:1386::2/64` |
| VcA Live       |  `144.76.62.66`  | `2a01:4f8:191:7147::2/64` |
| VcA Backend    | `136.243.53.198` | `2a01:4f8:212:14e1::2/64` |
| VcA Production | `136.243.50.228` | `2a01:4f8:212:12e2::2/64` |
| VcA BI Server  |   `5.9.57.115`   | `2a01:4f8:161:5375::2/64` |

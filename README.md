# awesome-tailscale

Some classic way and little trick of tailscale in our daily use.  
First of all, you need to open the port `41641` open on the client machines.

## How to build a Point-to-Point private connection?

`Tailscale` offers a simple way for this. You only need to download the tailscale app(Win, Mac, Android, docker ...) on the two sides going to connect. Then execute following command and follow the instructions:
```bash   
tailscale up
```

## How to build an equal-vpc in tailnet?

> [Connect to an AWS VPC using subnet routes](https://tailscale.com/kb/1021/install-aws)  

As outlined in this tutorial, it is possible to integrate Equal-VPC with Tailscale in private cloud servers. The concept of Equal-VPC is based on the premise that the subroute in Tailnet is equal to the servers' intranet, with an instance demonstrating the configuration and functionality.
Now we have a intranet `172.16.1.0/24` in several virtual machines, and they don't have a public exit while we need to access them. We can set up a virtual machine like 1 vcpu, 1 GB REM, 10 GB storage, and set up a subrouter that advertise `172.16.1.0/24`. The machines in the same tailnet can access those virtual machines.  
Tips: Only windows platform can automatically accept a subrouter, others need to mannually add a argument `--accept-route` when execute command `tailscale up`.

## How to expose intranet-like eip to `public` in tailnet?

Let us consider an EIP, `192.168.2.0/24`, that is capable of mapping the net stream of `172.16.1.0/24`. In typical circumstances, `192.168.2.0/24` would be regarded as a public route. However, in this instance, the limited nature of the network environment has resulted in the presence of two NAT walls.  
It is possible to expose those EIPs as normal by using Tailscale. The crux of this matter is the establishment of a subrouter that is capable of accessing the aforementioned EIPs without any restrictions.  

## How to send files temporarily in tailnet?

Use the `taildrop` function.
In the Windows operating system, a file can be selected and right-clicked, which will reveal an option entitled "Send with Tailscale." The user may then select the target machine to which the file is to be sent. 

```bash
tailscale file get <directory you want to save at>
# for example: tailscale file get ./
# the file will download and save at ./
```

## How to build a multi-user tailnet without a premium plan?

Should you utilize this resource for commercial gain, it is this organization's recommendation that you subscribe to a premium plan.  
The fundamental objective is to establish a bespoke access control list (ACL) for distinct tags, with different users employing varying tags.  
To illustrate, a user with the tag "common" is permitted access to a limited portion of the subroute.  

```json5
"hosts": {
    "manage-1":  "192.168.6.0/24",
    "eip":       "192.168.2.0/24",
    "cloudpods": "192.168.88.22",
},
{
    "action": "accept",
    "src":    ["tag:common"],
    "dst": [
        "eip:*",
        "cloudpods:80,443",
        "tag:subrouter:1000-8000",
        "tag:subrouter:10000-65535",
    ],
},
"tagOwners": {
    "tag:auto-approver": [
        "autogroup:admin",
    ],
    "tag:common": ["autogroup:member"],
}
```

This restricts users with the `tag:common` designation to accessing solely the "cloudpods" web page and `eip`. In the event that access is denied, it is recommended to ascertain whether the subrouter permissions have been enabled.

## How to contribute?

Feel free to pull request your ideas and tricks of tailscale!
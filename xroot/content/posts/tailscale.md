---
title: "Tailscale - VPN done RIGHT!"
date: 2021-10-30T22:59:17+01:00
tags: [vpn,administration,security]
draft: false
---

Recently [I wrote about how I don’t feel confident using Canonical’s Landscape](/posts/landscape-review/) and how my particular use case, where Landscape **was** my only means of accessing certain servers after they were deployed, made for an interesting challenge on how to tackle this *self imposed limitation* while keeping our operational goals in place. 

When designing a solution to address connectivity to remote/edge computers obviously our good old VPN will be the first go-to tool. Unfortunately VPNs, and specifically their requirements, were not something I ever felt good about imposing to our clients. Whether it’s a S2S VPN or a user-based VPN, it will undoubtedly create extra work within different teams for our clients (at a very least, compliance and operations). 

One of the benefits of the deployments [we](/3CORESec) do of our [appliances](https://3coresec.com/nta) is how little to no changes it requires in the client's network. The devices are often deployed without any access to the network under which they are installed and I would like to keep this same principle of least-privelege in the operational aspects of managing and supporting these devices. 

**[Tailscale](https://tailscale.com) to the rescue!** 

I don't know how I went for so long without bumping into Tailscale. In a way that’s beneficial though, as there’s a multitude of blog posts, reddit threads, guides and howtos that were written over its almost 2 years of existence. The abundence of write-ups on Tailscale, ironically, did little to aid in its deployment as Tailscale is - hands down - the most intuitive security product I’ve seen. From start to finish I was able to have Tailscale up and running in less than 5 minutes.

I don’t recall the last time I ran into a security product that was able to balance user experience, functionality and security, the way the Tailscale team has done. And while there are some [issues](https://github.com/tailscale/tailscale/issues/539) I’d like to see addressed, my experience with it has been nothing short of perfect. 

We've since changed our workflow and deployment process to account for Tailscale. All the machines that are sent to remote deployments now have Tailscale installed and enrolled in our account. If for some reason our maintenance system fails - where the client is able to reverse-SSH into our support host - Tailscale is our safety net and, so far, has been able to save several trips to data-centers.

While out-of-band accesses like IPMI should - theoretically - work, I’ve had mixed experiences. Similar to what I mentioned in the Landscape post I find it 10x worst to create a dependency on something that can’t live up to the expectations than not having that tool to begin with. With Tailscale I feel safe about creating that dependency.

Another extremely useful aspect of Tailscale is how you can run your entire computing workloads (say for example in AWS) without having to worry about exposing assets to the Internet. While you could always limit access to instances via a multitude of ways - most common being IP filtering with Security Groups - it was always a PITA unless you’re accessing them from a handful of locations with static public IPs.

With Tailscale you can easily deploy your EC2's without Internet access and save some money on NAT instances for private VPC subnet routing.

Because the Tailscale team seems to magically guess everything I'd like to do, there’s yet another feature that improved our workflows. 

With the Team plan of Tailscale, and more importantly with the [Network ACLs](https://tailscale.com/kb/1018/acls/), we have been able to make use of our GSuite directory to define RBACs groups and subsequently define ACLs that give particular groups, or users, access to the servers with the option of limiting per port, individual machine (name and IP) as well as machine groups. 

Here's an example where we define a group (`group:all`) that has access to all servers and a separate group (`group:detectionlab`) limited to a single machine using solely RDP:

```
{
  "acls": [
    {
      "action": "accept",
      "users": ["group:all"],
      "ports": [
        "*:*",
      ],
    },
    {
      "action": "accept",
      "users": ["group:detectionlab"],
      "ports": [
        "x.x.x.x:3389",
      ],
    },
  ],
  "groups": {
    "group:all": [
      "user-admin@3coresec.com",
    ],
    "group:detectionlab": [
      "user1@3coresec.com",
      "user2@3coresec.com",
    ],
  },
  "disableIPv4": false,
  "randomizeClientPort": false,
}
```

If you haven’t yet tried it and you do any sort of remote access/VPN, I guarantee you you’re missing out.
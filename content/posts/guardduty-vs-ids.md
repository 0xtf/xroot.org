---
title: "AWS Guardduty vs IDS"
date: 2020-09-07T13:52:18+01:00
draft: false
---

I recently came across an AWS Blog post titled [New third-party test compares Amazon GuardDuty to network intrusion detection systems](https://aws.amazon.com/blogs/security/new-third-party-test-compares-amazon-guardduty-to-network-intrusion-detection-systems/) where the author links to a [white paper](https://d1.awsstatic.com/certifications/foregenix_amazon_guardduty_security_review_07-2020.pdf) written by, as the title suggest, a third party.

The intent of their article is to provide an unbiased analyses of the capabilities of AWS GuardDuty when compared with "other" network intrusion detection systems. I do not, however, believe this objective was reached, and I want to highlight a few points about it in this blog post.

Before going forward I believe it's important to get a few things straight, in light of full disclosure:

- I work at [3CORESec](https://3coresec.com), a company that develops MDR solutions for AWS
- We started 3CORESec exactly to address a few issues that are discussed in the blog post as well as the white paper
- Choose whatever works best for you, but always take a supplier and its independent testing with a grain of salt
- I'm a strong believer that network security monitoring is one of the most powerful aspects of a mature information security program
- I went on a bit of [rant on Twitter](https://twitter.com/0xtf/status/1145913147603259392) once about it

I would also like to mention that it is not my intent to persuade users in choosing one technology/product or the other. I don't believe the analyses is completely unbiased or fair, and as I previously mentioned, I would like to express my personal opinion on what I consider those faults to be.

With that out of the way, let's proceed with examining the remarks that were made by AWS and the white paper.

> A traditional IDS typically relies on monitoring network traffic at specific network traffic control points, like firewalls and host network interfaces. This allows the IDS to use a set of preconfigured rules to examine incoming data packet information and identify patterns that closely align with network attack types. Traditional IDS have several challenges in the cloud:
>
> Networks are virtualized. Data traffic control points are decentralized and traffic flow management is a shared responsibility with the cloud provider. This makes it difficult or impossible to monitor all network traffic for analysis.
> Cloud applications are dynamic. Features like auto-scaling and load balancing continuously change how a network environment is configured as demand fluctuates.
> -- <cite>Referenced blog post</cite>

Before I address the technical inconsistencies of this quote, let's establish a common taxonomy to make our conversation a bit simpler going forward: what we are talking about when defining a platform with these capabilities is a Network Intrusion Detection System (NIDS). While they are often referred to IDS's that run with network data, it's just simpler to call them NIDS.

NIDS, contrary to its explanation in Wikipedia and the quoted article, does not require to be placed in strategic places in the network. For years all the deployments of NIDS I've performed where done through the usage of traffic mirroring, either natively by the switching equipment or through the usage of network TAPs.

While this requirement was in fact in place during the NIDS early days of coexistence with AWS, the release of the VPC Traffic Mirroring capability finally put an end to it and brought all sorts of different possibilities. The requirements of the old days of NSM in AWS and the advantages of VPC Traffic Mirroring were discussed in [my talk in Suricon 2019](https://www.youtube.com/watch?v=Bjm5C1VEdOc).

3CORESec developed, to make use of those capabilities, and as an example, the 3CS [Mirror Toolkit](https://github.com/3CORESec/aws-mirror-toolkit), which among other things completely automates the management of these sessions with [3CS AutoMirror](https://github.com/3CORESec/AWS-AutoMirror) as well as the compliance (are mirror sessions being created as they should) aspects of it, through the usage of our [AWS Config rule](https://github.com/3CORESec/AWS-Config-MirrorSession) (3CS AWS Config - Mirror Session).

Continuing our breakdown of the blog post, the author wrote:

> Most traditional IDS require experienced technicians to maintain their effective operation and avoid the common issue of receiving an overwhelming number of false positive findings. As a compliance assessor, I have often seen IDS intentionally de-tuned to address the false positive finding reporting issue when expert, continuous support isn’t available.
> -- <cite>Referenced blog post</cite>

While I'll never compare the ease of clicking a button to turn on AWS GuardDuty to running a full blown NIDS solution (even though [our integration](https://www.youtube.com/watch?v=4Nw2ljljZ4Q) with AWS Security Hub gets you pretty close), I believe great efforts have been made in automating and easing the workload of operators to maintain a reliable deployment. Leaving NIDS de-tuned, and comparing any solution against it, as the author mentioned is, at the very least, unfair.

Like any other software, if not properly implemented the results will not be satisfactory. If anything I believe this problem is the result of the great advancements and development of tools such as Suricata and Zeek, which decrease the barrier of their implementation by being properly maintained, packaged in many distributions, frequently updated, etc.

While it can be very easy to deploy Suricata it will undoubtedly be hard to do it properly. Not only because of the effort and knowledge it takes to have a fine tuned configuration but also in regards to managing the thousands of rules that are available through several threat information providers. Which, obviously, when you compare it with the few dozens (rules) that AWS GuardDuty has, it should not come as a surprise that just turning them all on will not fix anything for you.

**With great power comes great responsibility.**

Another reason we developed, and priced, our Suricata rule management platform as we did (read: [It has landed: Lawmaker is now available](https://blog.3coresec.com/2020/08/it-has-landed-lawmaker-is-now-available.html) and other [Lawmaker-tagged posts](https://blog.3coresec.com/search/label/Lawmaker)) was to help people and organizations facing this same problem. [Lawmaker](https://lawmaker.cloud/) is not, however, the only solution to this problem, as many users have been managing rules and rulesets in NIDS way before we even though about Lawmaker.

> AWS asked Foregenix to conduct a test that would compare GuardDuty to market-leading IDS to help answer this question for us. AWS didn’t specify any specific attacks or architecture to be implemented within their test. It was left up to the independent tester to determine both the threat space covered by market-leading IDS and how to construct a test for determining the effectiveness of threat detection capabilities of GuardDuty and traditional IDS solutions which included open-source and commercial IDS.
> -- <cite>Referenced blog post</cite>

Let's focus on a quote from the next paragraph before moving on to the whitepaper:

> Foregenix configured a lab environment to support tests that used extensive and complex attack playbooks. The lab environment simulated a real-world deployment composed of a web server, a bastion host, and an internal server used for centralized event logging. The environment was left running under normal operating conditions for more than 45 days. This allowed all tested solutions to build up a baseline of normal data traffic patterns prior to the anomaly detection testing exercises that followed this activity.
> -- <cite>Referenced whitepaper</cite>

We will address those "complex attack playbooks" later in the post.

From the Executive Summary, the testing areas are defined as:

1. Ability to detect suspected intrusions and alert personnel to suspicious activity
2. Ability to support PCI DSS compliance requirements
3. Ability to deploy and activate without expert skills

It's important to reference that I won't be focusing on the PCI DSS compliance requirements. Much can be said about compliance to this framework, but I believe it's safe to assume that if AWS GuardDuty can aid in its coverage, a typical NIDS as well as a [NSM](https://www.sciencedirect.com/topics/computer-science/network-security-monitoring) solution can do it just as easily. The only challenge here would be to categorize the signatures that would relate to the framework, which I'm sure someone has done already (even if they sell it as their own "product").

The network diagram provided in the white paper makes it very clear that we're running a "Internal IDS/SIEM server", even though the traffic that is being copied to it is unclear. Mentions to "Other IDS Agent Traffic" makes me believe we're not running the native AWS way of copying traffic.

The paper also mentions that a total of 4 different IDS systems were running to compare against AWS GuardDuty.

Even though I would have liked to understand a bit more on how those systems are configured, and without any additional information, we jump directly to the findings! I know ...!

Before going into the findings, though, I believe it is important to mention that throughout this post we will be focusing on the network (N in IDS) detection capabilities.

Since AWS is so concerned in projecting the image that AWS GuardDuty is superior to NIDS, I think it's fair to stay within this detection space - the network - especially as 18 out of the 22 findings (81%) were network-based and have zero AWS GuardDuty "AI" for detection. I actually brought up the "AI of GuardDuty" in [my talk in Suricon](https://www.youtube.com/watch?v=Bjm5C1VEdOc) back in November of last year.

The findings are not not based on any pre-determined, either commercially or community-backed, taxonomies or killchains. They are AWS GuardDuty-specific [findings](https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_findings.html) and we have no information about their equivalent, if they exists, in any of the other 4 IDS systems that were running.

> Amazon GuardDuty was unique in detecting DNS-based malicious activity such as lookups for bitcoin domains. It was also successful in detecting IAM-level activity such as attempts at credential exfiltration. Interestingly, it did not report our brute force SSH attacks (run as part of the tests) but did report genuine brute force SSH attacks targeting the same instances from several internet sources.
> -- <cite>Referenced whitepaper</cite>

I'm not going to go over each individual finding, but I believe this part of the report lets us in on a few important aspects:

![](imgs/blog-pyramid-pain.jpeg)

Looking at the Appendix where the tools are listed I have to admit I'm a bit confused on why they were chosen. Nevertheless, that's what we're working with.

I thought about taking each of them and running them against a proper threat information ruleset and Suricata, but I guess it goes without saying that detection for most of them is covered by [ET Open](https://rules.emergingthreats.net/open) just by looking at which tools were chosen.

The same, however, cannot be said for a simple [testmynids.org](http://testmynids.org/) run on an AWS EC2 instance on an AWS GuardDuty-enabled account, as nothing comes up on AWS GuardDuty from those tests. Not even the Tor Project connections, which leads me to believe that AWS GD has some sort of blacklist in ingress traffic, and not egress. To further add to that point, and since so many people classify the usage of Tor as something malicious or as being part of a bigger attack or C2, I think it's safe to say that I would much rather have information that my EC2 instance is talking to the Tor network than knowing someone using Tor is brute-forcing an SSH server that only has keyfile login enabled.

What "complex attack playbooks" actually means is unclear, but if the Appendix of tools used is anything to judge these attackers, I think we'll be OK.

The white paper also includes a list of threat "intelligence" feeds, and I have to admit I'm surprised not seeing Emerging Threats Open (at least) in that list. It is not clear to me where they were utilized, though.

Can it be because they were using only IP blacklists to simulate GuardDuty's capabilities (of [only accepting IP lists](https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_upload-lists.html))? If that's true, and let's say that these were loaded in both the SIEM (for comparison on post-processing) or in the NIDS sensors (for runtime detection), both of these approaches are unfair and they should clearly indicate that the NIDS engines were not being used to their full capacity (not even a fraction of it, in my opinion).

> This is not to suggest other solutions do not have significant or similar threat detection capabilities, however achieving comparable results would require additional customisation effort.
> -- <cite>Referenced whitepaper</cite>

Well, that's a tricky statement. Wouldn't this invalidate many of the findings of this white paper? Without knowing anything about how these systems were configured and which software was running on them, it's completely impossible to understand if they were in fact capable of doing so and the tester, for whatever reason, didn't properly configured them.

If I use the example of running Suricata and testing it with [testmynids.org](http://testmynids.org/) I know for a fact that it has superior detection capabilities, but without knowing what was used (in the test) and how, and based solely on the experience of running and comparing network-based detections on AWS GD with other NIDS, I would go as far as saying that a NIDS with a proper threat information feed and the NSM platforms I know (Zeek, Suricata) have in fact a far superior detection capability when compared with AWS GD.

What those "customizations" they are referring to might be, is, again, unclear.

> From an optimal cyber security standpoint, in order to achieve the highest possible threat detection rate, Foregenix believes AWS customers should leverage the best of both worlds: deploy a Host-based IDS for protecting the instances from the “inside”, and GuardDuty to protect the surrounding “cloud” as well as the host’s external environmental behaviour. Testing demonstrated that such a configuration would provide the most effective cyber security alternative for an AWS Cloud environment running both EC2 instances and serverless infrastructure.
> -- <cite>Referenced whitepaper</cite>

I'm not a fan of Host-based IDS, and the usage of this term in the conclusion only leaves me more confused on what it is that they were testing. If we focus on the network IDS, I disagree with this quote.

If the goal is to keep it simple, obviously use AWS GD. The problem is that at this point of the report we are no longer taking ease of use into account, as the quote talks about an "optimal" state, and Host IDS+AWS GD is far from an optimal standpoint.

While I would love if AWS Lambda's ENI's allowed their usage as a source in VPC Mirroring, proper NIDS is still miles ahead of AWS GD, and what we lose by not getting insight into serverless is not comparable to what we will gain in terms of visibility for EC2 (see my Twitter rant referenced above, which is just as true in large Enterprise AWS environment as old-school physical ones). The only scenario in which NSM or NIDS would not add much value would be in "cloud native" environment where EC2's are not being used, which is not the case for the majority of enterprise deployments (regardless of how cloud-aware they might be).

**Conclusion**

As I mentioned several times throughout this post, I'm not comparing the ease of deployment of AWS GuardDuty to the deployment and maintenance it takes to run a full-blown Network Security Monitoring (including NIDS) platform. I do, however, would like to see a better testing methodology and especially one that isn't based mostly on ease of use.

Is it unrealistic to expect, and following the white paper diagrams, that a company that has an internal/self-managed SIEM (or has it been deployed with the old "SIEM will fix everything" mindset?) also has the knowledge to configure Suricata+3CS Mirror Toolkit (and/or Zeek)?

As a final note in regards to the testing methodology, and while I understand these things rarely receive any love from AWS, MITRE ATT&CK (especially the AWS matrix) would be a perfect match for this type of testing. A common taxonomy and killchain that would allow the testing to be replicated and, more importantly, have a better understanding of the results. It would make it clearer and easier to understand where AWS GD actually stands when compared to other similar solutions.

One last technical thing, and I'll be done. Promise.

> Cloud applications are dynamic. Features like auto-scaling and load balancing continuously change how a network environment is configured as demand fluctuates.
> -- <cite>Referenced whitepaper</cite>

Did you know that [3CS AutoMirror](https://github.com/3CORESec/AWS-AutoMirror) will take care of all of this for you? And guess what: You can install it with a [single click](https://blog.3coresec.com/2020/04/automirror-100.html) and it's OPEN SOURCE!

# Interviewing for GCP Roles When You Come from AWS or Azure

I started my cloud journey with Google Cloud Platform, and I'll be honest—I got lucky. GCP was my first cloud, so I never had to deal with the mental overhead of translating concepts from one platform to another. But over the years, as I've interviewed candidates and talked with engineers making the switch, I've noticed a pattern: really talented people psyching themselves out about GCP simply because it's unfamiliar territory.

If you're an AWS or Azure engineer interviewing for a role that uses GCP heavily, this post is for you. You already understand cloud infrastructure. You don't need to become a GCP expert overnight. What you need is a mental map—enough to speak confidently about how your existing knowledge translates, and an understanding of where GCP does things differently.

## The Reality Check

Here's the thing: if you understand compute, networking, and IAM in one cloud, you understand the fundamentals. GCP isn't a foreign language—it's the same concepts with different syntax. The services have different names, some of the philosophies differ, but the underlying problems you're solving are the same.

When I'm interviewing someone, I'm not looking for them to recite GCP documentation. I want to know they can think through infrastructure problems and adapt. If you can explain how you'd architect something in AWS and then say, "I know GCP has something similar but I'd need to look up the specifics," that's totally fine. What's not fine is freezing up because you don't know the GCP term for something you use every day.

## The Service Translation Guide

Let's start with the basics. Here's how the core compute and infrastructure services map:

**Compute:**
- EC2 / Azure VMs → Compute Engine
- Lambda / Azure Functions → Cloud Functions (and Cloud Run for containerized workloads)
- ECS/EKS / Azure Container Instances/AKS → Google Kubernetes Engine (GKE)
- Fargate / Azure Container Instances → Cloud Run

**Storage:**
- S3 / Azure Blob Storage → Cloud Storage
- EBS / Azure Disk Storage → Persistent Disk

**Databases:**
- RDS / Azure SQL → Cloud SQL
- DynamoDB / Cosmos DB → Firestore or Bigtable (depending on use case)

**Networking:**
- VPC / Azure Virtual Network → VPC (yes, same name, different behavior)
- Route 53 / Azure DNS → Cloud DNS
- CloudFront / Azure CDN → Cloud CDN
- Application Load Balancer / Azure Load Balancer → Cloud Load Balancing

Knowing these translations gets you 80% of the way there. You can have intelligent conversations about architecture without knowing every GCP-specific detail.

## Where GCP Is Actually Different

This is the section that matters most. There are a few areas where GCP's approach differs enough that you should understand them going in.

### Resource Hierarchy

AWS uses accounts and organizational units. Azure uses subscriptions and resource groups. GCP uses **organizations, folders, and projects**.

Projects are the key concept here. Everything in GCP lives inside a project. Think of a project as a container for resources—it's where your VMs, storage buckets, and databases live. Projects have their own billing, quotas, and IAM policies. If you're used to AWS accounts, projects are somewhat similar but more lightweight.

The hierarchy goes: Organization → Folders → Projects → Resources. IAM policies inherit down this chain, which becomes important when you're talking about access control at scale.

### Networking

GCP's networking model surprises a lot of AWS folks. Here are the big differences:

**VPCs are global.** In AWS, a VPC is regional. In GCP, a VPC spans all regions. Subnets are regional, but the VPC itself is a global resource. This changes how you think about multi-region architectures.

**Firewall rules are defined at the VPC level, not the instance level.** There are no security groups attached to individual VMs. Instead, you create firewall rules that apply to instances based on tags or service accounts. It's more centralized and, once you get used to it, often simpler to manage.

**Shared VPC** is a thing. If you're working in an enterprise environment, you might encounter Shared VPC, which lets you share networking resources across projects while keeping the projects themselves isolated. It's closer to Azure's model than AWS's.

### IAM and Service Accounts

GCP's IAM model is both similar and different enough to trip people up.

**Service accounts are first-class citizens.** In AWS, you might use IAM roles for services. In GCP, service accounts are how you grant permissions to applications and services. Every Compute Engine VM, Cloud Function, or GKE pod can run as a service account, and that service account has specific IAM permissions.

**Roles are more granular.** GCP has predefined roles (like AWS managed policies), but it also has a very granular set of primitive roles (Owner, Editor, Viewer) and the ability to create custom roles. The principle of least privilege is taken seriously here—you'll often see service accounts with very specific, narrow permissions rather than broad roles.

**IAM bindings work differently.** In AWS, you attach policies to users or roles. In GCP, you grant roles to members (users, groups, or service accounts) on a resource. It's a subtle shift, but it changes how you think about access control. You're always asking: "Who has what role on which resource?"

### Cloud Run and Serverless Containers

This is worth calling out because it's one area where GCP really shines and does things differently. Cloud Run is a serverless container platform—you give it a container, and it runs it without you managing any infrastructure. It's like Lambda, but for containers instead of just code.

If you're coming from AWS, it's closest to Fargate + API Gateway, but simpler. If you're used to deploying to Lambda, Cloud Run lets you package your app however you want as long as it's a container. It's fast, scales to zero, and bills by the millisecond.

This comes up in interviews more than you'd think, especially for companies doing modern cloud-native development.

## What I'd Want You to Know as an Interviewer

Let me flip perspectives for a moment. If I'm interviewing you for a role that uses GCP heavily, here's what I care about:

### You Understand the Fundamentals

I don't expect you to know every GCP service. I do expect you to understand compute, networking, and IAM at a conceptual level. If you can explain how you'd design a secure, scalable web application architecture in AWS, you can do it in GCP. The principles are the same.

### You Can Map Your Experience

When I ask, "How would you approach this in GCP?" I want you to be able to say things like:
- "I'd use Compute Engine for the VMs, similar to EC2"
- "For serverless, I know GCP has Cloud Functions, and I've heard Cloud Run is good for containerized workloads"
- "I'd set up a VPC with subnets in the regions we need, though I know GCP VPCs work a bit differently than AWS"

That's it. You don't need to know the exact gcloud commands or console navigation. You need to show you can translate your knowledge.

### You're Aware of Key Differences

The things I mentioned earlier—VPCs being global, service accounts, the resource hierarchy—these are things I'd want you to at least be aware of, even if you haven't worked with them hands-on. If I mention "service account" and you look confused, that's a red flag. If you say, "Right, that's like an IAM role for a service in AWS," we're good.

### You're Comfortable with Ambiguity

Cloud platforms change constantly. GCP is no exception. I'd rather hire someone who says, "I don't know that specific service, but here's how I'd figure it out" than someone who pretends to know everything. Curiosity and adaptability matter more than encyclopedic knowledge.

### You've Done a Little Homework

Look, I get it—you're interviewing at multiple places, and you can't deep-dive into every technology stack. But if you're interviewing for a GCP-heavy role, spend an hour or two poking around the console or following a quickstart tutorial. Deploy a VM. Create a storage bucket. Mess with a firewall rule. It's free (within the free tier), and it'll make you so much more confident in the conversation.

I'm not looking for expertise. I'm looking for someone who's taken the initiative to translate their existing knowledge into GCP terms.

## Final Thoughts

If you're an experienced cloud engineer, interviewing for a GCP role shouldn't be intimidating. You already know this stuff. The services have different names, some concepts are organized differently, but the problems you're solving—how do I run workloads securely, how do I scale, how do I control access—are exactly the same.

Don't let unfamiliar terminology psych you out. Focus on demonstrating your foundational understanding, show that you can map your AWS or Azure experience to GCP equivalents, and be honest about what you don't know yet. That's what good interviewers are looking for.

And hey, once you start working with GCP, you might end up loving it as much as I do. There's a reason I keep seeking out roles that use it.

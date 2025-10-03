I've been working in AWS recently, and I keep catching myself missing Google Cloud Platform. Not the console UI, not the service names—specifically, IAM.

After a decade working as a DevOps engineer, cloud architect, and software engineer across various projects—most of that time spent in GCP—I've developed opinions about how cloud permissions should work. And honestly? GCP's approach just clicks for me in a way AWS's doesn't.

This isn't a hot take about one being objectively better. It's about architectural philosophy and how different models fit different mental frameworks. But if you've ever stared at an AWS IAM policy wondering why something so simple requires so much JSON, you might relate.

## The Mental Model Problem

Here's the thing: IAM in both clouds does roughly the same job. You're defining who can do what to which resources. But the way they architect that problem is fundamentally different.

GCP uses a role-based model with hierarchical inheritance. AWS uses a policy-based model with attachments at multiple levels. Both work. One just makes more intuitive sense to me.

### How GCP's Hierarchy Saves My Sanity

In GCP, permissions flow down through a clear structure:

```
Organization
  └── Folders
      └── Projects
          └── Resources
```

When I grant someone a role at the folder level, it applies to everything beneath it. I can reason about permissions top-down. If I need to give my entire engineering team read access to logs across twenty microservice projects, I do it once at the folder level.

In AWS, I'm managing policies attached to users, groups, roles, and resources. There's an implicit hierarchy through organizational units, but the permission model doesn't naturally follow that structure the same way. I find myself duplicating policies or creating complex policy combinations to achieve what feels simple in GCP.

### Predefined Roles as Training Wheels

Let me be honest: I don't always know the perfect set of granular permissions needed for every task. And I shouldn't have to.

GCP's predefined roles are genuinely useful. Roles like `roles/secretmanager.secretAccessor` or `roles/storage.objectViewer` are scoped sensibly. They're not too broad, not too narrow, and the names tell me exactly what they do. They serve as a starting point that works for 80% of use cases.

AWS has managed policies too, but I find them either too permissive or too vague. I end up in the documentation more often, piecing together custom policies because the managed ones don't quite fit.

## A Concrete Example: EC2 Reading from Secrets Manager

Let me show you where this hits home. I recently needed to set up permissions for an EC2 instance to read from AWS Secrets Manager. Common task, right?

### The AWS Way

Here's what I had to think through:

1. Create an IAM role for EC2
2. Write or attach a policy that grants Secrets Manager access
3. Attach the role to an instance profile
4. Associate that instance profile with the EC2 instance

The custom policy JSON looked something like this:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "secretsmanager:GetSecretValue",
        "secretsmanager:DescribeSecret"
      ],
      "Resource": "arn:aws:secretsmanager:us-east-1:123456789012:secret:my-secret-*"
    }
  ]
}
```

Not terrible, but I had to:
- Know the exact ARN format for Secrets Manager
- Remember which actions were needed (GetSecretValue vs GetSecret vs DescribeSecret)
- Understand the distinction between a role and an instance profile
- Navigate the trust policy for the role separately

### The GCP Way

In GCP, the equivalent task with Secret Manager:

1. Grant the Compute Engine default service account the `roles/secretmanager.secretAccessor` role at the project level (or scoped to specific secrets)
2. Done.

The CLI command is literally:

```bash
gcloud projects add-iam-policy-binding PROJECT_ID \
  --member="serviceAccount:PROJECT_NUMBER-compute@developer.gserviceaccount.com" \
  --role="roles/secretmanager.secretAccessor"
```

No JSON. No custom policy syntax. The predefined role has exactly the permissions needed. The service account is automatically attached to the VM. The mental model is: "This service account needs this role to access this resource."

## What AWS Does Better

Let me be fair here: AWS's approach has advantages.

The granularity is powerful. If you need extremely specific permissions that don't fit predefined patterns, AWS gives you the primitives to build exactly what you want. The policy language is more expressive for complex conditions.

AWS's model also makes cross-account access more explicit, which can be valuable in large enterprise environments where trust boundaries matter a lot.

And if you're deeply invested in the AWS ecosystem with hundreds of custom policies already written, the flexibility pays off. You're not constrained by someone else's opinion of what a role should include.

## The Grass is Always Greener

Here's the truth: I miss GCP's IAM because its architectural choices align with how I think about permissions. The role-based model, the hierarchical inheritance, the sensible predefined roles—they all feel like guard rails that keep me moving fast without second-guessing myself.

But I also know that if I'd spent ten years primarily in AWS, I might be writing the opposite post about how GCP's opinionated approach feels limiting.

Cloud IAM is one of those things where the "best" model is really about which mental framework clicks for you. For me, that's GCP. Your mileage may vary.

And hey, at least neither of us is managing permissions in a bunch of XML files anymore. We've got that going for us.

---

*What's your take? Are you team role-based inheritance or team policy-attachment flexibility? Let me know in the comments.*

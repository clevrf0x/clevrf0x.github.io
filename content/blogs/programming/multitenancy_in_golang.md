+++
title = 'Multitenancy Using Golang (WIP)'
date = 2024-09-21T16:25:54+05:30
description = ""

# Author
author = "Favas M"
authorTwitter = "clevrf0x"

# Categories and Tags
categories = ["Programming", "Golang", "SaaS", "Multi-tenancy"]
tags = ["golang", "saas", "multi-tenancy", "coding", "tutorial"]

aliases = ["/multi-tenancy-using-golang"]

# Localization
language = "en"

# SEO
keywords = ["multi-tenancy", "Golang", "Go multi-tenant", "multi-tenant architecture", "schema isolation Golang", "Golang tutorial", "multi-tenancy tutorial", "Golang multi-tenant system", "schema-level isolation", "Chi routing", "PostgreSQL multi-tenant"]
seoTitle = "Multi-Tenancy in Golang: Schema-Level Isolation with Chi & PostgreSQL"
+++

Hello! In this article, I will explain how to build a multi-tenant system in Golang. Before we dive in, I'd like to mention that this is my first blog post, so there may be some mistakes or areas lacking clarity. The content is based on my experience building a multi-tenant system in Golang at work. If there's a better way to approach this or potential drawbacks in the method described, I'd appreciate your feedback.

Keep in mind that I haven't tested this system in a high-traffic production environment, so it may have performance limitations. Please adapt the approach to your specific needs and use cases.

### TL;DR:

This post shares my experience building a multi-tenant system in Golang. The example code is meant to illustrate the concept and might have unhandled exceptions or security vulnerabilities. Make sure to thoroughly review and test the code before deploying it in a production environment.

### What is Multi-Tenancy

Before we explore how to build a multi-tenant system, let's clarify what multi-tenancy is. According to Wikipedia, "Multitenancy is a software architecture where a single instance of an application serves multiple users or organizations", users are often referred to as 'tenants.'

But what does that mean? Imagine you have a product with two users who both need identical environments but require their data to be completely isolated. In the past, the solution was to host two separate instances of the application, either on-premises or in the cloud. However, this approach has several downsides:

- It becomes expensive to spin up new servers for every user.
- Managing separate instances becomes difficult as the user base grows. For example, managing 100 instances for 100 customers can be a huge headache.
- Different instances might run different versions of the app, leading to inconsistency.
- Handling database migrations across multiple environments can become a major challenge.

![Single Tenant Application Architecture](/blogs/programming/multi-tenancy-in-golang/single-tenant-architecture.png)

<!-- _Single Tenant Application Architecture_ -->

What's the alternative? Instead of spinning up separate instances, we could host a single instance of the application where every tenant can interact with it, while still keeping their data isolated. This is where multi-tenancy comes in.

Using a multi-tenant architecture allows us to:

- Roll out version updates and database migrations across all tenants simultaneously.
- Reduce operational overhead by managing a single instance.
- Lower infrastructure costs.

### Real-world Examples of Multi-Tenant Architecture

Many popular SaaS products use multi-tenancy successfully. Here are a few examples:

- Microsoft Office 365
- GitHub
- Twilio
- Canva
- Atlassian Products (e.g., Jira, Confluence)

![Multi-Tenant Application Architecture](/blogs/programming/multi-tenancy-in-golang/multi-tenant-architecture.png)

<!-- _Multi-Tenant Application Architecture_ -->

### How to Achieve Data Isolation in Multi-Tenancy

Now that we understand what multi-tenancy is, let's explore how to implement it in a way that ensures data isolation between tenants. There are primarily three methods for achieving this, each with its own pros and cons:

#### 1. Database-Level Isolation

In database-level isolation, each tenant's data is stored in a separate database. A shared public database is used for common data.

##### Pros:

- Each tenant's data is completely isolated in a separate database.
- Low risk of accidentally accessing cross-tenant data.

##### Cons:

- Tenants cannot have foreign key relationships between their data and the shared public database.
- Switching between tenant databases and the public database adds performance overhead. This can be mitigated by injecting separate database sessions into your handlers.
- It's complex to implement and maintain.

#### 2. Schema-Level Isolation

In schema-level isolation, all tenants share the same database, but their data is separated into individual schemas. A shared public schema is used for common data.

##### Pros:

- Data is isolated at the schema level, similar to database-level isolation.
- No significant performance overhead when switching between public and tenant schemas.
- Reduced complexity compared to database-level isolation.
- Reusing the same database connection for both tenant and public data is easier to implement.

##### Cons:

- Less strict data isolation compared to database-level isolation.

#### 3. Row-Level Isolation

In row-level isolation, each tenant's data is stored in the same tables, but a "tenant ID" column is added to differentiate records by tenant.

##### Pros:

- Simple to implement and maintain.
- Easy to apply database migrations.
- Foreign key relationships between tables can be maintained as needed.

##### Cons:

- Data is less isolated compared to database or schema-level isolation.
- There's a higher risk of accidental cross-tenant data access through query errors.

#### Other Methods

There are additional methods, such as creating prefixed tables for each tenant (e.g., `tenant1_users`, `tenant2_users`). However, this approach is less common because it complicates database migrations and increases maintenance efforts.

### How to Implement Multi-Tenancy

For this blog post, we will be implementing schema-level isolation. After weighing the pros and cons of each approach, I believe schema-level isolation provides a balanced trade-off between complexity and data isolation, making it a good choice for this demonstration.

To keep this project as a proof of concept, we'll use a minimal set of dependencies. Here's the stack we'll be working with:

- **Routing:** We'll use the Chi library for routing. It's lightweight, easy to use, and simplifies middleware implementation, making the codebase more manageable.
- **Database Interaction:** For database queries, we'll use `sqlc` to generate Go code, ensuring type safety with minimal boilerplate.
- **Migrations:** Goose will handle our database migrations, allowing for smooth updates to the database schema as new tenants are added.
- **Database:** We'll use PostgreSQL, which natively supports schema-level isolation, making it the ideal choice for this project.

With these tools, we'll go step-by-step through setting up multi-tenancy using schema-level isolation.

### Project Structure

Now this project structure will seem a bit redundant or strange for some of the Go developers. This is how I generally build projects in Golang. Since this is not a blog about how to build API servers in Golang, I will not dwell deep into the structure; I will explain as much as I can of each package and file's purposes as we progress.

```bash
.
├── api
│   ├── middlewares
│   ├── middlewares.go
│   └── router.go
├── cmd
│   ├── app
│   │   └── main.go
│   └── migrate
│       ├── helpers.go
│       └── main.go
├── db
│   ├── migrations
│   │   ├── shared
│   │   │   └── 00001_init_db.sql
│   │   └── tenant
│   │       └── 00001_init_db.sql
│   └── queries
│       ├── shared
│       │   └── tenant.sql
│       └── tenant
│           └── user.sql
├── docker-compose.yml
├── .env
├── .env.example
├── .gitignore
├── go.mod
├── Makefile
├── pkg
│   └── helpers
├── server
│   └── server.go
└── sqlc.yml
```

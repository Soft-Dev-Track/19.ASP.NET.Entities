# 19.ASP.NET : Entities

In a previous course on the **Merise** method, you learned how to design your database schema. Now, it's time to implement it in your project.

Thanks to the packages previously installed, you can now use `Entity Framework` and create your first entities.

## 1. Create a folder named "Entities"

This folder will contain all your entities. Let's look at some simple use cases.

### User Entity

|Field|Type|Nullable|
|----|----|--------|
|Id  |nvachar(10)|NN
|FirstName  |nvachar(10)|NN
|LastName  |nvachar(10)|Nullable
|Email  |nvachar(20)|NN

---

### Address Entity

|Field|Type|Nullable|
|----|----|--------|
|Id  |nvachar(10)|NN
|StreetNumber|nvachar(10)|NN
|ZipCode|int|NN
|Town|nvachar(10)|NN
|Country|nvachar(10)|NN

Now, please create a **User.cs** & **Address.cs** classes. Inside those files, you will implement your solution. As a reminder, this course does not provide the answers—it is up to you to design the project step by step. This tutorial serves only as a guide.

However, to help with your learning, here is a starting point:

```csharp
namespace YourFirstAPI.Entities
{
    public class User
    {
        public int Id { get; set; }
        public string FirstName { get; set; } = string.Empty;
        // Your code here
    }
}
```

## 2. Create the Database Context
Once your entities are created, the next step is to define how they interact with the database. In **Entity Framework**, this is achieved by creating a database context.

A `database context` is essentially the bridge between your C# classes (entities) and the database. It is responsible for querying and saving data, ensuring that the operations you perform in C# are correctly translated into SQL queries.

In Entity Framework Core, this is done by extending the DbContext class, which provides the necessary functionality to interact with the database.

### What is DbContext?
DbContext is a fundamental part of Entity Framework that serves as:

- A session with the database: It keeps track of changes made to objects before they are persisted to the database.
- A gateway for querying data: It provides a way to write LINQ queries that retrieve data from the database.
- A way to apply configurations: It allows you to define how entities should be mapped to tables using attributes or Fluent API.

Each entity that you define (such as **User** and **Address**) will be registered inside the database context so that Entity Framework knows how to manage them.

### Creating the Database Context
To get started, create a `Data` folder in your project. Inside this folder, add a new file named **ContextDatabase.cs**.

```csharp
using Microsoft.EntityFrameworkCore;

namespace YourFirstAPI.Data
{
    public class ContextDatabase : DbContext
    {
        public ContextDatabase(DbContextOptions<ContextDatabase> options) : base(options) { }
    }
}
```

## 3. Adding Data Types and Relationships Between Tables
When designing your **Merise models**, you defined both data types for each attribute and the relationships between different entities. Now, we need to map these properties into `Entity Framework` so that the database structure reflects our design.

In `Entity Framework` Core, there are two ways to define data constraints, relationships, and integrity rules:

- Using Attributes (Data Annotations) → Directly inside entity classes.
- Using Fluent API → Inside the OnModelCreating method in the DbContext.

Both approaches serve the same purpose, but **Fluent API** offers more flexibility and is often preferred for complex configurations.

Let’s explore both methods.

### 1. FluentAPI
**Fluent API** is a powerful way to configure `entity properties` and `relationships`. It allows precise control over how entities are mapped to the database. This approach is implemented inside the OnModelCreating method of your **DbContext**.

#### Step 1: Define DbSet Properties
The first step is to declare **DbSet properties** inside the database context. This tells `Entity Framework` that these classes should be mapped to database tables.

```csharp
public virtual DbSet<User> Users { get; set; }
public virtual DbSet<Address> Addresses { get; set; }
```

#### Step 2: Adding Data Type to Entities
Now that our entities are created, the next step is to define the data types for each field. In Entity Framework, we need to specify constraints such as maximum length, required fields, and data types to ensure data integrity.

```csharp
using Microsoft.EntityFrameworkCore;
using Revision.Entities;

namespace Revision.Data
{
    public class ContextUser : DbContext
    {
        public ContextUser(DbContextOptions<ContextUser> options): base(options) { }

        public virtual DbSet<User> Users { get; set; }

        public virtual DbSet<Address> Addresses { get; set; }

        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.Entity<User>(entity =>
            {
                entity.HasKey(u => u.Id);
                // Your code here
            });

            // Address
            modelBuilder.Entity<Address>(entity => 
            {
                // Your code here ...
            });
        }
    }
}
```

### 2. Using Data Annotations (Attributes)
An alternative to `Fluent API` is using Data **Annotations**, which allows defining constraints directly inside entity classes. While easier for simple models, it has limitations for complex configurations.

Here’s how you can define the same constraints using Attributes inside the entity class:

```csharp
using System.ComponentModel.DataAnnotations;

namespace Revision.Entities
{
    public class User
    {
        [Key] // Defines Primary Key
        public int Id { get; set; }

        [MaxLength(10)]
        [Required]
        public string FirstName { get; set; } = string.Empty;

        // Your code here
    }  
}
```

## 4.Which Approach Should You Use?
Both Fluent API and Data Annotations serve the same purpose, but they have different advantages:

|Feature|FluentAPI|Data Annotations|
|:------|:--------:|:---------------:|
|Best for complex relationships	|✅	| ❌
|More readable for small models	|❌	|✅
|Easier to modify & scale | ✅	|❌
|Supports all Entity Framework features	| ✅	|❌
|Requires modifying DbContext |✅| ❌

- If your project is small and simple, **Data Annotations** might be enough.
- If your project grows and requires advanced configurations, **Fluent API** is more flexible.

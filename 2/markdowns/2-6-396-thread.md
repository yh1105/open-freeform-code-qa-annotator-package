
# Post \#58659431 [Link](https://stackoverflow.com/questions/58659431/)

## 'propertyName' cannot be used as a property on entity type 'typeName' because it is configured as a navigation

**Vote**: 30 (193/702) **Views**: 19159 (311/702) 

**Internal ID** \#2-6-396

Created at 2019-11-01 12:42:02

Tags: `c#` `asp.net` `entity-framework` `asp.net-core` `entity-framework-core`

----------

#### Metadata:

Area: `Back-end`

Language: `c#` (full parsed tag list: `c#`)

----------

**Notepad**


----------

I have an entity `user` with the following:

```
public class User
{
    [Key, Required]
    public int Id { get; set; }
    public int GenderId { get; set; }
    public virtual Gender Gender { get; set; }
}
```


In `gender`:

```
public class Gender
{
    [Key, Required]
    public int Id { get; set; }
    public virtual ICollection<User> Users { get; set; }
}
```


Then, inside my `DbContext`, I have:

```
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<User>(user =>
    {
        user
        .HasOne(x => x.Gender)
        .WithMany(x => x.Users)
        .HasForeignKey(x => x.GenderId);
    }

    user.HasIndex(x => x.Gender);
}
```


When I run `dotnet ef add migration User`, I am getting the error:

> 'Gender' cannot be used as a property on entity type 'User' because it
  is configured as a navigation.


----------
        
## Answer \#0

**Accepted** Vote: 44

Created at 2019-12-18 12:56:50

------------

I was trying to create an index on a navigation property. Instead, create the index on the foreign key.

Change `user.HasIndex(x => x.Gender)` to `user.HasIndex(x => x.GenderId)`.


------------
    
    
## Answer \#1

 Vote: 1

Created at 2019-11-01 12:47:54

------------

Use `[ForeignKey("GenderId")]` on your `public virtual Gender Gender { get; set; }` property . Thus GenderId would be identified as a foreign key for Associated User.

See below updated code:

```
public class User
{
    public int GenderId { get; set; }
    [ForeignKey("Id")]//Gender Primary key
    public virtual Gender Gender { get; set; }
}
```


Hope it will fix your issue.

Thanks,


------------
    
    
## Answer \#2

 Vote: 1

Created at 2019-11-20 16:38:34

------------

I had a similar error:

> 'Product' cannot be used as a property on entity type 'OrderLine'
  because it is configured as a navigation.

The cause of the error was that in the fluent api I also used the entity as a property:

```
modelBuilder.Entity<OrderLine>().Property(ol => ol.Product).IsRequired(true)
```



------------
    
    
## Answer \#3

 Vote: 1

Created at 2020-09-01 15:09:37

------------

I had the same problem for a complex hierarchy model with nested complex classes in it.
Apparently, the `IsRequired()` method conflicts with `OnDelete(...)`. I removed the `IsRequired()` and everything got back to normal.
```
public class MyComplexClassConfig : IEntityTypeConfiguration<MyComplexClass>
{
    public void Configure(EntityTypeBuilder<MyComplexClass> builder)
    {
        builder
            .HasOne(col => col.ChildClass)
            .WithOne(col => col.ParentClass)
            .OnDelete(DeleteBehavior.Cascade);
            
        // The following line of code needs to be deleted.
        builder.Property(col => col.Customer).IsRequired();
    }
}
```



------------
    
    
## Answer \#4

 Vote: 1

Created at 2022-04-04 19:32:16

------------


I had same problem when I was stablishing a relation between  Entity and  Entity as below:
```
public class Product
{
    public long Id { get; set; }
    public string Images { get; set; }
    public string Brand { get; set; }
    public Warranti Warranti { get; set; }
}


public class Warranti
{
    public Product Product { get; set; }
    public long ProductId { get; set; }
    public int WarrantyPeriod { get; set; }
}
```

and on my DatabaseContext Class I had this config:
```
protected override void OnModelCreating(ModelBuilder modelBuilder)  
   {
        modelBuilder.Entity<Product>()
            .HasOne(p => p.Warranti)
            .WithOne(p => p.Product)
            .HasForeignKey<Warranti>(p => p.Product);
    }
```

And I got Same error. But, What was The problem?
According to [Microsoft Official EFCore Doc](https://learn.microsoft.com/en-us/ef/core/modeling/relationships?tabs=fluent-api%2Cfluent-api-simple-key%2Csimple-key) The ForeignKey Should be something like this:
```
.HasForeignKey(p => p.BlogForeignKey); // single prop such as string or int,...
```

Or
```
.HasForeignKey("BlogForeignKey"); // Hardcoded favorite string
```

But I was Using `a class(Product)` instead of a property In `HasForeignKey` method. likewise, you were using `a class(Gender)` instead of a property In `HasIndex` method. Changing these classes to an Id property solves the problem.
: Try to use an Id property on  and ;


------------
    
    
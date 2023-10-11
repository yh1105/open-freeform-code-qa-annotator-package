
# Post \#64670235 [Link](https://stackoverflow.com/questions/64670235/)

## EF-Core: Table "name" already exists - when trying to update database

**Vote**: 10 (387/702) **Views**: 20293 (304/702) 

**Internal ID** \#2-6-422

Created at 2020-11-03 20:34:00

Tags: `c#` `entity-framework` `asp.net-core` `asp.net-web-api` `entity-framework-core`

----------

#### Metadata:

Area: `Back-end`

Language: `c#` (full parsed tag list: `c#`)

----------

**Notepad**


----------

ASP Core 3.1 - API. I'm using the latest version of Entity Framework Core.
I have created a table `ToDoItem` and a `ToDoItemContext`. After creating the initial migration, and running `update-database`. I now have that table in my database. I now added a new model called: `ToDoItemDescription`.
When I try to update the database after creating a new migration, I get the error:
> Table 'todoitems' already exists
Further details: I have two contexts, and this is the command I ran:
```
update-database -context todoitemscontext
```

I also tried:
```
update-database -context todoitemscontext -migration AddDescription
```

Here is my full code:
Models:
```
public class TodoItem : IEntity 
{
    public long Id { get; set; }
    public string Name { get; set; }
    bool IsComplete { get; set; }
}

public class ToDoItemDescription 
{
    public int id { get; set; }
    public string Description { get; set; }
    //public int ToDoItemId { get; set; }
    public TodoItem TodoItem { get; set; }
}
```

Context:
```
public class TodoItemsContext : DbContext 
{
   public TodoItemsContext(DbContextOptions<TodoItemsContext> options) : base(options) { }

   public DbSet<TodoItem> TodoItems { get; set; }
   public DbSet<ToDoItemDescription> TodoItemsDescription { get; set; }
}
```

Migrations:
```
[DbContext(typeof(TodoItemsContext))]
partial class TodoItemsContextModelSnapshot : ModelSnapshot 
{
    protected override void BuildModel(ModelBuilder modelBuilder) {
    #pragma warning disable 612, 618
    modelBuilder
        .HasAnnotation("ProductVersion", "3.1.9")
        .HasAnnotation("Relational:MaxIdentifierLength", 64);
    modelBuilder.Entity("project.Models.ToDoItemDescription", b => {
        b.Property<int>("id")
        .ValueGeneratedOnAdd()
        .HasColumnType("int");
        b.Property<string>("Description")
        .HasColumnType("longtext CHARACTER SET utf8mb4");
        b.Property<long?>("TodoItemId")
        .HasColumnType("bigint");
        b.HasKey("id");
        b.HasIndex("TodoItemId");
        b.ToTable("TodoItemsDescription");
    });

    modelBuilder.Entity("project.Models.TodoItem", b => {
        b.Property<long>("Id")
        .ValueGeneratedOnAdd()
        .HasColumnType("bigint");
        b.Property<bool>("IsComplete")
        .HasColumnType("tinyint(1)");
        b.Property<string>("Name")
        .HasColumnType("longtext CHARACTER SET utf8mb4");
        b.HasKey("Id");
        b.ToTable("TodoItems");
    });
    modelBuilder.Entity("project.Models.ToDoItemDescription", b =>
    {
    b.HasOne("project.Models.TodoItem", "TodoItem")
        .WithMany()
        .HasForeignKey("TodoItemId");
    });
#pragma warning restore 612, 618
}

public partial class TodoItems_Initial : Migration
{
        protected override void Up(MigrationBuilder migrationBuilder)
        {
            migrationBuilder.CreateTable(
                name: "TodoItems",
                columns: table => new
                {
                    Id = table.Column<long>(nullable: false)
                        .Annotation("MySql:ValueGenerationStrategy", MySqlValueGenerationStrategy.IdentityColumn),
                    Name = table.Column<string>(nullable: true),
                    IsComplete = table.Column<bool>(nullable: false)
                },
                constraints: table =>
                {
                    table.PrimaryKey("PK_TodoItems", x => x.Id);
                });
        }

        protected override void Down(MigrationBuilder migrationBuilder)
        {
            migrationBuilder.DropTable(
                name: "TodoItems");
        }
    }

public partial class AddDescription : Migration
{
        protected override void Up(MigrationBuilder migrationBuilder)
        {
            migrationBuilder.CreateTable(
                name: "TodoItemsDescription",
                columns: table => new
                {
                    id = table.Column<int>(nullable: false)
                        .Annotation("MySql:ValueGenerationStrategy", MySqlValueGenerationStrategy.IdentityColumn),
                    Description = table.Column<string>(nullable: true),
                    TodoItemId = table.Column<long>(nullable: true)
                },
                constraints: table =>
                {
                    table.PrimaryKey("PK_TodoItemsDescription", x => x.id);
                    table.ForeignKey(
                        name: "FK_TodoItemsDescription_TodoItems_TodoItemId",
                        column: x => x.TodoItemId,
                        principalTable: "TodoItems",
                        principalColumn: "Id",
                        onDelete: ReferentialAction.Restrict);
                });

            migrationBuilder.CreateIndex(
                name: "IX_TodoItemsDescription_TodoItemId",
                table: "TodoItemsDescription",
                column: "TodoItemId");
        }

        protected override void Down(MigrationBuilder migrationBuilder)
        {
            migrationBuilder.DropTable(
                name: "TodoItemsDescription");
        }
}
```

Thank you.


----------
        
## Answer \#0

**Accepted** Vote: 4

Created at 2021-06-16 00:49:47

------------



### TL;DR;


I had to rename the table
- `__efmigrationshistory`- `__EFMigrationsHistory`
so the command-line `dotnet-ef database update` managed to verify all the migrations present on the table `__EFMigrationsHistory`, and therefore, creating the new field on the table, say 

- - - - `dotnet-ef migrations add new-ftpSettings-field``dotnet-ef database update`- - - `dotnet-ef database update`
*** Like you might have figured this out, running  the command-line `dotnet-ef database update` on Linux was creating a new (and) empty table  to MySQL database while it had already, a  lower cased table on   (the good one, created on my Windows box, with all the migrations).
*** This is my first contribution. Any advice is welcome!
Keep safe! Tchau/Au revoir!


------------
    
    
## Answer \#1

 Vote: 19

Created at 2021-02-23 07:29:17

------------

This happens if you have created the database upfront without migrations, for example by using `DbContext.Database.EnsureCreated();`.


------------
    
    
## Answer \#2

 Vote: 8

Created at 2020-11-08 13:29:32

------------

This usually happens when you have a migration that creates a table and the required table is already present in your database so, when you update the database from classes in Migration, it will try to create a table and will fail because the Create command will not be executed as it already has that specific table.
So, in order to avoid the error, you might want to remove the migration class or comment the code in `Up()` method of that class so it doesn't execute that specific create command.


------------
    
    
## Answer \#3

 Vote: 0

Created at 2023-02-04 21:49:42

------------

I was working through the [migration tutorial](https://learn.microsoft.com/en-us/ef/core/managing-schemas/migrations/?tabs=dotnet-core-cli) and had made a mistake sometimes around these steps
```
dotnet ef migrations add AddBlogCreatedTimestamp
    dotnet ef database update
```

I did the following

1. deleted the files AddBlogCreatedTimestamp.Designer.cs and AddBlogCreatedTimestamp.cs
2. inside blogging.db in the table __EFMigrationsHistory i deleted the row that contains 2023__***__AddBlogCreatedTimestamp this was the migration step that failed.
3. I repeated the migration step dotnet ef migrations add ...
4. then manually added DropTable(...) to AddBlogCreatedTimestamp.Up()
5. only then i ran dotnet ef database update


This made sure that in an up-migration the tables would be deleted

### Code manually changed


```
public partial class AddBlogCreatedTimestamp : Migration
{
    protected override void Up(MigrationBuilder migrationBuilder)
    {
       // manually added
       migrationBuilder.DropTable(name: "Posts");
       migrationBuilder.DropTable(name: "Blogs");
       // ... other lines that were created
    }
    
    // more other code ...
}
```

What i still not get is why this is needed. I am not aware to have used anything like `EnsureCreated`


------------
    
    
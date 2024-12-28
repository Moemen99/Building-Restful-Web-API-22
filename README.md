## Question.cs Entity

This file will define the `Question` entity, used to represent a question within the application.

```csharp
public sealed class Question : AuditableEntity
{
    // Primary key for the Question entity.
    public int Id { get; set; }

    // The actual content of the question.
    public string Content { get; set; } = string.Empty;

    // Foreign key referencing the Poll to which this question belongs.
    public int PollId { get; set; }

    // Flag indicating whether the question is active (for soft delete approach). Defaults to true for new questions.
    public bool IsActive { get; set; } = true;

    // Navigational property to access the associated Poll.
    public Poll Poll { get; set; } = default!;

    // Navigation property representing the collection of Answers associated with this question.
    // Initialized with an empty list.
    public ICollection<Answer> Answers { get; set; } = [];
}


```

# Question Entity Class

The `Question` class represents the entity for questions in the application. It includes properties for the question's content, its relationship with a poll, and its set of answers. Below is the breakdown of the class and its properties:

```csharp
public sealed class Question : AuditableEntity
{
    // Primary key for the Question entity
    public int Id { get; set; }

    // Content of the question. Initialized as an empty string to avoid null references.
    public string Content { get; set; } = string.Empty;

    // Foreign key to associate the question with a specific poll.
    public int PollId { get; set; }

    // Boolean property to implement soft delete. 
    // By default, it is set to `true` to indicate the question is active.
    public bool IsActive { get; set; } = true;

    // Navigational property to represent the relationship with the `Poll` entity.
    // This helps Entity Framework understand the foreign key relationship.
    public Poll Poll { get; set; } = default!;

    // Collection of answers associated with the question.
    // Initialized as an empty array to avoid null references.
    public ICollection<Answer> Answers { get; set; } = [];
}
```

### Notes on Properties:
1. **`Id`**:  
   - The primary key for the `Question` entity.  
   - Used to uniquely identify each question in the database.

2. **`Content`**:  
   - Represents the actual content of the question.  
   - Initialized as an empty string to ensure it is never `null`.

3. **`PollId`**:  
   - Foreign key to associate the question with a specific `Poll`.  
   - Establishes a relationship between the `Question` and `Poll` entities.

4. **`IsActive`**:  
   - Implements soft delete functionality.  
   - Instead of physically deleting a question, this property is used to mark it as inactive.  
   - Defaults to `true` to ensure new questions are active by default.

5. **`Poll`**:  
   - Navigational property to represent the relationship with the `Poll` entity.  
   - Helps Entity Framework understand the foreign key relationship.

6. **`Answers`**:  
   - Represents the collection of answers associated with the question.  
   - Initialized as an empty array to ensure it is never `null`.

### Additional Notes:
- The class is `sealed` to prevent further inheritance.  
- It inherits from `AuditableEntity`, which likely includes audit logging fields like `CreatedAt`, `UpdatedAt`, etc.  
- Soft delete is implemented for risk management, as per business requirements.  
- The `Answers` property ensures a one-to-many relationship between `Question` and `Answer` entities.


Here’s the complete content for the `Answer` class, `Poll` entity updates, `ApplicationDbContext` changes, entity configurations, and migration steps, organized in a single block :


# Answer Entity Class

The `Answer` class represents the entity for answers in the application. It includes properties for the answer's content, its relationship with a question, and its active status. Below is the breakdown of the class and its properties:

```csharp
public sealed class Answer
{
    // Primary key for the Answer entity
    public int Id { get; set; }

    // Content of the answer. Initialized as an empty string to avoid null references.
    public string Content { get; set; } = string.Empty;

    // Foreign key to associate the answer with a specific question.
    public int QuestionId { get; set; }

    // Boolean property to implement soft delete. 
    // By default, it is set to `true` to indicate the answer is active.
    public bool IsActive { get; set; } = true;

    // Navigational property to represent the relationship with the `Question` entity.
    // This helps Entity Framework understand the foreign key relationship.
    public Question Question { get; set; } = default!;
}
```

### Notes on Properties:
1. **`Id`**:  
   - The primary key for the `Answer` entity.  
   - Used to uniquely identify each answer in the database.

2. **`Content`**:  
   - Represents the actual content of the answer.  
   - Initialized as an empty string to ensure it is never `null`.

3. **`QuestionId`**:  
   - Foreign key to associate the answer with a specific `Question`.  
   - Establishes a relationship between the `Answer` and `Question` entities.

4. **`IsActive`**:  
   - Implements soft delete functionality.  
   - Instead of physically deleting an answer, this property is used to mark it as inactive.  
   - Defaults to `true` to ensure new answers are active by default.

5. **`Question`**:  
   - Navigational property to represent the relationship with the `Question` entity.  
   - Helps Entity Framework understand the foreign key relationship.

---

# Poll Entity Updates

The `Poll` entity has been updated to include a collection of questions. Below is the updated class:

```csharp
public sealed class Poll : AuditableEntity
{
    // Primary key for the Poll entity
    public int Id { get; set; }

    // Title of the poll. Initialized as an empty string to avoid null references.
    public string Title { get; set; } = string.Empty;

    // Summary of the poll. Initialized as an empty string to avoid null references.
    public string Summary { get; set; } = string.Empty;

    // Boolean property to indicate whether the poll is published.
    public bool IsPublished { get; set; }

    // Date when the poll starts.
    public DateOnly StartsAt { get; set; }

    // Date when the poll ends.
    public DateOnly EndsAt { get; set; }

    // Collection of questions associated with the poll.
    // Initialized as an empty array to avoid null references.
    public ICollection<Question> Questions { get; set; } = [];
}
```

---

# ApplicationDbContext Updates

Two `DbSet` properties have been added to the `ApplicationDbContext` for the `Answer` and `Question` entities:

```csharp
public class ApplicationDbContext : DbContext
{
    // DbSet for the Answer entity
    public DbSet<Answer> Answers { get; set; }

    // DbSet for the Question entity
    public DbSet<Question> Questions { get; set; }
}
```

---

# Entity Configurations

### Answer Configuration
The `AnswerConfiguration` class defines the configuration for the `Answer` entity, including a unique composite index and a maximum length for the `Content` property:

```csharp
public class AnswerConfiguration : IEntityTypeConfiguration<Answer>
{
    public void Configure(EntityTypeBuilder<Answer> builder)
    {
        // Unique composite index for QuestionId and Content
        builder.HasIndex(x => new { x.QuestionId, x.Content }).IsUnique();

        // Maximum length for the Content property
        builder.Property(x => x.Content).HasMaxLength(1000);
    }
}
```

### Question Configuration
The `QuestionConfiguration` class defines the configuration for the `Question` entity, including a unique composite index and a maximum length for the `Content` property:

```csharp
public class QuestionConfiguration : IEntityTypeConfiguration<Question>
{
    public void Configure(EntityTypeBuilder<Question> builder)
    {
        // Unique composite index for PollId and Content
        builder.HasIndex(x => new { x.PollId, x.Content }).IsUnique();

        // Maximum length for the Content property
        builder.Property(x => x.Content).HasMaxLength(1000);
    }
}
```

---

# Migration Steps

1. **Add Migration**:  
   Run the following command in the Package Manager Console or terminal to create a new migration:  
   ```bash
   Add-Migration AddQuestionAndAnswerEntities
   ```

2. **Update Database**:  
   Apply the migration to the database by running:  
   ```bash
   Update-Database
   ```

---

### Summary
- The `Answer` and `Question` entities have been added with their respective properties and configurations.  
- The `Poll` entity has been updated to include a collection of questions.  
- The `ApplicationDbContext` has been updated to include `DbSet` properties for `Answer` and `Question`.  
- Entity configurations ensure data integrity with unique constraints and maximum lengths.  
- A migration has been added and applied to update the database schema.


Here’s the complete content for the migration process, including the issue with cascade delete, the solution to restrict delete behavior globally, and the steps to add and update the migration, organized in a single block :


# Migration Process and Cascade Delete Issue

### Adding Migration
To create a new migration for adding the `Questions` and `Answers` tables, run the following command in the Package Manager Console:

```bash
add-migration AddQuestionsAndAnswersTables
```

### Updating Database
After adding the migration, attempt to update the database:

```bash
update-database
```

---

### Cascade Delete Issue
When updating the database, you may encounter an issue due to the **cascade delete** behavior between the `Poll` and `Questions` entities. This creates a cycle or multiple cascade paths, leading to an infinite loop:
- Deleting a `Poll` will delete its associated `Questions`.
- Deleting a `Question` will attempt to delete its associated `Poll`.

This behavior is risky and undesirable for data integrity.

---

### Solutions to Cascade Delete Issue
There are two possible solutions:
1. **Local Solution**:  
   Modify the `OnDelete` behavior to `Restrict` in the configuration class for each entity.  
2. **Global Solution**:  
   Apply a global configuration in the `ApplicationDbContext` to set `OnDelete` behavior to `Restrict` for all foreign keys.

We will implement the **global solution** for consistency and efficiency.

---

### Implementing Global Restrict Behavior
To prevent cascade delete globally, modify the `OnModelCreating` method in the `ApplicationDbContext`:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    // Apply all configurations from the assembly
    modelBuilder.ApplyConfigurationsFromAssembly(Assembly.GetExecutingAssembly());

    // Get all foreign keys in the database with cascade delete behavior
    var cascadeFKs = modelBuilder.Model.GetEntityTypes()
        .SelectMany(t => t.GetForeignKeys()) // All foreign keys in the database
        .Where(fk => fk.DeleteBehavior == DeleteBehavior.Cascade && !fk.IsOwnership); // Exclude ownership relationships

    // Change the delete behavior to Restrict for each foreign key
    foreach (var fk in cascadeFKs)
    {
        fk.DeleteBehavior = DeleteBehavior.Restrict;
    }

    base.OnModelCreating(modelBuilder);
}
```

---

### Recreating the Migration
1. **Remove the Previous Migration**:  
   Since the previous migration had cascade delete behavior, remove it first:  
   ```bash
   remove-migration
   ```

2. **Add the Updated Migration**:  
   Recreate the migration with the global restrict behavior:  
   ```bash
   add-migration AddQuestionsAndAnswersTables
   ```

   This will drop the existing foreign keys with cascade behavior and add new foreign keys with restrict behavior.

3. **Update the Database**:  
   Apply the migration to the database:  
   ```bash
   update-database
   ```

---

### Summary
- The **cascade delete** issue was resolved by globally setting the `OnDelete` behavior to `Restrict` in the `ApplicationDbContext`.
- The migration was recreated to reflect this change, ensuring data integrity and preventing unwanted deletions.
- The database was successfully updated with the new schema and restrict behavior for all foreign keys.





Here’s the complete content for the `QuestionRequest` record and its validator:


# QuestionRequest and Validator

The `QuestionRequest` record and its validator are used to define the structure and validation rules for creating or updating a question. Below is the implementation:

---

## QuestionRequest Record

The `QuestionRequest` record contains the content of the question and a list of answers allowed for the question:

```csharp
public record QuestionRequest(string Content, List<string> Answers);
```

### Properties:
1. **`Content`**:  
   - Represents the content of the question.  
2. **`Answers`**:  
   - Represents the list of answers allowed for the question.

---

## QuestionRequestValidator

The `QuestionRequestValidator` class defines the validation rules for the `QuestionRequest` record. It inherits from `AbstractValidator<QuestionRequest>` and implements the following rules:

```csharp
public class QuestionRequestValidator : AbstractValidator<QuestionRequest>
{
    public QuestionRequestValidator()
    {
        // Rule for Content: Must not be empty and have a length between 3 and 1000 characters
        RuleFor(x => x.Content)
            .NotEmpty()
            .Length(3, 1000);

        // Rule for Answers: Must have at least 2 answers
        RuleFor(x => x.Answers)
            .Must(x => x.Count > 1)
            .WithMessage("Question should have at least 2 answers");

        // Rule for Answers: Must not have duplicate answers
        RuleFor(x => x.Answers)
            .Must(x => x.Distinct().Count() == x.Count)
            .WithMessage("You cannot add duplicated answers for the same question");
    }
}
```

### Validation Rules:
1. **Content Validation**:  
   - The `Content` must not be empty.  
   - The `Content` length must be between 3 and 1000 characters.

2. **Answers Validation**:  
   - The `Answers` list must contain at least 2 answers.  
   - The `Answers` list must not contain duplicate values.

---

### Summary
- The `QuestionRequest` record defines the structure for creating or updating a question.  
- The `QuestionRequestValidator` ensures that the `QuestionRequest` data meets the required validation rules.  
- Validation rules include checks for content length, minimum number of answers, and duplicate answers.



Here’s the Notes for the `QuestionErrors` class, `AnswerResponse` and `QuestionResponse` records, `IQuestionService` interface, and `QuestionService` implementation, organized :


# QuestionErrors Class

The `QuestionErrors` class defines custom error messages related to question operations:

```csharp
public static class QuestionErrors
{
    public static readonly Error QuestionNotFound =
        new("Question.NotFound", "No question was found with the given ID");

    public static readonly Error DuplicatedQuestionContent =
        new("Question.DuplicatedContent", "Another question with the same content already exists");
}
```

---

# AnswerResponse and QuestionResponse Records

### AnswerResponse Record
The `AnswerResponse` record represents the response shape for an answer:

```csharp
public record AnswerResponse(int Id, string Content);
```

### QuestionResponse Record
The `QuestionResponse` record represents the response shape for a question, including its answers:

```csharp
public record QuestionResponse(
    int Id,
    string Content,
    IEnumerable<AnswerResponse> Answers
);
```

---

# Question Service

### IQuestionService Interface
The `IQuestionService` interface defines the contract for the question service, including the `AddAsync` method:

```csharp
public interface IQuestionService
{
    Task<Result<QuestionResponse>> AddAsync(int pollId, QuestionRequest request, CancellationToken cancellationToken = default);
}
```

### QuestionService Implementation
The `QuestionService` class implements the `IQuestionService` interface and handles the logic for adding a question:

```csharp
public class QuestionService(ApplicationDbContext context) : IQuestionService
{
    private readonly ApplicationDbContext _context = context;

    public async Task<Result<QuestionResponse>> AddAsync(int pollId, QuestionRequest request, CancellationToken cancellationToken = default)
    {
        // Check if the poll exists with the given ID
        var pollIsExists = await _context.Polls.AnyAsync(x => x.Id == pollId, cancellationToken: cancellationToken);
        if (!pollIsExists)
            return Result.Failure<QuestionResponse>(PollErrors.PollNotFound);

        // Check if the question content already exists in the poll
        var questionIsExists = await _context.Questions.AnyAsync(x => x.Content == request.Content && x.PollId == pollId, cancellationToken: cancellationToken);
        if (questionIsExists)
            return Result.Failure<QuestionResponse>(QuestionErrors.DuplicatedQuestionContent);

        // Adapt the request to the Question domain model
        var question = request.Adapt<Question>();
        question.PollId = pollId;

        // Add answers to the question
        request.Answers.ForEach(answer => question.Answers.Add(new Answer { Content = answer }));

        // Save the question to the database
        await _context.AddAsync(question, cancellationToken);
        await _context.SaveChangesAsync(cancellationToken);

        // Return the success response
        return Result.Success(question.Adapt<QuestionResponse>());
    }
}
```

---

### Service Registration
To register the `QuestionService` in the dependency injection container, add the following line to your `Startup.cs` or `Program.cs`:

```csharp
services.AddScoped<IQuestionService, QuestionService>();
```

---

### Summary
- The `QuestionErrors` class defines custom errors for question operations.  
- The `AnswerResponse` and `QuestionResponse` records define the response shapes for answers and questions.  
- The `IQuestionService` interface and `QuestionService` class handle the logic for adding a question, including validation and database operations.  
- The service is registered in the dependency injection container for use throughout the application.




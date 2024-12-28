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

```markdown
# Answer Entity Class

The `Answer` class represents the entity for answers in the application. It includes properties for the answer's content, its relationship with a question, and its active status. Below is the breakdown of the class and its properties:
```
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






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

Here’s the organized markdown (MD) format for your notes. Each line is annotated as per your instructions:

```markdown
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
```

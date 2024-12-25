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

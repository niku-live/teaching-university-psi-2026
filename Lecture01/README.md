# Lecture 1

## What was discussed

All course information can be found at [MIF VMA Software engineering course (2026)](https://emokymai.vu.lt/course/view.php?id=1144)

## Team Homework

📋 **Action Required**: Teams must complete the initial setup tasks outlined in the [TODO-LIST.md](TODO-LIST.md)

This includes deciding on your team structure, application theme, technology stack, and setting up your development environment.

Read the [Tools Usage Notes](TOOLS-USAGE.md) before configuring your shared Git repository.

### Teams Setup

- All assignments will be done in **teams of 4-5 people**
- Teams will be responsible for developing an application to solve one of the selected problems

### Weekly Evaluation Process

Each week, teams participate in a peer evaluation process:

1. **Demo Presentation**: Each team demonstrates what changed during the last week (application updates, code changes, task distribution, etc.)

2. **Peer Evaluation**: Team members evaluate each other's contributions
   - Each team member has **X points** to distribute, where X = team members count + 1
   - You must rationalize why you're giving specific points to each team member
   - Points can be split by half (e.g., 1.5 points), but no smaller increments

#### Evaluation Example

**Scenario**: Team of 4 people, so each person has 5 points to distribute

**My point distribution**:
- **2.0 points** to myself - worked on many features and helped others
- **1.5 points** to colleague - implemented dependency injection
- **1.0 point** to teammate - created a couple of new classes and structs  
- **0.5 points** to teammate - joined discussions, attempted implementation but failed to commit

Every team member votes, and the lecturer enters the final totals into the evaluation sheet.

Applying this per member across a few weeks could look like this (each of the 4 members distributes 5 points weekly):

| Week   | Student A    | Student B | Student C | Student D |
|--------|--------------|-----------|-----------|-----------|
| 1      | 5            | 5         | 5         | 5         |
| 2      | 6            | 4         | 5         | 5         |
| 3      | 8            | 3         | 4         | 8         |
|--------|--------------|-----------|-----------|-----------|
| Total  | 19           | 12        | 14        | 18        |
| Dist.  | 106% -> 100% | 67%       | 78%       | 100%      |

The **Dist.** row shows each student's total as a percentage of the **second-highest** scorer's total (capped at 100%) - this ratio is what determines the individual mark relative to the group's mark.

#### Final Mark Calculation Example

**Scenario**: Lab assignment #1 is settled with a maximum mark of **1.5 points**, using the `Dist.` percentages above.

- Student A and Student D both reach 100% Dist. (A tops the raw totals, D matches the second-highest total), so **both receive the maximum mark**: 1.5 points each.
- Student B and Student C are scored proportionally to the maximum mark, using their own `Dist.` percentage:

| Student   | Dist. | Individual Mark                 |
|-----------|-------|---------------------------------|
| Student A | 100%  | 1.5 (max mark - top score)      |
| Student B | 67%   | 1.01 (67% of 1.5)               |
| Student C | 78%   | 1.17 (78% of 1.5)               |
| Student D | 100%  | 1.5 (max mark - second-highest) |

### Assignment Deadlines & Evaluation

#### Late Submission Penalty
- **20% reduction** in maximum mark for each week late

#### Evaluation Process

**Example scenario**: Week 7 arrives, first assignment deadline

1. **Demo Presentation**
   - Teams demonstrate their application
   - Each deadline is treated like a **Scrum sprint**
   - Must present fully working functionality for implemented features
   - Application evolves: Alpha → Beta → Final versions
   - Even intermediate deadlines require working partial functionality

2. **Requirements Review**
   - Lecturer checks if requirements are fulfilled
   - May ask individual questions to verify knowledge (affects individual marks)
   - **Minimum expectation**: 75% of requirements implemented
   - Quality of implementation matters

3. **Final Scoring**
   - Points from week 1 to current week are summarized
   - Highest scorer gets maximum evaluation
   - Others scored proportionally
   - Close scores may both receive maximum evaluation

4. **Quality Assessment**
   - Lecturer may cap maximum possible mark (e.g., "70% max due to missing X and Y")
   - Teams can disagree and return next week with requested fixes

## Course Grading

- **Laboratory assignments** are worth a total of **5.0 points**: Lab Assignment #1 (1.5), Lab Assignment #2 (1.5), and Lab Assignment #3 (2.0).
- **Written exam** is worth up to **5.0 points**.
- The exam can be taken only after all three laboratory assignments have been settled and at least **3.0 points** have been collected during the semester.
- The exam is passed by earning at least **1.5 out of 5.0 points**.
- Up to **1.0 additional point** may be awarded for outstanding performance during the course or contributions to the course material.

## Lab Assignment Requirements

Full details are in the [Lecture 01 material](https://github.com/smagurauskas/software-engineering/blob/main/01-intro.qmd). Summary of the per-assignment requirements:

### Lab Assignment #1 (Deadline: week 7, Points: 1.5)

Covers material from lectures 1-6.

1. Application can be interacted with using *some* sort of interface. There exists at least one user scenario, which can be demonstrated end to end.
2. Creating and using your own `class`, `struct`, `record` and `enum`. 1 type must be immutable.
3. Property usage in `struct` and `class`.
4. Named and optional argument usage.
5. Extension method usage.
6. Iterating through collections the right way.
7. Using a stream to load data (can be from file, web service, socket etc.).
8. LINQ to Objects used where appropriate (methods or queries). If LINQ is not used in a particular scenario, provide a justification.
9. Implement at least one of the standard .NET interfaces (`IEnumerable`, `IComparable`, `IComparer`, `IEquatable`, `IEnumerator`, etc.)
10. All changes reviewed via pull requests; each PR must have a description explaining what was done and why. Each team member must have authored at least 3 merged PRs and reviewed at least 3 PRs from teammates. A PR is counted as reviewed only if there are meaningful comments and discussions.
11. Uniform coding style is used throughout the project.

### Lab Assignment #2 (Deadline: week 11, Points: 1.5)

Covers material from lectures 7-10.

1. Relational database and Entity Framework is used for storing all the data.
2. Create a generic type with a generic method. Demonstrate how generics removed duplication.
3. Create at least 1 exception type and throw it; meaningfully deal with it by attaching custom handling logic.
4. Usage of `async`/`await`, no IO is performed synchronously.
5. Identify a shared memory scenario and deal with it using concurrent collections or appropriate synchronization mechanisms.
6. Dependency Injection is used everywhere reasonable, no **dependencies** are created manually with `new`.
7. Unit and integration tests coverage at least 50%.
8. All changes reviewed via pull requests (same PR requirements as Assignment #1).
9. Uniform coding style is used throughout the project.

### Lab Assignment #3 (Deadline: week 15, Points: 2.0)

Covers material from lectures 11-14.

1. Short video (4 to 6 minutes) is prepared, showcasing how the application works and its features. Key architectural and code design decisions can also be shown. Must be uploaded to a public video hosting service (i.e. YouTube) or provided as a file.
2. Application is in a stable state. Additional user flows could be asked to be shown during the presentation.
3. Value proposition of the application can be clearly articulated.
4. Application is shown to solve the problem the students have defined.
5. Unit and integration test coverage is at least 80%.
6. Entity Framework migrations are used and run automatically.
7. CI pipeline is set up which gates each pull request; it must have an automated tests check + at least 1 extra gate apart from tests.
8. Application provides live metrics or monitoring for its health and performance (OpenTelemetry or similar).
9. All changes reviewed via pull requests (same PR requirements as Assignment #1).
10. Uniform coding style is used throughout the project.

**Bonus points (max 0.5)**:
1. Application is hosted in some environment and is accessible through a public domain name. All students in the group must be able to explain how the deployment setup works.
2. A metrics observability tool (e.g., Prometheus, Grafana, OpenTelemetry) is integrated and has meaningful dashboards and alerts set up.

## AI Usage Policy

Use of AI tools (e.g., GitHub Copilot, ChatGPT) is **allowed and even encouraged** during the course, following the [Vilnius University guidelines on AI usage](https://www.vusa.lt/lt/vilnius-university-guidelines-).

- Students **own their code** - AI is a tool, not a substitute for understanding your submission.
- You must be able to **explain and understand** any AI-generated code you submit, including its design and trade-offs.
- Knowledge of related subjects (as covered in lectures and assignments) is still expected - AI usage does not excuse gaps in understanding during individual questioning or the exam.

## Creating Your First Project

### Getting Started with Development

For step-by-step instructions on how to create your initial project setup, including:
- Setting up your development environment
- Creating a basic project structure
- Configuring your chosen technology stack
- Best practices for team collaboration

**Reference Guide**: [Project Setup Tutorial](https://github.com/niku-live/teaching-university-psi-2026-playground/tree/lectures/01)

This tutorial walks through the practical steps demonstrated during the lecture and provides additional resources for getting your team project started.
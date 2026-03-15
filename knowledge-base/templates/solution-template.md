# Solution Template

Use this template when contributing a standalone solution to the NeuralDiff knowledge base. Solutions can apply to multiple patterns and focus on the fix rather than the detection.

---

## Solution: [Solution Name]

**ID**: `sol_[category]_[NNN]`
**Applies to**: [List of pattern IDs this solution addresses, e.g., `pat_layout_shift_001`, `pat_responsive_001`]

### Problem Summary

[1-2 sentences describing the problem this solution addresses. Reference the patterns it applies to.]

### Prerequisites

[What the developer needs to know or have in place before applying this solution.]

- [Prerequisite 1]
- [Prerequisite 2]

### Solution

[Step-by-step instructions to implement the fix.]

#### Step 1: [Action]

[Description of what to do and why.]

```css
/* Code for this step */
```

#### Step 2: [Action]

[Description of what to do and why.]

```css
/* Code for this step */
```

#### Step 3: Verify

[How to verify the fix worked.]

```bash
# Verification command or process
```

### Tradeoffs

[Every solution has tradeoffs. Be honest about them.]

| Aspect | Impact |
|---|---|
| [Tradeoff 1] | [Description] |
| [Tradeoff 2] | [Description] |

### Alternative Approaches

[Other ways to solve the same problem, and when to prefer them over this solution.]

1. **[Alternative 1]**: [When to use this instead and how it differs.]
2. **[Alternative 2]**: [When to use this instead and how it differs.]

### Related Patterns

- `pat_[id]` ([Pattern Name])
- `pat_[id]` ([Pattern Name])

### References

- [Link to relevant documentation, specification, or article]
- [Link to related MDN page or CSS spec]

---

## Contribution Guidelines

When writing a solution:

1. **Make it copy-pasteable.** A developer should be able to follow the steps and fix the problem without extensive context.

2. **Document tradeoffs.** Every fix has downsides -- performance, browser support, maintainability. Be transparent.

3. **Include verification.** How does the developer know the fix worked? Include a test or verification step.

4. **Link to patterns.** Solutions should reference the patterns they address, and patterns should link back to solutions.

5. **Provide alternatives.** There is rarely one right answer. Document when different approaches are appropriate.

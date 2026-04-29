# Feature Request: Add "Save Draft" button for homework submissions

## Problem

When filling out a homework submission form, if the page freezes, the browser crashes, or the user navigates away, all entered answers are lost. Users must start over from scratch.

## Proposed Solution

Add a "Save Draft" button alongside the existing "Submit" button that allows users to save their progress without finalizing the submission.

## Implementation Options

### Option A: Minimal (different message only)
- Add "Save Draft" button to the form
- Both buttons save to database identically
- Only the success message differs
- **Pros:** No migration, minimal code changes
- **Cons:** Course page shows "Submitted" for drafts (since a submission record exists)

### Option B: Full (with draft status tracking)
- Add `is_draft` boolean field to `Submission` model
- Add "Save Draft" button to the form
- Drafts show "Open" (or "Draft") on course page instead of "Submitted"
- **Pros:** Clear distinction between draft and submitted
- **Cons:** Requires migration

## Files affected

- `courses/models/homework.py` - Add `is_draft` field (Option B only)
- `courses/views/homework.py` - Handle `is_draft` parameter
- `courses/templates/homework/homework.html` - Add Save Draft button
- `courses/views/course.py` - Check `is_draft` when setting status (Option B only)

## UI Mockup

```
[Save Draft]  [Submit]
```

- **Save Draft:** Secondary button, saves answers with draft message
- **Submit:** Primary button, finalizes submission (existing behavior)

## Testing

Run existing tests to ensure no regressions:
```bash
uv run python manage.py test courses.tests.test_homework
uv run python manage.py test courses.tests.test_course
```

### Manual testing checklist
1. Save draft on new homework → answers persist after page refresh
2. Save draft → course page shows correct status (Open/Draft vs Submitted depending on option)
3. Submit after saving draft → status changes to "Submitted"
4. Scoring still works correctly for submitted homeworks
5. Admin views show correct submission counts

## Questions for maintainers

1. Which option do you prefer (A or B)?
2. Should drafts count toward the "Submitted" count in admin views?
3. Any concerns about the migration for existing data?

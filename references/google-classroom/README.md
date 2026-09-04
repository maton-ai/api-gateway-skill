# Google Classroom Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `google-classroom`
**Base URL proxied:** `classroom.googleapis.com`

## API Path Pattern

```
/google-classroom/v1/{resource}
```

## Common Endpoints

### Courses

#### List Courses
```bash
maton api '/google-classroom/v1/courses'
maton api '/google-classroom/v1/courses?courseStates=ACTIVE'
maton api '/google-classroom/v1/courses?teacherId=me'
```

#### Get Course
```bash
maton api '/google-classroom/v1/courses/{courseId}'
```

#### Create Course
```bash
maton api -X POST '/google-classroom/v1/courses' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "Course Name",
  "ownerId": "me"
}
EOF
```

#### Update Course
```bash
maton api -X PATCH '/google-classroom/v1/courses/{courseId}?updateMask=name' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "Updated Name"
}
EOF
```

#### Delete Course
```bash
maton api -X DELETE '/google-classroom/v1/courses/{courseId}'
```

### Course Work

#### List Course Work
```bash
maton api '/google-classroom/v1/courses/{courseId}/courseWork'
```

#### Create Course Work
```bash
maton api -X POST '/google-classroom/v1/courses/{courseId}/courseWork' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "title": "Assignment Title",
  "workType": "ASSIGNMENT",
  "state": "PUBLISHED",
  "maxPoints": 100
}
EOF
```

### Student Submissions

#### List Submissions
```bash
maton api '/google-classroom/v1/courses/{courseId}/courseWork/{courseWorkId}/studentSubmissions'
```

### Teachers & Students

#### List Teachers
```bash
maton api '/google-classroom/v1/courses/{courseId}/teachers'
```

#### List Students
```bash
maton api '/google-classroom/v1/courses/{courseId}/students'
```

### Announcements

#### List Announcements
```bash
maton api '/google-classroom/v1/courses/{courseId}/announcements'
```

#### Create Announcement
```bash
maton api -X POST '/google-classroom/v1/courses/{courseId}/announcements' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "text": "Announcement text",
  "state": "PUBLISHED"
}
EOF
```

### Topics

#### List Topics
```bash
maton api '/google-classroom/v1/courses/{courseId}/topics'
```

### User Profiles

#### Get Current User
```bash
maton api '/google-classroom/v1/userProfiles/me'
```

### Invitations

#### List Invitations
```bash
maton api '/google-classroom/v1/invitations?courseId={courseId}'
```

## Notes

- PATCH requests require `updateMask` query parameter
- Courses must be archived before deletion
- Student submissions require course work to be in PUBLISHED state
- Use `me` for current user ID
- Pagination uses `pageToken` parameter

## Resources

- [Google Classroom API Documentation](https://developers.google.com/workspace/classroom/reference/rest)
- [Courses Reference](https://developers.google.com/workspace/classroom/reference/rest/v1/courses)
- [CourseWork Reference](https://developers.google.com/workspace/classroom/reference/rest/v1/courses.courseWork)

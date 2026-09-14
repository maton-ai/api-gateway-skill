# Google Classroom

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `google-classroom`
**Upstream base URL:** `classroom.googleapis.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://classroom.googleapis.com/v1/userProfiles/me`
- Gateway: `https://api.maton.ai/google-classroom/v1/userProfiles/me`

### User Profiles API

#### Get Current User

```bash
maton api '/google-classroom/v1/userProfiles/me'
```

**Response:**
```json
{
  "id": "102753038276005039640",
  "name": {
    "givenName": "John",
    "familyName": "Doe",
    "fullName": "John Doe"
  },
  "emailAddress": "john.doe@example.com",
  "permissions": [
    {
      "permission": "CREATE_COURSE"
    }
  ],
  "verifiedTeacher": false
}
```

#### Get User Profile

```bash
maton api '/google-classroom/v1/userProfiles/{userId}'
```

**Note:** `{userId}` is a placeholder. Replace it with a real value before sending the request.

### Courses API

#### List Courses

```bash
maton api '/google-classroom/v1/courses'

maton api '/google-classroom/v1/courses?courseStates=ACTIVE'

maton api '/google-classroom/v1/courses?teacherId=me'

maton api '/google-classroom/v1/courses?studentId=me'

maton api '/google-classroom/v1/courses?pageSize=10'
```

**Query parameters:**
- `courseStates` - Filter by state: `ACTIVE`, `ARCHIVED`, `PROVISIONED`, `DECLINED`, `SUSPENDED`
- `teacherId` - Filter by teacher ID (use `me` for current user)
- `studentId` - Filter by student ID (use `me` for current user)
- `pageSize` - Number of results per page (max 100)
- `pageToken` - Token for next page

**Response:**
```json
{
  "courses": [
    {
      "id": "825635865485",
      "name": "Introduction to Programming",
      "section": "Section A",
      "descriptionHeading": "CS 101",
      "description": "Learn the basics of programming",
      "ownerId": "102753038276005039640",
      "creationTime": "2026-02-14T01:53:58.991Z",
      "updateTime": "2026-02-14T01:53:58.991Z",
      "enrollmentCode": "3qsua37m",
      "courseState": "ACTIVE",
      "alternateLink": "https://classroom.google.com/c/ODI1NjM1ODY1NDg1",
      "guardiansEnabled": false
    }
  ],
  "nextPageToken": "..."
}
```

#### Get Course

```bash
maton api '/google-classroom/v1/courses/{courseId}'
```

**Note:** `{courseId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Course

```bash
maton api -X POST '/google-classroom/v1/courses' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Course Name",
  "section": "Section A",
  "descriptionHeading": "Course Title",
  "description": "Course description",
  "ownerId": "me"
}
JSON
```

**Response:**
```json
{
  "id": "825637533405",
  "name": "Course Name",
  "section": "Section A",
  "ownerId": "102753038276005039640",
  "courseState": "PROVISIONED",
  "enrollmentCode": "abc123"
}
```

#### Update Course

```bash
maton api -X PATCH '/google-classroom/v1/courses/{courseId}?updateMask=name,description' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Course Name",
  "description": "Updated description"
}
JSON
```

**Note:** `{courseId}` is a placeholder. Replace it with a real value before sending the request.

**Note:** Use `updateMask` query parameter to specify which fields to update.

#### Delete Course

```bash
maton api '/google-classroom/v1/courses/{courseId}' -X DELETE
```

**Note:** `{courseId}` is a placeholder. Replace it with a real value before sending the request.

**Note:** Courses must be archived before deletion. To archive, update the course with `courseState: "ARCHIVED"`.

#### List Course Work Materials

```bash
maton api '/google-classroom/v1/courses/{courseId}/courseWorkMaterials'
```

**Note:** `{courseId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Course Work Material

```bash
maton api '/google-classroom/v1/courses/{courseId}/courseWorkMaterials/{courseWorkMaterialId}'
```

**Note:** `{courseId}` and `{courseWorkMaterialId}` are placeholders. Replace each of them with real values before sending the request.

#### List Course Aliases

```bash
maton api '/google-classroom/v1/courses/{courseId}/aliases'
```

**Note:** `{courseId}` is a placeholder. Replace it with a real value before sending the request.

### Course Work API

#### List Course Work

```bash
maton api '/google-classroom/v1/courses/{courseId}/courseWork'

maton api '/google-classroom/v1/courses/{courseId}/courseWork?courseWorkStates=PUBLISHED'

maton api '/google-classroom/v1/courses/{courseId}/courseWork?orderBy=dueDate'
```

**Note:** `{courseId}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `courseWorkStates` - Filter by state: `PUBLISHED`, `DRAFT`, `DELETED`
- `orderBy` - Sort by: `dueDate`, `updateTime`
- `pageSize` - Number of results per page
- `pageToken` - Token for next page

#### Get Course Work

```bash
maton api '/google-classroom/v1/courses/{courseId}/courseWork/{courseWorkId}'
```

**Note:** `{courseId}` and `{courseWorkId}` are placeholders. Replace each of them with real values before sending the request.

#### Create Course Work

```bash
maton api -X POST '/google-classroom/v1/courses/{courseId}/courseWork' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "title": "Assignment Title",
  "description": "Assignment description",
  "workType": "ASSIGNMENT",
  "state": "PUBLISHED",
  "maxPoints": 100,
  "dueDate": {
    "year": 2026,
    "month": 3,
    "day": 15
  },
  "dueTime": {
    "hours": 23,
    "minutes": 59
  }
}
JSON
```

**Note:** `{courseId}` is a placeholder. Replace it with a real value before sending the request.

**Work Types:**
- `ASSIGNMENT` - Regular assignment
- `SHORT_ANSWER_QUESTION` - Short answer question
- `MULTIPLE_CHOICE_QUESTION` - Multiple choice question

**States:**
- `DRAFT` - Not visible to students
- `PUBLISHED` - Visible to students

#### Update Course Work

```bash
maton api -X PATCH '/google-classroom/v1/courses/{courseId}/courseWork/{courseWorkId}?updateMask=title,description' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "title": "Updated Title",
  "description": "Updated description"
}
JSON
```

**Note:** `{courseId}` and `{courseWorkId}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Course Work

```bash
maton api '/google-classroom/v1/courses/{courseId}/courseWork/{courseWorkId}' -X DELETE
```

**Note:** `{courseId}` and `{courseWorkId}` are placeholders. Replace each of them with real values before sending the request.

### Submissions API

#### List Submissions

```bash
maton api '/google-classroom/v1/courses/{courseId}/courseWork/{courseWorkId}/studentSubmissions'

maton api '/google-classroom/v1/courses/{courseId}/courseWork/{courseWorkId}/studentSubmissions?states=TURNED_IN'
```

**Note:** `{courseId}` and `{courseWorkId}` are placeholders. Replace each of them with real values before sending the request.

**Query parameters:**
- `states` - Filter by state: `NEW`, `CREATED`, `TURNED_IN`, `RETURNED`, `RECLAIMED_BY_STUDENT`
- `userId` - Filter by student ID
- `pageSize` - Number of results per page
- `pageToken` - Token for next page

**Note:** Course work must be in `PUBLISHED` state to list submissions.

**Response:**
```json
{
  "studentSubmissions": [
    {
      "courseId": "825635865485",
      "courseWorkId": "825637404958",
      "id": "Cg4I8ufNwwYQ7tSZgYIB",
      "userId": "102753038276005039640",
      "creationTime": "2026-02-14T02:30:00.000Z",
      "state": "NEW",
      "alternateLink": "https://classroom.google.com/..."
    }
  ]
}
```

#### Get Student Submission

```bash
maton api '/google-classroom/v1/courses/{courseId}/courseWork/{courseWorkId}/studentSubmissions/{submissionId}'
```

**Note:** `{courseId}`, `{courseWorkId}` and `{submissionId}` are placeholders. Replace each of them with real values before sending the request.

#### Grade Submission

```bash
maton api -X PATCH '/google-classroom/v1/courses/{courseId}/courseWork/{courseWorkId}/studentSubmissions/{submissionId}?updateMask=assignedGrade,draftGrade' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "assignedGrade": 95,
  "draftGrade": 95
}
JSON
```

**Note:** `{courseId}`, `{courseWorkId}` and `{submissionId}` are placeholders. Replace each of them with real values before sending the request.

#### Return Submission

```bash
maton api -X POST '/google-classroom/v1/courses/{courseId}/courseWork/{courseWorkId}/studentSubmissions/{submissionId}:return' -H 'Content-Type: application/json' --input - <<'JSON'
{}
JSON
```

**Note:** `{courseId}`, `{courseWorkId}` and `{submissionId}` are placeholders. Replace each of them with real values before sending the request.

### Teachers API

#### List Teachers

```bash
maton api '/google-classroom/v1/courses/{courseId}/teachers'
```

**Note:** `{courseId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "teachers": [
    {
      "courseId": "825635865485",
      "userId": "102753038276005039640",
      "profile": {
        "id": "102753038276005039640",
        "name": {
          "givenName": "John",
          "familyName": "Doe",
          "fullName": "John Doe"
        },
        "emailAddress": "john.doe@example.com"
      }
    }
  ]
}
```

#### Get Teacher

```bash
maton api '/google-classroom/v1/courses/{courseId}/teachers/{userId}'
```

**Note:** `{courseId}` and `{userId}` are placeholders. Replace each of them with real values before sending the request.

#### Add Teacher

```bash
maton api -X POST '/google-classroom/v1/courses/{courseId}/teachers' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "userId": "teacher@example.com"
}
JSON
```

**Note:** `{courseId}` is a placeholder. Replace it with a real value before sending the request.

#### Remove Teacher

```bash
maton api '/google-classroom/v1/courses/{courseId}/teachers/{userId}' -X DELETE
```

**Note:** `{courseId}` and `{userId}` are placeholders. Replace each of them with real values before sending the request.

### Students API

#### List Students

```bash
maton api '/google-classroom/v1/courses/{courseId}/students'
```

**Note:** `{courseId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Student

```bash
maton api '/google-classroom/v1/courses/{courseId}/students/{userId}'
```

**Note:** `{courseId}` and `{userId}` are placeholders. Replace each of them with real values before sending the request.

#### Add Student

```bash
maton api -X POST '/google-classroom/v1/courses/{courseId}/students' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "userId": "student@example.com"
}
JSON
```

**Note:** `{courseId}` is a placeholder. Replace it with a real value before sending the request.

#### Remove Student

```bash
maton api '/google-classroom/v1/courses/{courseId}/students/{userId}' -X DELETE
```

**Note:** `{courseId}` and `{userId}` are placeholders. Replace each of them with real values before sending the request.

### Announcements API

#### List Announcements

```bash
maton api '/google-classroom/v1/courses/{courseId}/announcements'
```

**Note:** `{courseId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Announcement

```bash
maton api '/google-classroom/v1/courses/{courseId}/announcements/{announcementId}'
```

**Note:** `{courseId}` and `{announcementId}` are placeholders. Replace each of them with real values before sending the request.

#### Create Announcement

```bash
maton api -X POST '/google-classroom/v1/courses/{courseId}/announcements' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "text": "Announcement text content",
  "state": "PUBLISHED"
}
JSON
```

**Note:** `{courseId}` is a placeholder. Replace it with a real value before sending the request.

**States:**
- `DRAFT` - Not visible to students
- `PUBLISHED` - Visible to students

#### Update Announcement

```bash
maton api -X PATCH '/google-classroom/v1/courses/{courseId}/announcements/{announcementId}?updateMask=text' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "text": "Updated announcement text"
}
JSON
```

**Note:** `{courseId}` and `{announcementId}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Announcement

```bash
maton api '/google-classroom/v1/courses/{courseId}/announcements/{announcementId}' -X DELETE
```

**Note:** `{courseId}` and `{announcementId}` are placeholders. Replace each of them with real values before sending the request.

### Topics API

#### List Topics

```bash
maton api '/google-classroom/v1/courses/{courseId}/topics'
```

**Note:** `{courseId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Topic

```bash
maton api '/google-classroom/v1/courses/{courseId}/topics/{topicId}'
```

**Note:** `{courseId}` and `{topicId}` are placeholders. Replace each of them with real values before sending the request.

#### Create Topic

```bash
maton api -X POST '/google-classroom/v1/courses/{courseId}/topics' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Topic Name"
}
JSON
```

**Note:** `{courseId}` is a placeholder. Replace it with a real value before sending the request.

#### Update Topic

```bash
maton api -X PATCH '/google-classroom/v1/courses/{courseId}/topics/{topicId}?updateMask=name' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Topic Name"
}
JSON
```

**Note:** `{courseId}` and `{topicId}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Topic

```bash
maton api '/google-classroom/v1/courses/{courseId}/topics/{topicId}' -X DELETE
```

**Note:** `{courseId}` and `{topicId}` are placeholders. Replace each of them with real values before sending the request.

### Invitations API

#### List Invitations

```bash
maton api '/google-classroom/v1/invitations?courseId={courseId}'

maton api '/google-classroom/v1/invitations?userId=me'
```

**Note:** `{courseId}` is a placeholder. Replace it with a real value before sending the request.

**Note:** Either `courseId` or `userId` is required.

#### Create Invitation

```bash
maton api -X POST '/google-classroom/v1/invitations' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "courseId": "825635865485",
  "userId": "user@example.com",
  "role": "STUDENT"
}
JSON
```

**Roles:**
- `STUDENT`
- `TEACHER`
- `OWNER`

#### Accept Invitation

```bash
maton api -X POST '/google-classroom/v1/invitations/{invitationId}:accept'
```

**Note:** `{invitationId}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Invitation

```bash
maton api '/google-classroom/v1/invitations/{invitationId}' -X DELETE
```

**Note:** `{invitationId}` is a placeholder. Replace it with a real value before sending the request.

### Notes

- PATCH requests require `updateMask` query parameter
- Courses must be archived before deletion
- Student submissions require course work to be in PUBLISHED state
- Use `me` for current user ID
- Pagination uses `pageToken` parameter

### Resources

- [Google Classroom API Documentation](https://developers.google.com/workspace/classroom/reference/rest)
- [Courses Reference](https://developers.google.com/workspace/classroom/reference/rest/v1/courses)
- [CourseWork Reference](https://developers.google.com/workspace/classroom/reference/rest/v1/courses.courseWork)
- [Maton CLI Manual](https://cli.maton.ai/manual)

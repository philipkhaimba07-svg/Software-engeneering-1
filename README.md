# Technical Writing Exercises

This README contains two documentation samples:

1. [User Manual Procedure: Python Virtual Environment](#1-user-manual-procedure-python-virtual-environment)
2. [API Reference Entry: Create Task](#2-api-reference-entry-create-task)

---

## 1. User Manual Procedure: Python Virtual Environment

Create and activate a Python virtual environment, then install a package inside it. By the end, you will have an isolated environment for your project with the `requests` package installed.

**Audience:** First-semester computing students with basic computer literacy and no prior experience with virtual environments.

### Prerequisites

- A computer running macOS or Linux (Windows differences are in the troubleshooting note)
- Python 3.8 or newer (you will check this in Step 2)
- A terminal application (Terminal on macOS)
- An internet connection
- About 10 minutes

### Steps

1. Open your terminal.
   **Expected result:** A window appears with a prompt ending in `$` or `%`.

2. Type `python3 --version` and press Enter.
   **Expected result:** A version number such as `Python 3.11.4` appears. If you see "command not found", install Python from [python.org](https://www.python.org) first.

3. Type `mkdir my_project` and press Enter.
   **Expected result:** No message appears, and a new folder is created.

4. Type `cd my_project` and press Enter.
   **Expected result:** The prompt now shows `my_project`.

5. Type `python3 -m venv venv` and press Enter.
   **Expected result:** After a few seconds the prompt returns with no message, and a folder named `venv` now exists.

6. Type `source venv/bin/activate` and press Enter.
   **Expected result:** `(venv)` appears at the start of your prompt.

7. Type `pip install requests` and press Enter.
   **Expected result:** Text scrolls past and ends with "Successfully installed requests" and other package names.

8. Type `pip list` and press Enter.
   **Expected result:** A table appears that includes `requests`.

9. When you finish working, type `deactivate` and press Enter.
   **Expected result:** `(venv)` disappears from your prompt.

### Troubleshooting

**Problem:** `ModuleNotFoundError` when running your code.

**Cause:** The most common beginner error is skipping Step 6, or opening a new terminal window later and forgetting to activate again. The package then installs globally instead of in the project.

**Fix:** Check whether `(venv)` appears in your prompt. If it does not, run `source venv/bin/activate` from inside `my_project`, then reinstall the package. On Windows, use `venv\Scripts\activate` instead.

---

## 2. API Reference Entry: Create Task

### `POST /v1/projects/{project_id}/tasks`

Creates a new task inside a project and returns the saved task. The authenticated user must be a member of the project.

### Path Parameters

| Name | Type | Required | Description |
|---|---|---|---|
| `project_id` | string | Yes | Unique ID of the project that will contain the task. |

### Query Parameters

None.

### Headers

| Name | Required | Description |
|---|---|---|
| `Authorization` | Yes | `Bearer <access_token>`, the user's valid access token. |
| `Content-Type` | Yes | Must be `application/json`. |

### Body Parameters

| Name | Type | Required | Description |
|---|---|---|---|
| `title` | string | Yes | Short name of the task, 1 to 120 characters. |
| `description` | string | No | Details about the task, up to 2,000 characters. |
| `assignee_id` | string | Yes | ID of the user responsible for the task. Must be a member of the project. |
| `due_date` | string | Yes | Due date in `YYYY-MM-DD` format. Must not be in the past. |
| `priority` | string | Yes | One of `low`, `medium`, or `high`. |

### Example Request

```http
POST /v1/projects/prj_1045/tasks
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
Content-Type: application/json
```

```json
{
  "title": "Prepare Q4 budget report",
  "description": "Compile department spending and draft summary slides.",
  "assignee_id": "usr_48213",
  "due_date": "2026-12-04",
  "priority": "high"
}
```

### Response Codes

| Code | Meaning |
|---|---|
| `201 Created` | The task was created successfully. |
| `400 Bad Request` | The body is malformed, a required field is missing, or a value is invalid (for example, a priority other than `low`, `medium`, or `high`, or a past due date). |
| `401 Unauthorized` | The access token is missing, invalid, or expired. |
| `403 Forbidden` | The user is not a member of the project, or the assignee is not a project member. |
| `404 Not Found` | The project does not exist. |
| `429 Too Many Requests` | The user has exceeded the rate limit. Wait and retry. |
| `500 Internal Server Error` | An unexpected server problem occurred. Retry later or contact support. |

### Example Successful Response (`201 Created`)

```json
{
  "id": "tsk_90172",
  "project_id": "prj_1045",
  "title": "Prepare Q4 budget report",
  "description": "Compile department spending and draft summary slides.",
  "assignee_id": "usr_48213",
  "due_date": "2026-12-04",
  "priority": "high",
  "status": "open",
  "created_by": "usr_30571",
  "created_at": "2026-11-10T09:42:15Z"
}
```

# MCP Tools Reference

16 tools registered in `src/todo-index.ts`:

## Authentication
| Tool | Description |
|------|-------------|
| `auth-status` | Check auth status, token expiry, account type detection |

## Task Lists (CRUD)
| Tool | Params | Description |
|------|--------|-------------|
| `get-task-lists` | — | Get all lists with metadata (default, shared, owner) |
| `get-task-lists-organized` | `includeIds?`, `groupBy?` | Lists organized by emoji/naming patterns into categories |
| `create-task-list` | `displayName` | Create new list |
| `update-task-list` | `listId`, `displayName` | Rename list |
| `delete-task-list` | `listId` | Delete list + all tasks |

## Tasks (CRUD + archive)
| Tool | Params | Description |
|------|--------|-------------|
| `get-tasks` | `listId`, OData opts (`filter`, `select`, `orderby`, `top`, `skip`, `count`) | Get tasks with filtering/sorting |
| `create-task` | `listId`, `title`, optional: `body`, `dueDateTime`, `startDateTime`, `importance`, `isReminderOn`, `reminderDateTime`, `status`, `categories` | Create task |
| `update-task` | `listId`, `taskId`, same optionals as create | Update task properties |
| `delete-task` | `listId`, `taskId` | Delete task + checklist items |
| `archive-completed-tasks` | `sourceListId`, `targetListId`, `olderThanDays?` (90), `dryRun?` | Move old completed tasks between lists |

## Checklist Items (subtasks)
| Tool | Params | Description |
|------|--------|-------------|
| `get-checklist-items` | `listId`, `taskId` | Get subtasks for a task |
| `create-checklist-item` | `listId`, `taskId`, `displayName`, `isChecked?` | Add subtask |
| `update-checklist-item` | `listId`, `taskId`, `checklistItemId`, `displayName?`, `isChecked?` | Update subtask |
| `delete-checklist-item` | `listId`, `taskId`, `checklistItemId` | Delete subtask |

## Organization Categories (get-task-lists-organized)

Lists are auto-categorized by emoji prefix and naming patterns:
- ⭐ Special Lists (default, flagged emails)
- 👥 Shared Lists
- 💼 Work (prefix: "Work", "SBIR")
- 👪 Family
- 🏡 Properties
- 🛒 Shopping Lists
- 🚗 Travel & Rangeley
- 🎉 Seasonal & Events
- 📚 Reading
- 📦 Archives (pattern: "(Location - Archived)")
- 📋 Other Lists

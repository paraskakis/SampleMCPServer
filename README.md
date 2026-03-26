# MCP Server Requirements

The "Task Manager" MCP Server is meant to be used with ChatGPT and other LLMs or agents to help users manage a simple personal task list. Users can view their tasks, create new ones, update existing ones, or delete tasks they no longer need.

Tasks have an id, a name, and a status. Status can be "todo", "in_progress", or "done". The id is a numeric identifier assigned by the server when a task is created. It is not provided by the user.

Name the MCP server exactly as described and use the exact metadata below, including setting annotations exactly as described.

**NOTE:** Use enums where possible and fully describe all parameters.

## Name:
Task Manager

## Description:
A simple personal task list manager. Users can create, view, update, and delete tasks. Each task has a server-assigned id, a name, and a status (todo, in_progress, or done). Designed for quick personal task tracking directly from a chat interface. Not intended for project management, team collaboration, or complex workflows.

## Evals:
### Golden Prompts:
#### Direct Prompts:
- Show me all my tasks
- Add a task called "Buy groceries"
- Mark "Buy groceries" as done
- Delete the "Buy groceries" task
- What tasks are in progress?
- Change "Write report" to in_progress
#### Indirect Prompts:
- What do I need to do today?
- I just finished the report
- Remind me to call the dentist
- I don't need to do laundry anymore
- What's left on my list?
- I'm starting work on the presentation
#### Negative Prompts:
- Create a Jira ticket
- Assign this task to Sarah
- Set a due date for Friday
- Show me the project timeline
- Schedule a meeting for tomorrow
- Track time spent on this task

## Tools:

### 1. View tasks
#### Name:
`get_tasks`
#### Description:
Use this tool to retrieve and display tasks from the task list. Returns matching tasks with their id, name, and current status. Supports filtering by status (todo, in_progress, done) or returns all tasks if no filter is provided. Use this when the user wants to see what's on their list, check progress, or find specific tasks.
#### Parameters:
- `status` (optional, enum: "todo", "in_progress", "done"): Filter tasks by status. If omitted, returns all tasks.
#### Metadata:
- Set `readOnlyHint` to `true`
- Set `destructiveHint` to `false`
- Set `openWorldHint` to `false`

### 2. Create a task
#### Name:
`create_task`
#### Description:
Use this tool to add a new task to the task list. Requires a task name. Status defaults to "todo" unless specified. Use this when the user wants to add something to their list, remember to do something, or track a new item.
#### Parameters:
- `name` (required, string): The name or description of the task.
- `status` (optional, enum: "todo", "in_progress", "done", default: "todo"): The initial status of the task.
#### Metadata:
- Set `readOnlyHint` to `false`
- Set `destructiveHint` to `false`
- Set `openWorldHint` to `false`

### 3. Delete a task
#### Name:
`delete_task`
#### Description:
Use this tool to permanently remove a task from the task list. Requires the task id to identify which task to delete. Use this when the user no longer needs a task tracked, wants to clean up their list, or explicitly asks to remove an item. This action cannot be undone.
#### Parameters:
- `id` (required, integer): The id of the task to delete.
#### Metadata:
- Set `readOnlyHint` to `false`
- Set `destructiveHint` to `true`
- Set `openWorldHint` to `false`

### 4. Update a task
#### Name:
`change_task`
#### Description:
Use this tool to update an existing task's name or status. Requires the task id to identify which task to change. Use this when the user wants to mark a task as done, start working on a task, rename a task, or change its status in any way. At least one of name or status must be provided.
#### Parameters:
- `id` (required, integer): The id of the task to update.
- `name` (optional, string): A new name for the task. If omitted, the name stays the same.
- `status` (optional, enum: "todo", "in_progress", "done"): The new status for the task. If omitted, the status stays the same.
#### Metadata:
- Set `readOnlyHint` to `false`
- Set `destructiveHint` to `false`
- Set `openWorldHint` to `false`


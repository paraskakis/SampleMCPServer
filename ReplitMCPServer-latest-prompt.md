Build an MCP (Model Context Protocol) server with the following specifications:
1. **Protocol and SDK**
   - Use MCP protocol version `2026-07-28`.
   - Use the stable v2 TypeScript SDK packages that officially support this protocol revision.
   - Use `@modelcontextprotocol/server` for the MCP server and HTTP handler.
   - Use `@modelcontextprotocol/node` for the Node.js/Express adapter.
   - Do not use the legacy `@modelcontextprotocol/sdk` v1 package.
   - Explicitly opt the server into the `2026-07-28` protocol behavior. Installing the v2 packages alone is not sufficient.
   - Do not manually claim support by changing a version string without enabling the SDK’s official `2026-07-28` behavior.
2. **Transport**
   - Use the v2 SDK’s HTTP serving model with `createMcpHandler()` from `@modelcontextprotocol/server`.
   - Adapt the web-standard handler to Express using `toNodeHandler()` from `@modelcontextprotocol/node`.
   - Do not use the deprecated SSE transport.
   - Follow the MCP `2026-07-28` Streamable HTTP and protocol-core requirements.
3. **Endpoint**
   - Create one MCP endpoint at `/mcp`.
   - Mount it in Express using `app.all("/mcp", ...)`.
   - The same endpoint must handle every HTTP method required by the SDK and protocol.
   - Ensure `/mcp` is included in the shared API server’s Replit proxy routing configuration.
4. **Tools**
   - Register all tools described in the attached requirements file.
   - Preserve the exact server name, description, tool names, titles, descriptions, parameters, enum values, and annotations from that file.
   - Do not add unsupported concepts such as projects, assignments, due dates, meetings, or time tracking.
5. **Input and Output Schemas**
   - Use Zod 4 schemas where supported by the v2 SDK.
   - Use enums wherever possible.
   - Fully describe every input and output field with `.describe()` strings so models understand its meaning.
   - Every tool must have an `outputSchema` matching the exact structured data it returns.
   - Ensure refined or transformed schemas still produce complete JSON Schema definitions. In particular, verify that every expected input property appears in the advertised tool schema.
   - Enforce the `change_task` requirement that at least one of `name` or `status` is provided.
6. **Tool Results**
   - Every successful tool handler must return both:
     - `content`: an array containing one text item whose text is the JSON-stringified result.
     - `structuredContent`: the structured result object matching the tool’s `outputSchema`.
   - The two representations must contain equivalent data.
7. **Tool Metadata**
   - Keep each tool’s `title` as a top-level tool configuration property, not inside `annotations`.
   - Set `readOnlyHint`, `destructiveHint`, and `openWorldHint` exactly as specified in the attached requirements.
8. **Data**
   - No database or persistent storage is needed.
   - Store tasks in server-process memory.
   - Assign each new task a numeric ID on the server.
   - Task IDs must not be supplied by users.
   - Clearly document that restarting or redeploying the server clears all tasks.
9. **Protocol State**
   - Follow the `2026-07-28` protocol’s stateless core and the v2 SDK’s recommended request-handling model.
   - Do not reproduce the legacy v1 per-session transport `Map` pattern unless the v2 SDK documentation explicitly requires state for a feature being used.
   - Keep the in-memory task list at application scope so tasks remain available across requests until the process restarts.
   - Use `createMcpHandler()` in the officially documented way for serving both modern `2026-07-28` traffic and any intentionally enabled legacy fallback.
   - If legacy fallback is enabled, document it and verify that modern clients still negotiate `2026-07-28`.
   - Do not silently fall back to a 2025 protocol revision when the client explicitly requires `2026-07-28`.
10. **DNS Rebinding and Origin Protection**
    - Implement the protections required by the MCP `2026-07-28` transport specification.
    - Validate browser `Origin` headers on `/mcp`.
    - Reject unrecognized origins with HTTP `403`.
    - Allow requests without an `Origin` header when appropriate for non-browser MCP clients.
    - Validate request hosts using the v2 SDK’s official middleware or helpers when available.
    - Support the active Replit development and published hosts without hardcoding a guessed production URL.
11. **JSON-RPC Validation**
    - Validate JSON-RPC messages using the official schemas and validation behavior provided by the v2 SDK.
    - Before normal MCP dispatch, distinguish:
      - Malformed JSON: JSON-RPC error code `-32700` (`Parse error`)
      - Valid JSON containing an invalid JSON-RPC request: error code `-32600` (`Invalid Request`)
    - Return HTTP `400` and `id: null` in both cases.
    - Avoid duplicating validation already performed correctly by the official v2 handler, but verify that its externally visible responses meet these requirements.
12. **Protocol Verification**
    - Test initialization or discovery using a client explicitly configured for `2026-07-28`.
    - Confirm that the negotiated protocol revision is exactly `2026-07-28`.
    - Confirm that a client pinned to `2026-07-28` fails clearly rather than silently downgrading if modern negotiation is unavailable.
    - Verify tool discovery advertises:
      - Exactly the required tool names
      - Top-level titles
      - Complete input schemas
      - Complete output schemas
      - Correct annotations
    - Test both malformed JSON and invalid JSON-RPC envelopes.
    - Test the complete task sequence:
      1. Create a task
      2. Retrieve it
      3. Change its status
      4. Delete it
    - Confirm every tool response contains both `content` and `structuredContent`.
13. **Frontend**
    - Build this inside a React web app, not as an API-only artifact, so Replit displays a normal web preview with a URL bar.
    - The React app should be a simple static landing page.
    - The landing page should show:
      - The server’s current status
      - The MCP endpoint URL
      - Protocol version `2026-07-28`
      - The four supported tools
      - A notice that task data is stored in memory and is cleared by a restart
    - Derive the displayed MCP endpoint from the current browser origin. Do not hardcode or guess the published Replit URL.
    - Put all MCP server logic in the shared Express API server at `/mcp`.
    - Do not create a design canvas or mockup artifact.
14. **Completion Requirements**
    - Run TypeScript type checking for both the React app and shared API server.
    - Start the existing managed workflows and confirm they run without errors.
    - Confirm `/mcp` is reachable through Replit’s shared proxy, not only through the API server’s internal port.
    - Do not report completion unless the server actually negotiates protocol version `2026-07-28`.

Build an MCP (Model Context Protocol) server with the following specifications:

1. **Transport:** Use the Streamable HTTP transport from `@modelcontextprotocol/sdk/server/streamableHttp.js` (NOT SSE - that's deprecated as of March 2025)

2. **Endpoint:** Create a single `/mcp` endpoint using `app.all("/mcp", ...)` that handles both GET and POST requests on the same path

3. **Tools:** Register the tools described in the attached requirements file

4. **Metadata:** Use the information in the requirements file but note, MCP Tool Output Schema is required:

a. Every tool must include an outputSchema defined with Zod that matches the shape of data the tool returns. Derive the schema from the data structure described in the requirements — use descriptive `.describe()` strings on every field so models understand what each field means. The tool handler must always return both:
- `content` — an array with one text item containing the JSON-stringified result (backwards compatibility for older clients).
- `structuredContent` — the structured result object matching the outputSchema.

b. When registering tools with `server.registerTool()`, the `title` field must be a top-level property of the config object — not nested inside `annotations`. Example: `{ title: "My Tool", description: "...", inputSchema: ..., annotations: { ... } }`

5. **Data:** No persistence needed for now, use in-memory storage

6. **Session Management:** Use a Map to store transports by session ID. For new POST requests without a session ID, create a new `StreamableHTTPServerTransport` with a `sessionIdGenerator` that returns a UUID. For requests with an existing session ID header (`mcp-session-id`), reuse the existing transport.

7. **Server Setup:** Use `McpServer` from `@modelcontextprotocol/sdk/server/mcp.js` with name and version of this MCP Server

8. **Frontend:** A simple landing page showing the server status and the MCP endpoint URL

9. **Dependencies:** Install `@modelcontextprotocol/sdk` exactly as is. Use the latest MCP protocol version (2025-11-25).

10. **Implement DNS rebinding protection per MCP spec Transports § 2.0.1:** validate the Origin header on /mcp requests and reject unrecognized origins with 403.

11. **Very Important, Do Not Skip:** Build this inside a React web app (not an API artifact) so I can see a preview with a URL bar in Replit. The React app should just be a simple static landing page. The MCP server logic goes in the shared Express API server at /mcp. Do not create any design canvas or mockup artifacts.

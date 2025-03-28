# Debugging the Google Flights MCP Server: Finding the Tool Contract

This document outlines the steps taken to determine the available tools and their parameters (the "contract") for the `google-flights` MCP server running within the LibreChat Docker container. This was necessary to update the `flight-agent-instructions.md` file accurately.

## Background

When integrating a new MCP server, it's crucial to understand the exact tools it provides and the specific arguments (parameters) each tool expects. While the server might announce its available tools upon connection (as seen in the logs), the detailed parameter information often requires inspecting the server's source code.

## Process: Using `docker exec` and `grep`

Since the `google-flights` MCP server code (`flights-mcp-server.py`) is located inside the running `LibreChat` container at `/app/google-flights-mcp/src/`, we can use `docker exec` to run commands inside the container and inspect the file.

The `grep` command is particularly useful here. We can use it to search for the Python function definitions (`def function_name(...)`) corresponding to the tools announced by the server. The `-A 15` flag tells `grep` to show the 15 lines *after* each match, which usually includes the function signature (listing the parameters) and the docstring (explaining the tool's purpose and arguments).

## Commands Used

The following `docker exec` commands were run sequentially to extract the definition for each known tool:

1.  **Find `search_flights` definition:**
    ```bash
    docker exec -it LibreChat sh -c "grep -A 15 'def search_flights' /app/google-flights-mcp/src/flights-mcp-server.py"
    ```
    *Purpose:* To find the function signature and docstring for the `search_flights` tool.

2.  **Find `airport_search` definition:**
    ```bash
    docker exec -it LibreChat sh -c "grep -A 15 'def airport_search' /app/google-flights-mcp/src/flights-mcp-server.py"
    ```
    *Purpose:* To find the function signature and docstring for the `airport_search` tool.

3.  **Find `get_travel_dates` definition:**
    ```bash
    docker exec -it LibreChat sh -c "grep -A 15 'def get_travel_dates' /app/google-flights-mcp/src/flights-mcp-server.py"
    ```
    *Purpose:* To find the function signature and docstring for the `get_travel_dates` tool.

4.  **Find `update_airports_database` definition:**
    ```bash
    docker exec -it LibreChat sh -c "grep -A 15 'def update_airports_database' /app/google-flights-mcp/src/flights-mcp-server.py"
    ```
    *Purpose:* To find the function signature and docstring for the `update_airports_database` tool.

## Outcome

By examining the output of these commands, we were able to determine the exact parameters required for each tool, including their names, expected data types, and whether they are optional or required. This information formed the basis for updating the `flight-agent-instructions.md` file.
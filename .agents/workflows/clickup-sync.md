---
description: Fetch Epic and Stories from ClickUp and save to ACE-Planning folder
---
# ClickUp Epic & Story Fetcher Workflow

This workflow defines the steps for the agent to fetch an Epic and its Stories from a ClickUp link and save them locally in a consistent markdown format.

## Steps

1. **Understand the Target**:
   - The USER will provide a ClickUp Epic link (e.g., `https://app.clickup.com/t/25605274/ACE-32`).
   - The goal is to scrape/read the Epic details and all its associated Stories, and then write them locally.

2. **Fetching Data**:
   - Because ClickUp requires authentication, use the `browser_subagent` tool to navigate to the link.
   - If `browser_subagent` fails to access the relevant information due to login constraints, proactively ask the USER to paste the raw text or markdown of the Epic and Story descriptions.

3. **Analyzing the Data**:
   - Identify the **Epic ID** (e.g., `ACE-32`) and the **Epic Name**.
   - For each related Story, identify its **Story ID** (e.g., `ACE-689`), **Story Name** (e.g., `STORY-SOC-01...`), **Status**, **Assignees**, and **Description**.

4. **Directory Structure**:
   - Target base directory: `D:\Work\Meaw - Q\ACE\ACE-Planning\Omnichat`.
   - Ensure the Epic folder exists (e.g., `EPIC-<EPIC_ID>`). If not, the file creation steps below will automatically safely create the parent directories.

5. **Saving Files**:
   - For each Story, use `write_to_file` to create a markdown file:
     `D:\Work\Meaw - Q\ACE\ACE-Planning\Omnichat\EPIC-<EPIC_ID>\<STORY_ID>_<STORY_NAME_SANITIZED>.md`
     *(Replace spaces and invalid characters in the story name with underscores `_`)*
     
   - **File Template**: Must closely follow this format:
     ```markdown
     # <STORY_ID> <STORY_NAME>
     
     > **Status:** `<STATUS>` &nbsp; | &nbsp; **Assignees:** `<ASSIGNEES>`
     
     ## 📝 Description
     <DESCRIPTION_CONTENT>
     
     ---
     
     ## 📋 Custom Fields
     | Field | Value |
     |---|---|
     | Product | Omni |
     
     ## 🏗️ Subtasks
     | Subtask | Status |
     |---|---|
     | <SUBTASK_NAME> | <SUBTASK_STATUS> |
     
     ## 🔧 Technical Requirements
     | Requirement | Needed? |
     |---|---|
     | Sequence Diagram | ❌ |
     | ER Diagram | ✅ |
     | API Spec | ✅ |
     ```
   
   // turbo
6. **Create Sub-Asset Folders**:
   - Create a sub-folder for any story assets using the command line:
     ```bash
     mkdir -p "D:\Work\Meaw - Q\ACE\ACE-Planning\Omnichat\EPIC-<EPIC_ID>\STORY-<STORY_ID>"
     ```
     *(Make sure to use proper path quoting for Windows PowerShell like `New-Item -ItemType Directory -Force -Path "..."` if using Powershell)*

7. **Review & Confirm**:
   - Inform the USER about the successfully created files and directories. Ask if any specific `Technical Requirements` (ER Diagram, API Spec, etc.) need to be expanded further.

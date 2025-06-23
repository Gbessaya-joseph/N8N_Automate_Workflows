```markdown
# GitHub Workflow Backup

## General description:
This workflow automatically backs up your n8n workflows to a GitHub repository. It checks for new or updated workflows and saves them as JSON files within a specified directory in your repository.

## Step-by-step operation:

1.  **Schedule Trigger:**  Triggers the workflow based on a schedule. This can be configured to run automatically at set intervals. (Node `Schedule Trigger`)
2.  **Execute Workflow Trigger:** This is the trigger node which is used for running the whole workflow. It is also used as a trigger when running a single n8n workflow.(Node `Execute Workflow Trigger`)
3.  **tag?:** Checks if the workflow has any tags.
4.  **/:** If there are tags, it sets the tag name used for creating a path
5.  **Globals:** Sets global variables used throughout the workflow.  Crucially, you need to configure these to match your GitHub repository details. These include:
    *   `repo.owner`: Your GitHub username or organization name.
    *   `repo.name`: The name of your GitHub repository.
    *   `repo.path`: The directory within your repository where the workflow files will be stored (e.g., "workflows/").  The path includes the tag
6.  **Loop Over Items:** This node is used to split the input items in batches so the workflow can be executed without errors
7.  **Execute Workflow:** Executes the main workflow logic (as a subworkflow) for each workflow item. This is where the actual backup process occurs.
8.  **Get file data:** Retrieves the existing workflow data from the GitHub repository using the file path constructed based on the workflow ID. (Node `Get file data`)
9.  **If file too large:** Checks if the retrieved file's content is not empty.
10. **Get File:** If the retrieved file content is empty, then it will download the data
11. **Merge Items:** Merges the workflow data retrieved from GitHub with the current workflow data. (Node `Merge Items`)
12. **isDiffOrNew:**  Compares the current workflow definition with the one stored in GitHub. It determines if the workflow is new, has been updated, or is unchanged. It also orders the JSON to make for a proper comparison
13. **Check Status:**  A switch node that checks the status of the workflow determined by the previous node, based on if the workflow is same, different, or new.
    *   **Same file - Do nothing:** If the workflow hasn't changed, this path is taken, and the workflow does nothing.  (Node `Same file - Do nothing`)
    *   **File is different:** If the workflow has changed, the workflow updates the file. (Node `File is different`)
    *   **File is new:** If the workflow is new, this node creates a new file in Github. (Node `File is new`)
14. **Create new file:** Uploads the new workflow to GitHub as a JSON file using the GitHub integration. The file name is the workflow's ID, and the file content is the workflow's JSON data. (Node `Create new file`)
15. **Edit existing file:** Updates the existing workflow file in GitHub with the current workflow definition. (Node `Edit existing file`)
16. **Return:** Returns the final output of the workflow, indicating completion. (Node `Return`)

## Dependencies or services used:

*   **n8n**: The n8n automation platform itself.
*   **GitHub**:  Used for storing and managing the workflow backups.  Requires a GitHub account and a personal access token or configured GitHub App for authentication in the `GitHub` node.
*   **n8n API**:  Used for calling sub-workflows and getting the information of the main workflow.

## Scheduled execution:

*   The workflow is triggered automatically based on the schedule configured in the **Schedule Trigger** node.  Also, it's triggered manually via the "Test Workflow" button.

## Tips or remarks:

*   **Prerequisites**: You must have a GitHub account and a repository set up to store the workflow backups.
*   **Configuration**:  The `Globals` node is critical for configuring the workflow.  You MUST edit the values within the 'Set' node to match your GitHub repository details (owner, repository name, and desired file path).
*   **GitHub Permissions**: Ensure your GitHub access token (or the GitHub App configuration) has the necessary permissions to write to your repository.
*   **File Path**: The workflow saves the n8n workflow files in the specified path as `workflow_id.json`.
*   **Tags**: The `repo.path` variable in the Globals node uses the name of the first tag of the workflow as the path, if the workflow has tags.
```
# n8n Multi-Binary Compression & Headless Git Deployment

## Context
When extracting, generating, or formatting multiple files within an n8n workflow to be bundled into a single ZIP archive, standard approaches often rely on Docker-level `zip` utilities or the `bestzip` npm package wrapped in an `Execute Command` node. 
With newer n8n images shifting to distroless environments, OS-level binaries are unavailable and custom Dockerfiles become brittle.

## The Problem
1. **n8n Compression Node Defaults**: Passing multiple items to a Compression node generates multiple ZIP files, not one unified archive.
2. **Binary Field Naming**: `Convert to File` nodes default to naming all internal binaries `File.csv`.
3. **Headless Deployments**: Sending the final ZIP via SSH `git push` nodes to GitHub crashes with "Username required" because the headless terminal cannot prompt for HTTPS credentials.
4. **Execution Log Bottlenecks**: Attempting to process thousands of rows (like GTFS datasets) with `Save Data for Successful Executions` enabled causes extreme Postgres database bloat and hours of network latency.

## The Solution Pattern

### 1. Dynamic Binary Merging (n8n JS Code)
Instead of relying on OS shell tools, use a Code node to merge multiple binary inputs into a single item containing dynamically named properties (`file_0`, `file_1`). This allows the native n8n Compression node to pack them perfectly into a single archive.

```javascript
const combinedBinaries = {};
const binaryFieldNames = [];

let index = 0;
for (const item of $input.all()) {
  if (item.binary && item.binary.data) {
    // Extract real name from JSON metadata
    let realName = item.json.fileName ? item.json.fileName.split('/').pop() : `file_${index}.txt`;
    
    // Copy and correctly rename binary payload
    let newBinary = { ...item.binary.data };
    newBinary.fileName = realName;
    newBinary.fileExtension = "txt"; // Override as needed
    
    // Assign to unique property key
    let propKey = `file_${index}`;
    combinedBinaries[propKey] = newBinary;
    binaryFieldNames.push(propKey);
    
    index++;
  }
}

// Return 1 single item containing all files and a dynamic list of the field names
return [{ 
  json: { zipFields: binaryFieldNames.join(',') }, 
  binary: combinedBinaries 
}];
```

**Compression Node Configuration:**
- **Input Binary Field(s)**: `={{ $json.zipFields }}`

### 2. Headless Git Push
Ensure the target server uses SSH deploy keys or PAT-embedded URLs to bypass interactive terminal prompts during n8n `Execute Command` runs.
```bash
# Recommended Approach (SSH Deploy Key)
git remote set-url origin git@github.com:USERNAME/REPO.git
```

### 3. Execution Data Throttling
For high-volume workflows, ALWAYS set **Save Data for Successful Executions** to `Do not save`. Failing to do so forces n8n to write gigabytes of JSON to Postgres, extending 15-second workflows to over 1 hour.

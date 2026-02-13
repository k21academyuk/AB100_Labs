# Agentic Retrieval Pipeline — Azure AI Search + Foundry Agent Service

This folder contains a corrected and enhanced version of the
[Tutorial: Build an end-to-end agentic retrieval solution using Azure AI Search](https://learn.microsoft.com/azure/search/agentic-retrieval-how-to-create-pipeline).

## Prerequisites

| Requirement | Details |
|---|---|
| Azure AI Search | Any [region that supports agentic retrieval](https://learn.microsoft.com/azure/search/search-region-support) |
| Microsoft Foundry project | Creates the parent resource automatically |
| Embedding model | `text-embedding-3-large` deployed to your project |
| LLM model | `gpt-4.1-mini` deployed to your project |
| Azure CLI | For keyless (Entra ID) authentication |
| Python | 3.10 or later (tested on 3.13) |
| VS Code | With Python + Jupyter extensions |

## Quick Start

```bash
# 1. Clone / copy this folder, then cd into it
cd azio

# 2. Create & activate a virtual environment
python -m venv .venv
source .venv/bin/activate        # Linux / macOS
# .venv\Scripts\activate         # Windows

# 3. Install dependencies
pip install -r requirements.txt

# 4. Copy sample.env → .env and fill in your values
cp sample.env .env
# Edit .env with your actual Azure endpoints and resource IDs

# 5. Login to Azure
az login

# 6. Open the notebook in VS Code and run cells sequentially
code agent-example.ipynb
```

## Required RBAC Roles

### On your Azure AI Search service
| Role | Assignee |
|---|---|
| Search Service Contributor | Your user account |
| Search Index Data Contributor | Your user account |
| Search Index Data Reader | Your user account **AND** project managed identity |

### On your project's parent resource (AI Foundry resource)
| Role | Assignee |
|---|---|
| Azure AI User | Your user account |
| Azure AI Project Manager | Your user account |
| Cognitive Services User | Search service managed identity |

### Critical Setup Steps (often missed)
1. **Enable system-assigned managed identity** on BOTH your Search service and your Foundry project.
2. **Ensure your AI Hub has associated ML resources.** In the Azure Portal → Resource Group → Resource Visualizer, confirm an ML Service exists alongside your AI Hub.
3. **Verify model deployments** exist in your project under *Models + Endpoints*.

## Troubleshooting

### BadRequestError 400 at "Chat with the agent" step
- **AML connection decode error**: Your AI Hub is missing Machine Learning resources. Recreate the project or add ML resources.
- **Agent not found / empty agent list**: The agent creation step failed silently. Verify model deployment name matches `AGENT_MODEL` in `.env`.
- **Authentication failures**: Re-run `az login`, verify all RBAC roles above are assigned.
- **Region mismatch**: Ensure Search service is in a region that supports agentic retrieval.

### Agent not visible in Azure AI Foundry portal
- It can take a few minutes to appear. Run `list(project_client.agents.list())` to confirm programmatically.
- If the list is empty, the `create_version` call failed — check the error output.

## Files

| File | Purpose |
|---|---|
| `agent-example.ipynb` | Main notebook with all pipeline steps + error handling |
| `requirements.txt` | Pinned Python dependencies |
| `sample.env` | Environment variable template (copy to `.env`) |
| `README.md` | This file |

# Managing GitHub Copilot CLI Plugins

This project is a practical guide to finding, installing, disabling, updating,
and uninstalling plugins with GitHub Copilot CLI.

The examples use the `gaming-xpci-marketplace` marketplace and its
`zen-agents` and `nexus-hub` plugins. Replace these names with the marketplace
and plugin you want to manage.

## Prerequisites

- GitHub Copilot CLI is installed.
- You are signed in to GitHub Copilot CLI.
- You have access to the GitHub repository that hosts the marketplace.

Verify the CLI:

```powershell
copilot --version
copilot plugin --help
```

## Two Ways to Run Plugin Commands

### From PowerShell or another terminal

Prefix commands with `copilot`:

```powershell
copilot plugin list
```

### Inside an interactive Copilot CLI session

Start the session:

```powershell
copilot
```

Then use slash commands:

```text
/plugin list
```

Do not enter an interactive slash command directly at a normal PowerShell
prompt. Use the corresponding `copilot plugin ...` command instead.

## 1. Register a Marketplace

A marketplace is a catalog of plugins. Register a GitHub-hosted marketplace
with its `OWNER/REPOSITORY` value.

Terminal:

```powershell
copilot plugin marketplace add gaming-microsoft/Gaming.XPCi.Workspace
```

Interactive Copilot CLI:

```text
/plugin marketplace add gaming-microsoft/Gaming.XPCi.Workspace
```

The repository's marketplace manifest determines its marketplace name. The
name used by subsequent commands can therefore differ from `OWNER/REPOSITORY`.

List registered marketplaces:

```powershell
copilot plugin marketplace list
```

Browse a marketplace:

```powershell
copilot plugin marketplace browse gaming-xpci-marketplace
```

## 2. Install a Plugin

### Install from a registered marketplace

Use the format `PLUGIN-NAME@MARKETPLACE-NAME`.

Terminal:

```powershell
copilot plugin install zen-agents@gaming-xpci-marketplace
```

Interactive Copilot CLI:

```text
/plugin install zen-agents@gaming-xpci-marketplace
```

Verify the installation:

```powershell
copilot plugin list
```

Installed plugin files are normally managed under the Copilot user data
directory. Do not manually edit or delete those installed copies; use the CLI
commands so Copilot can keep its registration and cache state consistent.

### Install directly from GitHub

A plugin does not have to belong to a registered marketplace. Install one
directly from a GitHub repository:

```powershell
copilot plugin install OWNER/REPOSITORY
```

If the plugin is in a repository subdirectory:

```powershell
copilot plugin install OWNER/REPOSITORY:path/to/plugin
```

You can also provide a Git URL:

```powershell
copilot plugin install https://github.com/OWNER/REPOSITORY.git
```

Run `copilot plugin list` after installation and copy the displayed plugin name
or source identifier. Use that value for later update, disable, enable, and
uninstall commands.

## 3. Disable or Enable a Plugin

Disable a plugin when you want to stop loading it without removing it:

```powershell
copilot plugin disable zen-agents@gaming-xpci-marketplace
```

Enable it again:

```powershell
copilot plugin enable zen-agents@gaming-xpci-marketplace
```

Use disable/enable when troubleshooting conflicts or when a plugin is needed
only occasionally.

## 4. Update Plugins and Marketplaces

Refresh one marketplace catalog:

```powershell
copilot plugin marketplace update gaming-xpci-marketplace
```

Update one installed plugin:

```powershell
copilot plugin update zen-agents@gaming-xpci-marketplace
```

Review the installed CLI's help if the available update options differ:

```powershell
copilot plugin update --help
```

## 5. Uninstall a Plugin

Terminal:

```powershell
copilot plugin uninstall nexus-hub@gaming-xpci-marketplace
```

Interactive Copilot CLI:

```text
/plugin uninstall nexus-hub@gaming-xpci-marketplace
```

A successful command reports that the plugin was uninstalled successfully.

To remove both copies of a plugin installed from different marketplaces, run a
separate uninstall command for each qualified name:

```powershell
copilot plugin uninstall zen-agents@gaming-xpci-marketplace
copilot plugin uninstall zen-agents@agency-playground
```

For a directly installed plugin, use the name or source identifier shown by
`copilot plugin list`:

```powershell
copilot plugin uninstall PLUGIN-NAME
```

Verify:

```powershell
copilot plugin list
```

Uninstalling a plugin removes the agents, skills, hooks, and plugin-provided
tool registrations bundled with that installed plugin. It does not remove MCP
servers configured separately in a user or workspace MCP configuration file.

## 6. Remove a Marketplace

Remove a marketplace only after uninstalling the plugins installed from it:

```powershell
copilot plugin marketplace remove agency-playground
```

If plugins from the marketplace are still installed, removal fails and lists
the blockers. The CLI supports forced removal, but it also uninstalls plugins
from that marketplace:

```powershell
copilot plugin marketplace remove agency-playground --force
```

Prefer explicit plugin uninstallation before marketplace removal so you can
review exactly what is being removed.

## Complete Example

```powershell
# Register the marketplace.
copilot plugin marketplace add gaming-microsoft/Gaming.XPCi.Workspace

# Confirm its registered name and browse it.
copilot plugin marketplace list
copilot plugin marketplace browse gaming-xpci-marketplace

# Install and verify a plugin.
copilot plugin install zen-agents@gaming-xpci-marketplace
copilot plugin list

# Temporarily disable and re-enable it.
copilot plugin disable zen-agents@gaming-xpci-marketplace
copilot plugin enable zen-agents@gaming-xpci-marketplace

# Remove it and verify.
copilot plugin uninstall zen-agents@gaming-xpci-marketplace
copilot plugin list

# Optionally unregister the marketplace when no longer needed.
copilot plugin marketplace remove gaming-xpci-marketplace
```

## Troubleshooting

### Plugin appears more than once

The same plugin may be installed from multiple marketplaces. List installed
plugins and uninstall each qualified entry separately:

```powershell
copilot plugin list
copilot plugin uninstall zen-agents@gaming-xpci-marketplace
copilot plugin uninstall zen-agents@agency-playground
```

### Plugin-contributed MCP servers still appear in VS Code

1. Confirm every copy of the plugin is uninstalled.
2. In VS Code, run **Developer: Reload Window**.
3. Run **MCP: List Servers** again.
4. Check user and workspace MCP configuration files for separately configured
   servers with the same names.

### Marketplace removal fails

Uninstall the plugins named in the error, then rerun the marketplace removal.
Use `--force` only when you intentionally want to remove the marketplace and
all plugins installed from it.

### Command syntax is rejected

The CLI evolves. Treat local help as the authority for the installed version:

```powershell
copilot --version
copilot plugin --help
copilot plugin install --help
copilot plugin uninstall --help
copilot plugin marketplace --help
```

Also make sure you are not mixing terminal syntax with interactive syntax:

```text
Terminal:     copilot plugin list
Interactive:  /plugin list
```

## Official References

- [GitHub Copilot CLI plugin reference](https://docs.github.com/copilot/reference/copilot-cli-reference/cli-plugin-reference)
- [Finding and installing plugins for GitHub Copilot CLI](https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot/plugins-finding-installing)
- [About GitHub Copilot CLI plugins](https://docs.github.com/copilot/concepts/agents/copilot-cli/about-cli-plugins)

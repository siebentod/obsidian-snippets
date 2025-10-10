This Templater script finds all notes in a Vault, whose 'type' field includes 'denken', builds a JSON index of their in/out links, and then REPLACES the repository’s main branch with those notes and the index.

To use it, you need to create a .git directory in your vault folder and link it to a remote repository.

Put it here because it seems easy to customize for your needs.

Works with inline fields.

```
<%*
try {
    // Check for Dataview plugin
    if (!app.plugins.plugins.dataview) {
        new Notice('Dataview plugin not found!', 5000);
        return;
    }

    const dv = app.plugins.plugins.dataview.api;
    
    // Get all files with type 'denken'
    console.log('Searching for files with type denken for forced synchronization...');
    const denkenFiles = dv.pages()
        .where(p => p.type && p.type.toString().includes('denken'))
        .array();

    if (denkenFiles.length === 0) {
        new Notice('No files with type denken found');
        return;
    }

    console.log(`Found ${denkenFiles.length} denken files`);
    new Notice(`FULL REPLACEMENT of repository with ${denkenFiles.length} denken files`);
    
    const filePaths = denkenFiles.map(page => page.file.path);

   // --- Index generation ---
const fs = require('fs');
const path = require('path');

const safeName = (link) => {
    // Dataview link can be an object with .path and .name
    if (!link) return null;
    if (typeof link === 'string') return link;
    if (link.path) return link.path.split('/').pop().replace(/\.md$/, '');
    if (link.name) return link.name;
    return String(link);
};

const index = denkenFiles.map(page => ({
    name: page.file.name,
    inlinks: (page.file.inlinks ? page.file.inlinks.values.map(l => safeName(l)) : []).filter(Boolean),
    outlinks: (page.file.outlinks ? page.file.outlinks.values.map(l => safeName(l)) : []).filter(Boolean)
}));

const indexFilePath = path.join(app.vault.adapter.basePath, 'links-index.json');
fs.writeFileSync(indexFilePath, JSON.stringify(index, null, 2), 'utf-8');

// Add index to commit
filePaths.push('links-index.json');

    const { spawn } = require('child_process');
    
    // Function for executing commands
    const runCommand = (command, args = []) => {
        return new Promise((resolve, reject) => {
            console.log(`Running: ${command} ${args.join(' ')}`);
            const process = spawn(command, args, { 
                cwd: app.vault.adapter.basePath,
                shell: false
            });
            
            let stdout = '';
            let stderr = '';
            
            process.stdout.on('data', (data) => stdout += data.toString());
            process.stderr.on('data', (data) => stderr += data.toString());
            
            process.on('close', (code) => {
                console.log(`Command finished with code: ${code}`);
                if (stderr) console.log(`stderr: ${stderr}`);
                
                if (code === 0) {
                    resolve({ stdout, stderr, code });
                } else {
                    reject(new Error(`Git command failed (code ${code}): ${stderr}`));
                }
            });
        });
    };

    // Step 1: Create a new orphan branch (completely clean history)
    console.log('Creating new orphan branch...');
    new Notice('Creating a clean branch...');
    await runCommand('git', ['checkout', '--orphan', 'temp-clean-denken']);
    
    // Step 2: Clear staging area (without touching local files)
    console.log('Clearing staging area...');
    await runCommand('git', ['reset']);
    
    // Step 3: Add only denken files in batches
    console.log('Adding only denken files...');
    new Notice('Adding only denken files...');
    
    const batchSize = 50;
    for (let i = 0; i < filePaths.length; i += batchSize) {
        const batch = filePaths.slice(i, i + batchSize);
        console.log(`Adding batch ${Math.floor(i/batchSize) + 1}/${Math.ceil(filePaths.length/batchSize)}: ${batch.length} files`);
        
        const addArgs = ['add'].concat(batch);
        await runCommand('git', addArgs);
        
        // 50% notification
        const progress = (i + batchSize) / filePaths.length;
        if (progress >= 0.5 && progress < 0.5 + (batchSize / filePaths.length)) {
            new Notice(`50% of denken files added (${Math.min(i + batchSize, filePaths.length)} of ${filePaths.length})`);
        }
    }
    
    // Step 4: Commit new clean history
    console.log('Creating first commit in clean history...');
    const commitMessage = `Clean repository: ${filePaths.length} denken files only (with index) - ${new Date().toISOString()}`;
    await runCommand('git', ['commit', '-m', commitMessage]);
    
    // Step 5: Delete old main branch and rename the new one
    console.log('Replacing main branch...');
    new Notice('Replacing main branch...');
    await runCommand('git', ['branch', '-D', 'main']);
    await runCommand('git', ['branch', '-m', 'main']);
    
    // Step 6: Force-push new main branch
    console.log('Force-replacing repository...');
    new Notice('Force-replacing entire repository...');
    await runCommand('git', ['push', '--force', 'origin', 'main']);

    // Success!
    new Notice(`✅ Repository fully replaced! Contains ${denkenFiles.length} denken files + index`);
    console.log('Forced repository replacement completed successfully');

} catch (error) {
    console.error('Forced replacement error:', error);
    new Notice(`Error: ${error.message}`, 8000);
    
    // In case of error, try to return to main branch
    try {
        const { spawn } = require('child_process');
        const process = spawn('git', ['checkout', 'main'], { 
            cwd: app.vault.adapter.basePath 
        });
        console.log('Attempting to return to main branch...');
    } catch (recoveryError) {
        console.error('Recovery error:', recoveryError);
    }
}
%>
```

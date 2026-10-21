# Read-me-for-Filmcraft-Projects


# -------------------------------------------------------------------------------- STARTING / MAIN MESSAGE --------------------------------------------------------------------------------
```
IGNORE EVERYTHING CURRENTLY IN THE ROOT OF YOUR PROJECT!
Project_Name: "filmcraft-fork"
Repository:  "https://github.com/storytold/filmcraft"

We will instead focus on a different project, Copy this repo into the root of your project inside of a folder named "[Project_Name]", We will focus on this project And this folder ONLY, Not anything else in your root. DON'T Recreate anything from the github repo And say "Oh yeah I copied it", NO! You have to ACTUALLY Copy the repo! (Except for the engines, You know Like Bun, PyCache, Etc etc)

Rules [
VERY IMPORTANT RULES, (THE ONES IN 0): [
0: READ https://github.com/cobyfromhtc/filmcraft/blob/main/AGENTS.md FIRST, DO NOT IGNORE THIS!
0.1: When implementing tasks, bug fixes, features, etc, Do NOT Run those new changes through tests, Tell me what needs testing and I will test them to make sure they work. Steer clear of anything test-related entirely.]
1. Follow only the explicitly requested tasks. Do not make unrelated changes or additions + ONLY Research what you are Working on, Anything else is useless unless told to work/research/fix other contents.
2. Actually clone and copy the repository into `[Project_Name]/`. Never recreate its contents manually or claim it was copied without verifying that the real repository was obtained successfully.
3. Strictly isolate all work to `[Project_Name]/`. Ignore all other root-level files and folders, and never modify anything outside `[Project_Name]/`.
4. Write clean, complete, functional, production-quality code (Code (& Bug Fixing) quality focus should be all the way up). No placeholders, fake implementations, or unfinished features.
5. Maintain the highest possible code quality, including UI/UX. Follow the existing project's conventions and make implementations look professionally handcrafted rather than generic or AI-generated.
6. Fix underlying bugs when implementing requested changes. Check for regressions and verify changes with appropriate tests, linting, type checks, and builds when available.
8. Inspect existing code before editing, review changes afterward, and preserve existing functionality and user modifications.
9. Never overwrite or delete existing work without authorization. If `[Project_Name]/` already exists, inspect it before proceeding.
10. Never fabricate successful operations, If blocked, explain the issue honestly.
11. After fixing issues/bugs, Explain what was happening REALLY Simply.

Tasks To-Do [
No Panel Customization: Unlike Adobe Premiere, which allows users to drag, dock, and completely re-arrange their workspace, FilmCraft's interface panels are completely locked in place. [1]

Laggy Playhead Scrubbing: If you have the "Effect Controls" panel open, dragging the playhead across the timeline becomes incredibly laggy and unresponsive. [2]

Broken Mouse / Trimming Controls: Traditional multi-tool timeline editing is still unpolished. Reviewers noted that basic actions—like scrolling on the playback window to zoom in/out or hovering to click-and-drag a clip's outer edge to trim it—frequently fail to respond. [3]

Issues that are already in our project that could be fixed:
Playback scheduling changes can over-queue rendering at high shuttle speeds and keep requesting every frame when workers cannot keep up, so the monitor may show stale frames. The advertised background import is not connected to the app, so large imports still block. Several smaller interaction and documentation issues also remain. [1]

When resizing the Project Panel size, These Buttons Collapse onto each other when resize too small (Veritcal Attachment) [2]

QoL Bugs:
Dragging files directly onto the timeline often fails.
The Delete key does not ripple-snap clips backward, forcing manual adjustments.
The text tool gets "stuck"—if you try to use a shortcut or type in the effect search bar while a text layer is selected, it keeps typing into the video instead.]

When All tasks are finished, Can you put the Project folder in a.zip? Then put the .zip in the "Public" folder in the root of your Project Directory?
```
# -------------------------------------------------------------------------------- BUILDING AND RUNNING THE PROJECT --------------------------------------------------------------------------------
```
winget install --id Rustlang.Rustup -e
cargo --version
cargo run --release -p filmcraft
```

# -------------------------------------------------------------------------------- PUSH NEW CHANGESTO REPO --------------------------------------------------------------------------------
```
# ============================================================
#  Push-Changes.ps1
#  Commits everything in your local folder and pushes it to
#  your GitHub repo.
# ============================================================

# ---------------- EDIT THESE ----------------
$RepoPath      = "C:\Users\User\Downloads\FilmCraft\filmcraft-fork"
$Branch        = "Drag-and-drop-folders-in-project-media"      # <-- change this when you need a different branch
$RemoteUrl     = "https://github.com/cobyfromhtc/filmcraft.git"
$CommitMessage = "Update $(Get-Date -Format 'yyyy-MM-dd HH:mm')"
$ForcePush     = $false      # set to $true only if you want to overwrite the remote branch
# --------------------------------------------

$ErrorActionPreference = "Stop"

function Fail($msg) {
    Write-Host "ERROR: $msg" -ForegroundColor Red
    exit 1
}

if (-not (Get-Command git -ErrorAction SilentlyContinue)) {
    Fail "Git is not installed or not on PATH."
}

if (-not (Test-Path $RepoPath)) {
    Fail "Folder not found: $RepoPath"
}

Set-Location $RepoPath

if (-not (Test-Path (Join-Path $RepoPath ".git"))) {
    Write-Host "No git repo found here, initializing one..." -ForegroundColor Yellow
    git init
    if ($LASTEXITCODE -ne 0) { Fail "git init failed." }
}

# Point 'origin' at your repo (add it if missing, update it if it exists)
$remotes = git remote
if ($remotes -contains "origin") {
    git remote set-url origin $RemoteUrl
} else {
    git remote add origin $RemoteUrl
}
if ($LASTEXITCODE -ne 0) { Fail "Could not set the remote URL." }

# Switch to the branch, creating it if it doesn't exist yet
git show-ref --verify --quiet "refs/heads/$Branch"
if ($LASTEXITCODE -eq 0) {
    git checkout $Branch
} else {
    git checkout -b $Branch
}
if ($LASTEXITCODE -ne 0) { Fail "Could not switch to branch '$Branch'." }

# Stage and commit any changes
$changes = git status --porcelain
if ($changes) {
    git add -A
    git commit -m $CommitMessage
    if ($LASTEXITCODE -ne 0) { Fail "Commit failed." }
    Write-Host "Committed: $CommitMessage" -ForegroundColor Green
} else {
    Write-Host "No new changes to commit. Pushing any existing commits..." -ForegroundColor Yellow
}

# Push
if ($ForcePush) {
    git push -u origin $Branch --force
} else {
    git push -u origin $Branch
}

if ($LASTEXITCODE -ne 0) {
    Write-Host ""
    Write-Host "Push failed." -ForegroundColor Red
    Write-Host "If it says 'fetch first' or 'rejected', run:  git pull origin $Branch --rebase" -ForegroundColor Yellow
    Write-Host "Or set `$ForcePush = `$true at the top of this script to overwrite the remote branch." -ForegroundColor Yellow
    exit 1
}

Write-Host ""
Write-Host "Done. Pushed branch '$Branch' to $RemoteUrl" -ForegroundColor Green
```
# -------------------------------------------------------------------------------- CREATE PULL REQUEST (EDIT BRANCH NEEDED) --------------------------------------------------------------------------------
```
# ============================================================
#  Create-PR.ps1
#  Opens a pull request from your fork's branch into the
#  original (upstream) repo.
# ============================================================

# ===================== EDIT THESE ===========================
$RepoPath   = "C:\Users\User\Downloads\FilmCraft\filmcraft-fork"
$Branch     = "Drag-and-drop-folders-in-project-media"                  # <-- CHANGE THIS: your fork's branch to open the PR from
$BaseBranch = "main"                  # branch on the original repo the PR merges into
$Upstream   = "storytold/filmcraft"   # original repo (where the PR is opened)
$ForkOwner  = "cobyfromhtc"           # your GitHub username
$Title      = "motion blur functionality (keyframe-aware)"
$Body       = "motion blur functionality (keyframe-aware)"
# ============================================================

$ErrorActionPreference = "Stop"
Set-Location $RepoPath

$head = "${ForkOwner}:${Branch}"

if (Get-Command gh -ErrorAction SilentlyContinue) {
    gh pr create --repo $Upstream --base $BaseBranch --head $head --title $Title --body $Body
    if ($LASTEXITCODE -ne 0) {
        Write-Warning "PR creation failed or a PR already exists for $head. See the message above."
    }
} else {
    Write-Warning "GitHub CLI (gh) not found. Opening the PR form in your browser instead."
    $url = "https://github.com/$Upstream/compare/${BaseBranch}...${head}?expand=1" +
           "&title=" + [uri]::EscapeDataString($Title) +
           "&body="  + [uri]::EscapeDataString($Body)
    Start-Process $url
}
```



# -------------------------------------------------------------------------------- UPDATE AN EXISTING PR'S TITLE & BODY TEXT ----------------------------------------------------------------------------
```
$Title = "Drag and drop folders in project media"
$Body  = "Adds drag-and-drop support for folders in the project media panel."

gh pr edit 2632 --repo storytold/filmcraft --title $Title --body $Body
```

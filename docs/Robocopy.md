# Robocopy Command Builder

Build a Robocopy command interactively.  
Nothing is executed — this only generates a command you can copy and run manually.

---

## Paths (Required)

<div class="robocopy-builder">

<label class="important">
  Source Path
  <input type="text" id="source" placeholder="C:\Source">
</label>

<label class="important">
  Destination Path
  <input type="text" id="destination" placeholder="\\SERVER\Share\Destination">
</label>

</div>

---

## Presets

<button class="md-button" onclick="presetMirror()">Mirror Folder</button>
<button class="md-button" onclick="presetBackup()">Backup Copy</button>
<button class="md-button" onclick="presetLogOnly()">Test / Log Only</button>

---

## Options

<div class="robocopy-builder">

<div class="group">
  <label>
    <input type="checkbox" id="mirror">
    Mirror (/MIR)
  </label>

  <label class="indent">
    <input type="checkbox" id="purge">
    Purge destination (/PURGE)
  </label>
</div>

<label>
  <input type="checkbox" id="subdirs">
  Include subdirectories (/E)
</label>

<label>
  <input type="checkbox" id="restartable">
  Restartable mode (/Z)
</label>

<label>
  <input type="checkbox" id="backupmode">
  Backup mode (/B)
</label>

<label>
  <input type="checkbox" id="security">
  Copy NTFS permissions (/COPYALL)
</label>

<label>
  <input type="checkbox" id="timestamps">
  Preserve timestamps (/DCOPY:T)
</label>

<label>
  Retry count (/R)
  <input type="number" id="retries" placeholder="3">
</label>

<label>
  Retry wait seconds (/W)
  <input type="number" id="wait" placeholder="5">
</label>

<label>
  Threads (/MT)
  <input type="number" id="threads" placeholder="16">
</label>

<label>
  Exclude files (/XF)
  <input type="text" id="excludefiles" placeholder="*.tmp *.log">
</label>

<label>
  Exclude directories (/XD)
  <input type="text" id="excludedirs" placeholder="Temp Cache">
</label>

<label>
  <input type="checkbox" id="nolog">
  No logging (/NFL /NDL)
</label>

<label>
  <input type="checkbox" id="logfile">
  Write log file (/LOG)
</label>

</div>

---

## Validation

<div id="validation" class="validation"></div>

---

## Generated Command

<textarea id="preview" rows="4" style="width:100%; font-family: monospace;" readonly></textarea>

<button class="md-button md-button--primary" onclick="copyCommand()">
  Copy to Clipboard
</button>

---

<script>
function q(value) {
  if (!value) return null;
  if (value.includes(" ")) return `"${value}"`;
  return value;
}

function validate() {
  const warnings = [];

  if (!source.value) warnings.push("Source path is required.");
  if (!destination.value) warnings.push("Destination path is required.");

  if (mirror.checked && !purge.checked) {
    warnings.push("Mirror usually implies purge (deleted files will be removed).");
  }

  if (threads.value && threads.value > 64) {
    warnings.push("High thread counts can cause network or disk saturation.");
  }

  validation.innerHTML = warnings.length
    ? "<ul><li>" + warnings.join("</li><li>") + "</li></ul>"
    : "<span class='ok'>No issues detected.</span>";
}

function updateCommand() {
  const parts = [];
  parts.push("robocopy");

  parts.push(q(source.value) || "<source>");
  parts.push(q(destination.value) || "<destination>");

  if (mirror.checked) parts.push("/MIR");
  if (purge.checked) parts.push("/PURGE");
  if (subdirs.checked) parts.push("/E");
  if (restartable.checked) parts.push("/Z");
  if (backupmode.checked) parts.push("/B");
  if (security.checked) parts.push("/COPYALL");
  if (timestamps.checked) parts.push("/DCOPY:T");

  if (retries.value) parts.push(`/R:${retries.value}`);
  if (wait.value) parts.push(`/W:${wait.value}`);
  if (threads.value) parts.push(`/MT:${threads.value}`);

  if (excludefiles.value) parts.push(`/XF ${excludefiles.value}`);
  if (excludedirs.value) parts.push(`/XD ${excludedirs.value}`);

  if (nolog.checked) parts.push("/NFL /NDL");
  if (logfile.checked) parts.push(`/LOG:%WINDIR%\\Temp\\robocopy.log`);

  preview.value = parts.filter(Boolean).join(" ");
  validate();
}

function copyCommand() {
  navigator.clipboard.writeText(preview.value);
}

/* Presets */

function presetMirror() {
  mirror.checked = true;
  purge.checked = true;
  subdirs.checked = true;
  restartable.checked = true;
  retries.value = 3;
  wait.value = 5;
  threads.value = 16;
  logfile.checked = true;
  updateCommand();
}

function presetBackup() {
  mirror.checked = false;
  purge.checked = false;
  subdirs.checked = true;
  restartable.checked = true;
  security.checked = true;
  timestamps.checked = true;
  retries.value = 3;
  wait.value = 5;
  logfile.checked = true;
  updateCommand();
}

function presetLogOnly() {
  logfile.checked = true;
  nolog.checked = false;
  updateCommand();
}

/* Wiring */

document.querySelectorAll(".robocopy-builder input").forEach(el => {
  el.addEventListener("input", updateCommand);
});

updateCommand();
</script>

<style>
.robocopy-builder {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(260px, 1fr));
  gap: 0.75rem;
}

.robocopy-builder label {
  display: flex;
  flex-direction: column;
  font-size: 0.9rem;
}

.group {
  border-left: 3px solid var(--md-default-fg-color--lightest);
  padding-left: 0.75rem;
}

.indent {
  margin-left: 1.5rem;
  opacity: 0.9;
}

.important input {
  font-size: 1rem;
  font-weight: 600;
}

.validation {
  background: var(--md-default-bg-color--lighter);
  border-left: 4px solid #ffa000;
  padding: 0.5rem 0.75rem;
  font-size: 0.85rem;
}

.validation .ok {
  color: #2e7d32;
}
</style>

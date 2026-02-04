# Robocopy Command Builder

Build a Robocopy command safely and quickly. This page only generates a command string — nothing is executed automatically.

---

## Quick Build

<div class="robocopy-grid">
  <label class="important">
    Source Path
    <input type="text" id="source" placeholder="C:\Source">
  </label>

  <label class="important">
    Destination Path
    <input type="text" id="destination" placeholder="\\SERVER\Share\Destination">
  </label>
</div>

<div class="robocopy-actions">
  <button class="md-button md-button--primary" onclick="presetMirror()">Mirror Folder</button>
  <button class="md-button" onclick="presetBackup()">Backup Copy</button>
  <button class="md-button" onclick="presetLogOnly()">Test / Log Only</button>
  <button class="md-button" onclick="resetAll()">Reset</button>
</div>

---

## Options

<div class="robocopy-grid">
  <label class="robocopy-option">
    <input type="checkbox" id="mirror">
    <span class="option-text">Mirror directory tree</span>
    <span class="option-flag">/MIR</span>
  </label>

  <label class="robocopy-option">
    <input type="checkbox" id="purge">
    <span class="option-text">Purge destination</span>
    <span class="option-flag">/PURGE</span>
  </label>

  <label class="robocopy-option">
    <input type="checkbox" id="subdirs">
    <span class="option-text">Include subdirectories</span>
    <span class="option-flag">/E</span>
  </label>

  <label class="robocopy-option">
    <input type="checkbox" id="restartable">
    <span class="option-text">Restartable mode</span>
    <span class="option-flag">/Z</span>
  </label>

  <label class="robocopy-option">
    <input type="checkbox" id="backupmode">
    <span class="option-text">Backup mode</span>
    <span class="option-flag">/B</span>
  </label>

  <label class="robocopy-option">
    <input type="checkbox" id="security">
    <span class="option-text">Copy all NTFS info</span>
    <span class="option-flag">/COPYALL</span>
  </label>

  <label class="robocopy-option">
    <input type="checkbox" id="timestamps">
    <span class="option-text">Preserve directory timestamps</span>
    <span class="option-flag">/DCOPY:T</span>
  </label>

  <label class="robocopy-option">
    <input type="checkbox" id="listonly">
    <span class="option-text">List only (no copy)</span>
    <span class="option-flag">/L</span>
  </label>

  <label class="robocopy-option">
    <input type="checkbox" id="nflndl">
    <span class="option-text">No file/dir listing</span>
    <span class="option-flag">/NFL /NDL</span>
  </label>
</div>

---

## Advanced (Optional)

<details id="advanced">
  <summary>Show advanced options</summary>

  <div class="robocopy-grid robocopy-advanced">
    <label>
      Retry count (/R)
      <input type="number" id="retries" placeholder="3" min="0">
    </label>

    <label>
      Retry wait seconds (/W)
      <input type="number" id="wait" placeholder="5" min="0">
    </label>

    <label>
      Threads (/MT)
      <input type="number" id="threads" placeholder="16" min="1">
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
      Log path (/LOG)
      <input type="text" id="logpath" placeholder="%WINDIR%\\Temp\\robocopy.log">
    </label>

    <label class="robocopy-option robocopy-option--inline">
      <input type="checkbox" id="logfile">
      <span class="option-text">Write log file</span>
      <span class="option-flag">/LOG</span>
    </label>

    <label class="robocopy-option robocopy-option--inline">
      <input type="checkbox" id="excludejunctions">
      <span class="option-text">Exclude junctions</span>
      <span class="option-flag">/XJ</span>
    </label>
  </div>
</details>

---

## Validation

<div id="validation" class="validation"></div>

---

## Generated Command

<textarea id="preview" rows="4" readonly></textarea>

<div class="robocopy-actions">
  <button class="md-button md-button--primary" onclick="copyCommand()">Copy to Clipboard</button>
  <span id="copyStatus" class="robocopy-status" aria-live="polite"></span>
</div>

---

<script>
const controls = {
  source: document.getElementById("source"),
  destination: document.getElementById("destination"),
  mirror: document.getElementById("mirror"),
  purge: document.getElementById("purge"),
  subdirs: document.getElementById("subdirs"),
  restartable: document.getElementById("restartable"),
  backupmode: document.getElementById("backupmode"),
  security: document.getElementById("security"),
  timestamps: document.getElementById("timestamps"),
  listonly: document.getElementById("listonly"),
  nflndl: document.getElementById("nflndl"),
  retries: document.getElementById("retries"),
  wait: document.getElementById("wait"),
  threads: document.getElementById("threads"),
  excludefiles: document.getElementById("excludefiles"),
  excludedirs: document.getElementById("excludedirs"),
  logpath: document.getElementById("logpath"),
  logfile: document.getElementById("logfile"),
  excludejunctions: document.getElementById("excludejunctions"),
  validation: document.getElementById("validation"),
  preview: document.getElementById("preview"),
  copyStatus: document.getElementById("copyStatus"),
};

function formatToken(value) {
  if (!value) return null;
  const escaped = value.replace(/"/g, '""');
  if (/[\s&|<>]/.test(escaped)) {
    return `"${escaped}"`;
  }
  return escaped;
}

function getValue(input) {
  return input ? input.value.trim() : "";
}

function resetAll() {
  document.querySelectorAll("#advanced input").forEach(el => {
    el.value = "";
  });

  document.querySelectorAll("input[type=checkbox]").forEach(c => {
    c.checked = false;
  });

  syncMirrorOptions();
  updateCommand();
}

function syncMirrorOptions() {
  const enabled = controls.mirror.checked;
  controls.purge.disabled = enabled;
  controls.subdirs.disabled = enabled;

  if (enabled) {
    controls.purge.checked = true;
    controls.subdirs.checked = true;
  }
}

function validate() {
  const warnings = [];

  if (!getValue(controls.source)) warnings.push("Source path is required.");
  if (!getValue(controls.destination)) warnings.push("Destination path is required.");

  if (controls.mirror.checked) {
    warnings.push("Mirror includes /E and /PURGE. Use with caution.");
  }

  if (controls.threads.value && Number(controls.threads.value) > 64) {
    warnings.push("High thread counts can cause network or disk saturation.");
  }

  controls.validation.innerHTML = warnings.length
    ? `<ul><li>${warnings.join("</li><li>")}</li></ul>`
    : "<span class='ok'>No issues detected.</span>";
}

function updateCommand() {
  const parts = ["robocopy"];

  parts.push(formatToken(getValue(controls.source)) || "<source>");
  parts.push(formatToken(getValue(controls.destination)) || "<destination>");

  if (controls.mirror.checked) parts.push("/MIR");
  if (!controls.mirror.checked && controls.purge.checked) parts.push("/PURGE");
  if (!controls.mirror.checked && controls.subdirs.checked) parts.push("/E");
  if (controls.restartable.checked) parts.push("/Z");
  if (controls.backupmode.checked) parts.push("/B");
  if (controls.security.checked) parts.push("/COPYALL");
  if (controls.timestamps.checked) parts.push("/DCOPY:T");
  if (controls.listonly.checked) parts.push("/L");
  if (controls.nflndl.checked) parts.push("/NFL /NDL");
  if (controls.excludejunctions.checked) parts.push("/XJ");

  const retries = getValue(controls.retries);
  if (retries) parts.push(`/R:${retries}`);

  const wait = getValue(controls.wait);
  if (wait) parts.push(`/W:${wait}`);

  const threads = getValue(controls.threads);
  if (threads) parts.push(`/MT:${threads}`);

  const excludeFiles = getValue(controls.excludefiles);
  if (excludeFiles) parts.push(`/XF ${excludeFiles}`);

  const excludeDirs = getValue(controls.excludedirs);
  if (excludeDirs) parts.push(`/XD ${excludeDirs}`);

  if (controls.logfile.checked) {
    const logPath = getValue(controls.logpath) || "%WINDIR%\\Temp\\robocopy.log";
    parts.push(`/LOG:${logPath}`);
  }

  controls.preview.value = parts.filter(Boolean).join(" ");
  validate();
}

function presetMirror() {
  resetAll();
  controls.mirror.checked = true;
  controls.restartable.checked = true;
  controls.retries.value = 3;
  controls.wait.value = 5;
  controls.threads.value = 16;
  controls.logfile.checked = true;
  syncMirrorOptions();
  updateCommand();
}

function presetBackup() {
  resetAll();
  controls.subdirs.checked = true;
  controls.restartable.checked = true;
  controls.backupmode.checked = true;
  controls.security.checked = true;
  controls.timestamps.checked = true;
  controls.retries.value = 3;
  controls.wait.value = 5;
  controls.logfile.checked = true;
  syncMirrorOptions();
  updateCommand();
}

function presetLogOnly() {
  resetAll();
  controls.listonly.checked = true;
  controls.logfile.checked = true;
  syncMirrorOptions();
  updateCommand();
}

async function copyCommand() {
  const command = controls.preview.value;
  if (!command) return;

  try {
    await navigator.clipboard.writeText(command);
    controls.copyStatus.textContent = "Copied!";
  } catch (error) {
    controls.preview.select();
    document.execCommand("copy");
    controls.copyStatus.textContent = "Copied (fallback).";
  }

  setTimeout(() => {
    controls.copyStatus.textContent = "";
  }, 2000);
}

document.querySelectorAll("input").forEach(el => {
  el.addEventListener("input", () => {
    if (el === controls.mirror) syncMirrorOptions();
    updateCommand();
  });
  el.addEventListener("change", () => {
    if (el === controls.mirror) syncMirrorOptions();
    updateCommand();
  });
});

syncMirrorOptions();
updateCommand();
</script>

<style>
.robocopy-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(240px, 1fr));
  gap: 0.75rem;
}

.robocopy-grid label {
  display: flex;
  flex-direction: column;
  font-size: 0.9rem;
  gap: 0.35rem;
}

.robocopy-actions {
  display: flex;
  flex-wrap: wrap;
  gap: 0.5rem;
  align-items: center;
}

.robocopy-option {
  flex-direction: row;
  align-items: center;
  gap: 0.5rem;
  border: 1px solid var(--md-default-fg-color--lightest, #e0e0e0);
  border-radius: 0.5rem;
  padding: 0.5rem 0.75rem;
  background: var(--md-default-bg-color, #fff);
}

.robocopy-option--inline {
  margin-top: 0.5rem;
}

.robocopy-option input {
  margin: 0;
}

.robocopy-option .option-text {
  font-weight: 600;
}

.robocopy-option .option-flag {
  margin-left: auto;
  font-family: ui-monospace, SFMono-Regular, Menlo, Consolas, "Liberation Mono", monospace;
  font-size: 0.85rem;
  color: var(--md-default-fg-color--light, #6b6b6b);
  background: var(--md-default-bg-color--lighter, #f6f6f6);
  padding: 0.15rem 0.4rem;
  border-radius: 0.35rem;
}

.robocopy-advanced {
  margin-top: 0.75rem;
}

.important input {
  font-size: 1rem;
  font-weight: 600;
}

#preview {
  width: 100%;
  font-family: ui-monospace, SFMono-Regular, Menlo, Consolas, "Liberation Mono", monospace;
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

.robocopy-status {
  font-size: 0.85rem;
  color: var(--md-default-fg-color--light, #6b6b6b);
}
</style>

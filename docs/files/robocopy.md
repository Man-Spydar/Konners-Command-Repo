# Robocopy Command Builder

Build a Robocopy command safely and quickly. This page only generates a command string — nothing is executed automatically.

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
  document.querySelectorAll("input").forEach(el => {
    if (el.type === "checkbox") {
      el.checked = false;
    } else {
      el.value = "";
    }
  });
  updatePreview();
  updateCopyStatus("");
  controls.validation.textContent = "";
}

function presetMirror() {
  controls.mirror.checked = true;
  controls.purge.checked = true;
  updatePreview();
}

function presetBackup() {
  controls.subdirs.checked = true;
  controls.restartable.checked = true;
  controls.backupmode.checked = true;
  updatePreview();
}

function presetLogOnly() {
  controls.listonly.checked = true;
  controls.logfile.checked = true;
  updatePreview();
}

function updateCopyStatus(message) {
  controls.copyStatus.textContent = message;
}

function addFlag(parts, flag, condition) {
  if (condition) parts.push(flag);
}

function buildCommand() {
  const source = getValue(controls.source);
  const destination = getValue(controls.destination);

  if (!source || !destination) {
    return "";
  }

  const parts = ["robocopy", formatToken(source), formatToken(destination)];

  addFlag(parts, "/MIR", controls.mirror.checked);
  addFlag(parts, "/PURGE", controls.purge.checked);
  addFlag(parts, "/E", controls.subdirs.checked);
  addFlag(parts, "/Z", controls.restartable.checked);
  addFlag(parts, "/B", controls.backupmode.checked);
  addFlag(parts, "/COPYALL", controls.security.checked);
  addFlag(parts, "/DCOPY:T", controls.timestamps.checked);
  addFlag(parts, "/L", controls.listonly.checked);
  addFlag(parts, "/NFL", controls.nflndl.checked);
  addFlag(parts, "/NDL", controls.nflndl.checked);

  const retries = getValue(controls.retries);
  if (retries) parts.push(`/R:${retries}`);

  const wait = getValue(controls.wait);
  if (wait) parts.push(`/W:${wait}`);

  const threads = getValue(controls.threads);
  if (threads) parts.push(`/MT:${threads}`);

  const excludefiles = getValue(controls.excludefiles);
  if (excludefiles) parts.push(`/XF ${excludefiles}`);

  const excludedirs = getValue(controls.excludedirs);
  if (excludedirs) parts.push(`/XD ${excludedirs}`);

  if (controls.logfile.checked) {
    const logpath = getValue(controls.logpath) || "%WINDIR%\\Temp\\robocopy.log";
    parts.push(`/LOG:${logpath}`);
  }

  addFlag(parts, "/XJ", controls.excludejunctions.checked);

  return parts.join(" ");
}

function validateInputs() {
  const source = getValue(controls.source);
  const destination = getValue(controls.destination);

  if (!source || !destination) {
    controls.validation.textContent = "Enter both source and destination paths.";
    return false;
  }

  controls.validation.textContent = "";
  return true;
}

function updatePreview() {
  if (!validateInputs()) {
    controls.preview.value = "";
    return;
  }

  controls.preview.value = buildCommand();
}

function copyCommand() {
  const command = buildCommand();
  if (!command) {
    updateCopyStatus("Enter paths first.");
    return;
  }
  navigator.clipboard.writeText(command).then(() => {
    updateCopyStatus("Copied!");
  }).catch(() => {
    updateCopyStatus("Unable to copy");
  });
}

Object.values(controls).forEach(control => {
  if (!control) return;
  const event = control.tagName === "SELECT" || control.type === "checkbox" ? "change" : "input";
  control.addEventListener(event, updatePreview);
});

resetAll();
</script>

# PsExec Command Builder

Build a PsExec command safely and quickly. This page **only** generates a command string — nothing is executed automatically.

---

## Quick Build

<div class="psexec-grid">
  <label class="important">
    Target Computer
    <input type="text" id="computer" placeholder="COMPUTER01">
  </label>

  <label>
    Remote Command
    <input type="text" id="command" value="cmd.exe">
  </label>

  <label>
    Arguments
    <input type="text" id="arguments" placeholder="/c whoami">
  </label>
</div>

<div class="psexec-actions">
  <button class="md-button md-button--primary" onclick="presetSystemCmd()">SYSTEM + Interactive CMD</button>
  <button class="md-button" onclick="presetBasicCmd()">Basic CMD</button>
  <button class="md-button" onclick="resetAll()">Reset</button>
</div>

---

## Options

<div class="psexec-grid">
  <label class="psexec-option">
    <input type="checkbox" id="accepteula">
    <span class="option-text">Accept EULA</span>
    <span class="option-flag">-accepteula</span>
  </label>

  <label class="psexec-option">
    <input type="checkbox" id="nobanner">
    <span class="option-text">Hide banner</span>
    <span class="option-flag">-nobanner</span>
  </label>

  <label class="psexec-option">
    <input type="checkbox" id="system">
    <span class="option-text">Run as SYSTEM</span>
    <span class="option-flag">-s</span>
  </label>

  <label class="psexec-option">
    <input type="checkbox" id="elevated">
    <span class="option-text">Elevated token</span>
    <span class="option-flag">-h</span>
  </label>

  <label class="psexec-option">
    <input type="checkbox" id="limited">
    <span class="option-text">Limited token</span>
    <span class="option-flag">-l</span>
  </label>

  <label class="psexec-option">
    <input type="checkbox" id="interactive">
    <span class="option-text">Interactive session</span>
    <span class="option-flag">-i</span>
  </label>

  <label class="psexec-option">
    <input type="checkbox" id="dontwait">
    <span class="option-text">Don't wait</span>
    <span class="option-flag">-d</span>
  </label>

  <label class="psexec-option">
    <input type="checkbox" id="copy">
    <span class="option-text">Copy executable</span>
    <span class="option-flag">-c</span>
  </label>

  <label class="psexec-option">
    <input type="checkbox" id="forcecopy">
    <span class="option-text">Force copy</span>
    <span class="option-flag">-f</span>
  </label>

  <label class="psexec-option">
    <input type="checkbox" id="verifycopy">
    <span class="option-text">Verify copy</span>
    <span class="option-flag">-v</span>
  </label>

  <label class="psexec-option">
    <input type="checkbox" id="noprofile">
    <span class="option-text">No profile</span>
    <span class="option-flag">-e</span>
  </label>

  <label class="psexec-option">
    <input type="checkbox" id="noconsole">
    <span class="option-text">No console</span>
    <span class="option-flag">-x</span>
  </label>
</div>

---

## Advanced (Optional)

<details id="advanced">
  <summary>Show advanced options</summary>

  <div class="psexec-grid psexec-advanced">
    <label>
      PsExec path
      <input type="text" id="psexecPath" placeholder="PsExec.exe">
    </label>

    <label>
      Session ID (-i <session>)
      <input type="text" id="session" placeholder="1">
    </label>

    <label>
      Timeout (seconds) (-n)
      <input type="number" id="timeout" placeholder="30" min="1">
    </label>

    <label>
      Processors (-a)
      <input type="text" id="processors" placeholder="0,2-3">
    </label>

    <label>
      Username (-u)
      <input type="text" id="username" placeholder="DOMAIN\\user">
    </label>

    <label>
      Password (-p)
      <input type="password" id="password" placeholder="••••••••">
    </label>

    <label>
      Working directory (-w)
      <input type="text" id="workingDir" placeholder="C:\\Temp">
    </label>

    <label>
      Service name (-r)
      <input type="text" id="service" placeholder="PSEXESVC">
    </label>

    <label>
      Priority (-priority)
      <select id="priority">
        <option value="">Default</option>
        <option value="low">Low</option>
        <option value="belownormal">Below normal</option>
        <option value="normal">Normal</option>
        <option value="abovenormal">Above normal</option>
        <option value="high">High</option>
        <option value="realtime">Realtime</option>
      </select>
    </label>

  </div>
</details>

---

## Generated Command

<textarea id="preview" rows="3" readonly></textarea>

<div class="psexec-actions">
  <button class="md-button md-button--primary" onclick="copyCommand()">Copy to Clipboard</button>
  <span id="copyStatus" class="psexec-status" aria-live="polite"></span>
</div>

---

<script>
const controls = {
  computer: document.getElementById("computer"),
  command: document.getElementById("command"),
  arguments: document.getElementById("arguments"),
  accepteula: document.getElementById("accepteula"),
  nobanner: document.getElementById("nobanner"),
  system: document.getElementById("system"),
  elevated: document.getElementById("elevated"),
  limited: document.getElementById("limited"),
  interactive: document.getElementById("interactive"),
  dontwait: document.getElementById("dontwait"),
  copy: document.getElementById("copy"),
  forcecopy: document.getElementById("forcecopy"),
  verifycopy: document.getElementById("verifycopy"),
  noprofile: document.getElementById("noprofile"),
  noconsole: document.getElementById("noconsole"),
  psexecPath: document.getElementById("psexecPath"),
  session: document.getElementById("session"),
  timeout: document.getElementById("timeout"),
  processors: document.getElementById("processors"),
  username: document.getElementById("username"),
  password: document.getElementById("password"),
  workingDir: document.getElementById("workingDir"),
  service: document.getElementById("service"),
  priority: document.getElementById("priority"),
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
  document.querySelectorAll("#advanced input, #advanced select").forEach(el => {
    if (el.tagName === "SELECT") {
      el.value = "";
    } else {
      el.value = "";
    }
  });

  document.querySelectorAll("input[type=checkbox]").forEach(c => {
    c.checked = false;
  });

  controls.command.value = "cmd.exe";
  controls.arguments.value = "";
  syncCopyOptions();
  build();
}

function syncCopyOptions() {
  const enabled = controls.copy.checked;
  controls.forcecopy.disabled = !enabled;
  controls.verifycopy.disabled = !enabled;
  if (!enabled) {
    controls.forcecopy.checked = false;
    controls.verifycopy.checked = false;
  }
}

function build() {
  const parts = [];
  const path = getValue(controls.psexecPath) || "PsExec.exe";

  parts.push(formatToken(path));

  if (controls.accepteula.checked) parts.push("-accepteula");
  if (controls.nobanner.checked) parts.push("-nobanner");
  if (controls.system.checked) parts.push("-s");
  if (controls.elevated.checked) parts.push("-h");
  if (controls.limited.checked) parts.push("-l");

  const interactive = controls.interactive.checked;
  const session = getValue(controls.session);
  if (interactive || session) {
    parts.push(session ? `-i ${session}` : "-i");
  }

  if (controls.dontwait.checked) parts.push("-d");
  if (controls.copy.checked) parts.push("-c");
  if (controls.forcecopy.checked && controls.copy.checked) parts.push("-f");
  if (controls.verifycopy.checked && controls.copy.checked) parts.push("-v");
  if (controls.noprofile.checked) parts.push("-e");
  if (controls.noconsole.checked) parts.push("-x");

  const timeout = getValue(controls.timeout);
  if (timeout) parts.push(`-n ${timeout}`);

  const processors = getValue(controls.processors);
  if (processors) parts.push(`-a ${processors}`);

  const username = getValue(controls.username);
  if (username) parts.push(`-u ${formatToken(username)}`);

  const password = getValue(controls.password);
  if (password) parts.push(`-p ${formatToken(password)}`);

  const workingDir = getValue(controls.workingDir);
  if (workingDir) parts.push(`-w ${formatToken(workingDir)}`);

  const service = getValue(controls.service);
  if (service) parts.push(`-r ${formatToken(service)}`);

  if (controls.priority.value) parts.push(`-priority ${controls.priority.value}`);

  const computer = getValue(controls.computer) || "<computer>";
  parts.push(computer.startsWith("\\\\") ? computer : `\\\\${computer}`);

  const command = getValue(controls.command) || "cmd.exe";
  parts.push(formatToken(command));

  const args = getValue(controls.arguments);
  if (args) parts.push(args.replace(/\r?\n/g, " "));

  controls.preview.value = parts.filter(Boolean).join(" ");
}

function presetSystemCmd() {
  resetAll();
  controls.accepteula.checked = true;
  controls.system.checked = true;
  controls.interactive.checked = true;
  build();
}

function presetBasicCmd() {
  resetAll();
  controls.accepteula.checked = true;
  build();
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

document.querySelectorAll("input, select").forEach(el => {
  el.addEventListener("input", () => {
    if (el === controls.copy) syncCopyOptions();
    build();
  });
  el.addEventListener("change", () => {
    if (el === controls.copy) syncCopyOptions();
    build();
  });
});

syncCopyOptions();
build();
</script>

<style>
.psexec-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(240px, 1fr));
  gap: 0.75rem;
}

.psexec-grid label {
  display: flex;
  flex-direction: column;
  font-size: 0.9rem;
  gap: 0.35rem;
}

.psexec-option {
  flex-direction: row;
  align-items: center;
  gap: 0.5rem;
  border: 1px solid var(--md-default-fg-color--lightest, #e0e0e0);
  border-radius: 0.5rem;
  padding: 0.5rem 0.75rem;
  background: var(--md-default-bg-color, #fff);
}

.psexec-option input {
  margin: 0;
}

.psexec-option .option-text {
  font-weight: 600;
}

.psexec-option .option-flag {
  margin-left: auto;
  font-family: ui-monospace, SFMono-Regular, Menlo, Consolas, "Liberation Mono", monospace;
  font-size: 0.85rem;
  color: var(--md-default-fg-color--light, #6b6b6b);
  background: var(--md-default-bg-color--lighter, #f6f6f6);
  padding: 0.15rem 0.4rem;
  border-radius: 0.35rem;
}

.psexec-actions {
  display: flex;
  flex-wrap: wrap;
  gap: 0.5rem;
  align-items: center;
}

.important input {
  font-size: 1rem;
  font-weight: 600;
}

#preview {
  width: 100%;
  font-family: ui-monospace, SFMono-Regular, SFMono-Regular, Menlo, Consolas, "Liberation Mono", monospace;
}

.psexec-advanced {
  margin-top: 0.75rem;
}

.psexec-status {
  font-size: 0.85rem;
  color: var(--md-default-fg-color--light, #6b6b6b);
}
</style>
